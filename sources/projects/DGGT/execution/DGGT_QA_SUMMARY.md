# DGGT 算法理解 — Q&A 总结

**日期**: 2026-05-15  
**背景**: 在 RTX 2000 Ada (8GB) 上运行 DGGT 推理后的技术问答

---

## Q1: 为什么 VRAM 使用率和帧数挂钩？

### 核心原因：Vision Transformer 的注意力机制

**1. Token 数量线性增长**

每帧图像被切成 patches：`(518/14)² = 37² = 1,369 tokens/帧`

- 2 帧 → 2,738 tokens
- 4 帧 → 5,476 tokens

这些 token embeddings 全部驻留在 VRAM 中。

**2. Attention 内存二次增长（关键瓶颈）**

Aggregator 中的 Global Attention 对所有帧的 tokens 做跨帧注意力：

```
Attention Matrix 大小 = (S × N) × (S × N)
```

其中 S=帧数，N=1369。意味着：
- 2帧: 2738² ≈ 7.5M 元素
- 4帧: 5476² ≈ 30M 元素 → **4倍内存**

每一层 transformer 都要存储这个 attention map。

**3. 中间激活值（Activations）**

推理时 `torch.no_grad()` 不需要梯度，但每层的输出 tensor 仍然存在：

```
每层激活: [S × N, hidden_dim] = [S × 1369, 1024]
```

乘以 ~24 层 aggregator → 线性于 S。

**4. 预测头输出**

每帧输出独立的 3D Gaussian 参数（位置、颜色、不透明度、协方差）：
```
Gaussians per frame: N × (3+3+1+4+3) = 1369 × 14 参数
```

总输出也线性于 S。

**总结**：主要瓶颈是全局注意力的二次复杂度。帧数翻倍 → attention 内存 ×4，加上线性增长的 token/activation 存储，2→4 帧约多用 ~1-2 GB，刚好把 8GB 的边界推过。

---

## Q2: DGGT 的输入输出和基本算法逻辑是什么？

### 输入
- **S 帧 RGB 图像**（无需相机标定/位姿），来自行车摄像头的连续帧
- 分辨率统一为 518×518

### 输出
- **相机位姿**（intrinsics + extrinsics）— 自动预测，不需要外部标定
- **3D Gaussian 场景表示** — 每个像素对应一个 3D Gaussian（位置、颜色、不透明度、尺度、旋转）
- **动态物体运动** — 每帧每个 Gaussian 的运动向量
- **深度图** — 每帧的逐像素深度
- **渲染图像** — 通过 gsplat 光栅化重建的视角图像

### 算法流程

```
输入图像 [S, 3, 518, 518]
        │
        ▼
┌─────────────────────┐
│  DINOv2 ViT-L/14    │  冻结的预训练视觉编码器
│  提取 patch tokens   │  每帧 → 1369 个 token
└─────────────────────┘
        │
        ▼
┌─────────────────────┐
│  Aggregator          │  交替执行：
│  (24层 Transformer)  │  1. Frame Attention（帧内 token 交互）
│                      │  2. Global Attention（跨帧 token 交互）
│                      │  → 建立多帧之间的3D一致性
└─────────────────────┘
        │
        ▼
┌─────────────────────────────────────────┐
│  多头预测（并行）                         │
│                                          │
│  CameraHead  → 相机内外参 [S, 3, 4]     │
│  DPTHead     → 3D点云位置 [S, N, 3]     │
│  GaussianHead → 颜色/不透明度/尺度/旋转  │
│  TrackHead   → 动态运动向量              │
│  SkyGaussian → 天空背景建模              │
└─────────────────────────────────────────┘
        │
        ▼
┌─────────────────────┐
│  gsplat 渲染         │  将 3D Gaussians 光栅化到目标视角
│  静态 + 动态合成     │  → 生成渲染图像
└─────────────────────┘
        │
        ▼
  渲染图像 + 深度图 + 指标(PSNR/SSIM/LPIPS)
```

### 核心创新点

| 特点 | 传统方法 | DGGT |
|------|----------|------|
| 相机位姿 | 需要 SfM/标定 | 直接预测 (pose-free) |
| 3D重建 | 逐场景优化（几分钟~小时） | 单次前向传播（~2秒） |
| 动态物体 | 需额外跟踪模块 | 端到端联合预测 |
| 泛化性 | 每个场景重新训练 | Waymo训练 → 直接迁移到 nuScenes |

