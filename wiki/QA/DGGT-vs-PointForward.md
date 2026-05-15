---
tags: [qa, dggt, pointforward, gaussian-splatting, feedforward]
last_compiled: 2026-05-15
topic: "DGGT vs PointForward"
sources:
  - "sources/papers/arXiv-2512.03004-DGGT-Feedforward-4D-Reconstruction-of-Dynamic-Driving-Scenes-using-Unposed-Images.md"
  - "sources/papers/arXiv-2605.11594-PointForward-Feedforward-Driving-Reconstruction-through-Point-Aligned-Representations.md"
---

# Q&A: DGGT vs PointForward

## Q1: DGGT 和 PointForward 的核心区别是什么？

**Answer:**

Both are feedforward Gaussian Splatting methods for dynamic driving-scene reconstruction from Xiaomi EV. PointForward is the successor that addresses two structural weaknesses of DGGT:

| Dimension | DGGT | PointForward |
|-----------|------|--------------|
| **Gaussian Prediction** | Per-pixel (pixel-aligned): each pixel independently predicts a Gaussian | Per-point (point-aligned): sparse 3D queries in world space aggregate multi-view features |
| **Multi-view Consistency** | Implicit — same physical point predicted independently per view → redundant Gaussians, layering artifacts | Explicit by design — single set of world-space queries fused across all views |
| **Dynamic Modeling** | Dense flow prediction via 2D dynamic masks + per-Gaussian motion vectors | Scene graph with 3D bounding boxes → canonical-space transforms for instance-level motion |
| **Pose Requirement** | Pose-free (predicts camera params as model output) | Requires known camera poses + 3D bounding box annotations |
| **Temporal Modeling** | Lifespan-based visibility modulation | Instance-level canonical-space motion propagation |
| **Refinement** | Diffusion-based rendered-image refinement | No diffusion refinement mentioned |
| **Dataset Coverage** | Waymo, nuScenes, Argoverse2 (cross-dataset) | Waymo, nuScenes (no Argoverse2) |

**Key tradeoff:** DGGT is more autonomous (no pose/bbox needed) but suffers from pixel-level inconsistency and fragmented motion. PointForward achieves higher reconstruction quality and multi-view coherence but requires structured inputs (calibrated cameras + 3D detection/tracking).

**Sources:** `sources/papers/arXiv-2512.03004-DGGT-...md`, `sources/papers/arXiv-2605.11594-PointForward-...md`

---

## Q2: 为什么 PointForward 能避免 DGGT 的分层伪影（layering artifacts）？

**Answer:**

DGGT 的像素对齐范式中，每个视图的每个像素独立预测一个高斯。同一物理 3D 点在不同视图中会被独立预测为略有偏差的多个高斯，导致冗余原语和分层效果（尤其在细粒度区域如电线杆、栏杆等）。

PointForward 改用世界空间中的稀疏 3D 查询点作为唯一的高斯锚点。这些查询投影到所有视图聚合特征后输出单一一组高斯——物理上同一点只有一个高斯表示，从设计层面消除了多视图不一致和冗余。

**Sources:** `sources/papers/arXiv-2605.11594-PointForward-...md` (Introduction, Section 3.1)

---

## Q3: 动态建模方面，两者的差异意味着什么？

**Answer:**

- **DGGT**: 用 2D 动态 mask 标注运动区域，然后对每个高斯预测稠密 3D 运动光流。问题是 2D mask 无法完整捕捉 3D 实例范围，且逐像素光流缺乏实例级一致性——同一车辆不同像素可能预测出不同运动矢量。

- **PointForward**: 引入场景图（scene graph）显式组织动态实例。利用 3D 边界框将属于同一实例的查询点变换到规范空间（canonical space），运动建模在实例级别执行。渲染时根据目标时间戳统一变换回世界坐标。这保证了同一物体的所有高斯共享一致的运动轨迹。

**代价**: PointForward 需要 3D 边界框标注（来自检测/跟踪模块），而 DGGT 完全自监督（无需外部标注）。

**Sources:** `sources/papers/arXiv-2512.03004-DGGT-...md`, `sources/papers/arXiv-2605.11594-PointForward-...md`

---

## Q4: 在实际部署中应该选择哪个？

**Answer:**

| Scenario | Recommendation |
|----------|---------------|
| 无标定位姿、无检测结果的原始视频 | DGGT（pose-free，不依赖外部标注） |
| 已有标定相机和 3D 检测/跟踪结果 | PointForward（更高质量、更一致） |
| 需要跨数据集零样本泛化 | DGGT（已验证 Waymo→nuScenes→Argoverse2） |
| 重视细粒度结构质量（电线杆、栏杆等） | PointForward（无分层伪影） |
| 需要实例级可控编辑/重放 | PointForward（场景图天然支持实例操作） |

**Note:** PointForward 代码尚未开源，当前仅 DGGT 可实际部署。

**Sources:** `sources/papers/arXiv-2512.03004-DGGT-...md`, `sources/papers/arXiv-2605.11594-PointForward-...md`

---

## Q5: 两者的性能对比如何？

**Answer:**

PointForward 论文中直接与 DGGT 对比（Waymo Open Dataset）：

| Metric | DGGT | PointForward |
|--------|------|--------------|
| Global PSNR | ~27.5 (estimated from PointForward paper context) | **28.48** |
| Dynamic PSNR | lower (PointForward claims improvement) | **25.01** |

PointForward 在定性对比中展示了 DGGT 出现明显分层伪影的区域，自身结果更为干净。

**Caveat:** 具体数值对比需以 PointForward 正式论文表格为准；DGGT 论文中自身报告的数据可能使用不同评测设置。

**Sources:** `sources/papers/arXiv-2605.11594-PointForward-...md` (abstract, Figure 1)
