---
tags: [template, gaussian-splatting, neural-rendering]
last_compiled: 2026-05-18
---

# Gaussian Splatting

## Definition
Gaussian Splatting is a scene representation and rendering approach that uses optimized 3D Gaussians for real-time or near-real-time synthesis.

## Synthesis
### Route Taxonomy from Current Sources
Current sources indicate six active Gaussian-Splatting routes:
1. **Foundational 3DGS route**: real-time high-quality rasterized novel-view synthesis.
2. **Projection/rendering generalization route**: 3DGUT (nonlinear cameras) and 3DGRT (ray-traced secondary effects).
3. **Automotive multimodal route**: SplatAD (camera + lidar rendering), Flow4DGS-SLAM (dynamic 4DGS + SLAM), DGGT (pose-free feedforward dynamic 4D reconstruction), PointForward (point-aligned feedforward with scene-graph dynamics), [[Real2Sim]] (4DGS + differentiable MPM physics for editable AD scenes).
4. **Infrastructure route**: gsplat as engineering backend for efficient Gaussian rasterization and extensions.
5. **Primitive generalization route**: [[3D Skew-Normal Splatting]] replaces symmetric Gaussians with Azzalini Skew-Normal distributions for better boundary/thin-structure modeling.
6. **Scalability route**: [[BlitzGS]] (city-scale distributed training in minutes via index-parity sharding), [[AV1-3DGS]] (codec motion vectors for 8× denser SfM init, 63% faster training).
7. **Hybrid pipeline route**: [[Hybrid Reconstruction Pipeline]] — combines feed-forward initialization (VGGT) + per-scene refinement + sensor heads for AD simulation.

### Practical Comparison Axes
- **Primary rendering mode**: rasterization vs ray tracing vs hybrid.
- **Sensor support**: camera-only vs camera+lidar.
- **Dynamics support**: static scenes vs dynamic actors/scenes.
- **Deployment focus**: research prototype vs reusable library.

### Notes
- Expand this page as additional Gaussian-Splatting variants are imported.

### 中文版本
### 当前来源下的路线划分
1. **3DGS 基础路线**：强调实时高质量新视角合成。
2. **投影/渲染泛化路线**：3DGUT（非线性相机）与 3DGRT（二次光线）。
3. **自动驾驶多模态路线**：SplatAD（相机+雷达）、Flow4DGS-SLAM（动态 4DGS + SLAM）、DGGT（无位姿前馈动态 4D 重建）、PointForward（点对齐前馈 + 场景图动态建模）与 [[Real2Sim]]（4DGS + 可微MPM物理仿真）。
4. **基础设施路线**：gsplat 作为高效渲染与扩展后端。
5. **原语泛化路线**：[[3D Skew-Normal Splatting]] 用偏正态分布替代对称高斯，改善边界建模。
6. **可扩展性路线**：[[BlitzGS]]（分布式城市尺度训练）、[[AV1-3DGS]]（编解码器运动向量加速SfM初始化）。

## Graph Connections
- [[Neural Rendering]]
- [[3DGS]]
- [[3DGUT]]
- [[3DGRT]]
- [[Flow4DGS-SLAM]]
- [[SplatAD]]
- [[DGGT]]
- [[PointForward]]
- [[gsplat]]
- [[Real2Sim]]
- [[BlitzGS]]
- [[3D Skew-Normal Splatting]]
- [[AV1-3DGS]]
- [[NeRF]]

## Sources
- [[sources/papers/arXiv-2308.04079-3D-Gaussian-Splatting-for-Real-Time-Rendering-of-Radiance-Fields]]
- [[sources/papers/arXiv-2412.12507-3DGUT-3D-Gaussian-Unscented-Transform]]
- [[sources/papers/3DGRT-2024-3D-Gaussian-Ray-Tracing-Fast-Tracing-of-Particle-Scenes]]
- [[sources/papers/arXiv-2604.22339-Flow4DGS-SLAM-Optical-Flow-Guided-4D-Gaussian-Splatting-SLAM]]
- [[sources/papers/arXiv-2411.16816-SplatAD-Real-Time-Lidar-and-Camera-Rendering-with-3D-Gaussian-Splatting-for-AD]]
- [[sources/papers/arXiv-2512.03004-DGGT-Feedforward-4D-Reconstruction-of-Dynamic-Driving-Scenes-using-Unposed-Images]]
- [[sources/papers/arXiv-2605.11594-PointForward-Feedforward-Driving-Reconstruction-through-Point-Aligned-Representations]]
- [[sources/papers/arXiv-2605.15010-3D-Skew-Normal-Splatting]]
- [[sources/papers/arXiv-2605.13794-BlitzGS-City-Scale-Gaussian-Splatting-at-Lightning-Speed]]
- [[sources/papers/arXiv-2605.14629-AV1-Motion-Vectors-for-Enhanced-Gaussian-Splatting]]
- [[sources/papers/arXiv-2605.13591-Real2Sim-Physics-driven-Editable-Gaussian-Splatting-for-AD]]
- [[sources/projects/gsplat/gsplat]]
- [[sources/projects/3DGRUT/3DGRUT]]
- [[sources/projects/SplatAD/SplatAD]]
- [[sources/projects/DGGT/DGGT]]