**一句话总结**：丢进去几帧行车图像，模型一次前向传播就输出完整的 4D 场景（3D结构 + 时间运动），可以从任意视角渲染新图像。

---

## Q3: 需要多少帧才能生成多大的场景？新视角渲染如何使用？

### 帧数 vs 场景规模

| 帧数 | 覆盖范围 | 典型场景 |
|------|----------|----------|
| 2 帧 | ~5-10m 行驶距离 | 局部重建（一个路口片段） |
| 4 帧 | ~15-20m | 标准评估设置（论文默认） |
| 8-16 帧 | ~50-100m | 较长路段重建 |

DGGT 论文主要用 **4帧** 做评估。帧间距通常 0.5-1秒（10Hz 采样后每隔几帧取一帧），覆盖一段短距离的行驶场景。

### 场景规模的限制因素

不是帧数越多场景越大那么简单：
- **更多帧 = 更多 3D Gaussians** → 场景细节更丰富
- **但每帧只看到前方视角**（单目前视摄像头）→ 侧面/后方永远是空白
- **遮挡问题** → 被前车挡住的区域无法重建
- 实际上 DGGT 重建的是 **驾驶走廊**（前方扇形区域），不是完整 360° 场景

### 新视角渲染

**当前 Mode 2（我们跑的）— 重建验证**：
```
输入: 帧0, 帧1
任务: 用帧0的信息重建帧1的图像
评估: 重建图像 vs 真实帧1 → PSNR/SSIM/LPIPS
```

验证模型的 3D 理解能力，但视角受限于输入帧的位置。

**真正的新视角渲染**：

```python
# 1. 模型前向传播 → 得到 3D Gaussians
predictions = model(images)

# 2. 指定新相机位姿（你想从哪里看）
novel_camera = custom_extrinsics  # 自定义 4×4 矩阵

# 3. gsplat 光栅化渲染
rendered = rasterize(gaussians, novel_camera, intrinsics)
```

**可以做的事情**：

| 应用 | 操作 |
|------|------|
| 平移视角 | 相机左移/右移几米 |
| 升高俯视 | 相机抬高做鸟瞰 |
| 时间插值 | Mode 3: 在帧0和帧1之间插入新时刻 |
| 跟车视角变换 | 模拟从旁边车道观看 |

**不能做的事情**：
- 转 180° 看后面（模型从未见过后方信息）
- 看到被完全遮挡的区域
- 大幅偏离原始轨迹（>5-10m）→ 质量急剧下降

### 实际应用价值

DGGT 的价值不在于「生成一个很大的场景然后随意漫游」，而在于：

1. **自动驾驶仿真** — 微调驾驶轨迹，看「如果车稍微偏左会看到什么」
2. **数据增强** — 从有限的行车记录生成更多训练视角
3. **场景理解验证** — 证明模型真正理解了 3D 结构（而非记忆 2D 像素）

### 当前硬件（RTX 2000 Ada 8GB）的实际能力

- 2帧 → 重建一个 ~5m 的路段片段
- 能验证模型的 3D 重建质量
- 新视角渲染范围：原始轨迹附近的小范围偏移
- 如果需要更大场景，需要 16+ GB GPU 跑 4-8 帧

---

## Q4: 当前硬件上最多能跑多少帧？

**结论：2帧是 518×518 分辨率下的上限。**

| 帧数 | 分辨率 | 峰值 VRAM | 状态 |
|------|--------|-----------|------|
| 2 | 518×518 | ~6.4 GB | ✅ 可用 |
| 3 | 518×518 | ~7.5+ GB | ⚠️ 大概率 OOM |
| 4 | 518×518 | ~7.2+ GB | ❌ 已确认 OOM |

理论上可以通过降低分辨率（如 490 或 476，必须整除14）来塞入第3帧，但未实测。

FP16 不可用（模型内部强制关闭 autocast），所以无法通过半精度省内存。

---

## Q5: 如何从偏移轨迹渲染新视角？

### 原理

模型前向传播得到 3D Gaussians 后，渲染过程仅依赖相机外参（extrinsic matrix）。
修改 extrinsic 中的平移分量即可从新位置渲染：

```python
def offset_extrinsic(extrinsic, dx=0.0, dy=0.0, dz=0.0):
    """对 world-to-camera 矩阵施加世界坐标系偏移"""
    offset = torch.tensor([dx, dy, dz], device=extrinsic.device, dtype=extrinsic.dtype)
    R = extrinsic[:3, :3]
    new_ext = extrinsic.clone()
    new_ext[:3, 3] = extrinsic[:3, 3] + R @ offset  # 世界偏移转换到相机坐标
    return new_ext
```

### 使用方法

> ⚠️ **重要发现：DGGT 使用归一化坐标，不是米！**  
> 帧间平移 ≈ 0.05 单位，点云范围 [-1.4, 12.5]。  
> 偏移量使用 0.01–0.05 为合理范围（≈ 现实世界 0.5–2m）。  
> 大偏移量（≥0.5）会导致场景消失（alpha→0）。

```bash
python render_novel_views.py \
  --image_dir data/nuscenes/processed_10Hz/mini \
  --scene_names 000 \
  --sequence_length 2 \
  --ckpt_path pretrained/model_latest_waymo.pth \
  --output_path results/novel_views \
  --lateral_offsets -0.05 -0.03 -0.01 0.01 0.03 0.05 \
  --save_video
```

### 输出结构
```
results/novel_views/000/
├── frame0_original.png       # 原始视角参考
├── frame1_original.png
├── lateral_+0.000/frame*.png # 零偏移（与原图完全一致）
├── lateral_+0.010/frame*.png # 微小右移
├── lateral_-0.010/frame*.png # 微小左移
├── lateral_+0.050/frame*.png # 明显右移
└── offset_sweep.mp4          # 各偏移的frame0合集视频
```

### 支持的偏移类型（归一化场景坐标）
| 参数 | 含义 | 推荐范围 |
|------|------|----------|
| `--lateral_offsets` | 左右平移 (正=右) | ±0.01–0.05 |
| `--vertical_offsets` | 上下平移 (负=升高) | -0.01 to -0.05 |
| `--longitudinal_offsets` | 前后平移 (正=前) | ±0.01–0.05 |

### 质量验证结果（scene 000, lateral offsets）
| 偏移量 | 平均差异 | 中位差异 | >20 像素占比 |
|--------|----------|----------|-------------|
| 0.000 | 0.00 | 0.0 | 0.0% |
| ±0.010 | ~10.4 | 3.0 | 14.3% |
| ±0.020 | ~15.2 | 4.0 | 21.1% |
| ±0.050 | ~27.2 | 10.5 | 35.8% |

### 注意事项
- 天空背景只需预计算一次（小偏移时差异 <0.001）
- 多次 rasterization 调用不额外消耗模型显存（Gaussians 已固定）
- gsplat 要求所有输入为 float32（extrinsic decode 可能返回 float64）
- 不支持大角度旋转（模型从未见过后方场景）
- 偏移量 ≥0.1 后质量急剧下降（Gaussians 覆盖范围有限）

---

## 我们的实际推理结果

**运行命令**：
```bash
python inference.py \
  --image_dir data/nuscenes/processed_10Hz/mini \
  --scene_names 000 001 \
  --mode 2 --sequence_length 2 \
  --ckpt_path pretrained/model_latest_waymo.pth \
  --output_path results/nuscenes_mode2 \
  -images -metrics -depth
```

**指标**（Waymo→nuScenes 跨数据集迁移）：
| 指标 | 值 | 说明 |
|------|-----|------|
| PSNR | 11.43 dB | 跨数据集偏低是正常的 |
| SSIM | 0.706 | 结构相似度尚可 |
| LPIPS | 0.375 | 感知距离（越低越好） |
| 推理时间 | ~1.7 s/场景 | FP32, RTX 2000 Ada |

**输出文件**：
```
results/nuscenes_mode2/{scene}/
├── comparison.mp4      # GT vs 预测 对比视频
├── rendered_video.mp4  # 渲染的新视角视频
├── view_*.png          # 渲染帧图片
└── view_*.npy          # 深度图
```
