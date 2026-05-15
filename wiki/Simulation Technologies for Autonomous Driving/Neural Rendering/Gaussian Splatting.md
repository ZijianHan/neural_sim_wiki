---
tags: [template, gaussian-splatting, neural-rendering]
last_compiled: 2026-05-15
sources:
  - "sources/papers/arXiv-2308.04079-3D-Gaussian-Splatting-for-Real-Time-Rendering-of-Radiance-Fields.md"
  - "sources/papers/arXiv-2412.12507-3DGUT-3D-Gaussian-Unscented-Transform.md"
  - "sources/papers/3DGRT-2024-3D-Gaussian-Ray-Tracing-Fast-Tracing-of-Particle-Scenes.md"
  - "sources/papers/arXiv-2604.22339-Flow4DGS-SLAM-Optical-Flow-Guided-4D-Gaussian-Splatting-SLAM.md"
  - "sources/papers/arXiv-2411.16816-SplatAD-Real-Time-Lidar-and-Camera-Rendering-with-3D-Gaussian-Splatting-for-AD.md"
  - "sources/papers/arXiv-2512.03004-DGGT-Feedforward-4D-Reconstruction-of-Dynamic-Driving-Scenes-using-Unposed-Images.md"
  - "sources/papers/arXiv-2605.11594-PointForward-Feedforward-Driving-Reconstruction-through-Point-Aligned-Representations.md"
  - "sources/projects/gsplat/gsplat.md"
  - "sources/projects/3DGRUT/3DGRUT.md"
  - "sources/projects/SplatAD/SplatAD.md"
  - "sources/projects/DGGT/DGGT.md"
---

# Gaussian Splatting

## Definition
Gaussian Splatting is a scene representation and rendering approach that uses optimized 3D Gaussians for real-time or near-real-time synthesis.

## Synthesis
### Route Taxonomy from Current Sources
Current sources indicate four active Gaussian-Splatting routes:
1. **Foundational 3DGS route**: real-time high-quality rasterized novel-view synthesis.
2. **Projection/rendering generalization route**: 3DGUT (nonlinear cameras) and 3DGRT (ray-traced secondary effects).
3. **Automotive multimodal route**: SplatAD (camera + lidar rendering), Flow4DGS-SLAM (dynamic 4DGS + SLAM), DGGT (pose-free feedforward dynamic 4D reconstruction), PointForward (point-aligned feedforward with scene-graph dynamics).
4. **Infrastructure route**: gsplat as engineering backend for efficient Gaussian rasterization and extensions.

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
3. **自动驾驶多模态路线**：SplatAD（相机+雷达）、Flow4DGS-SLAM（动态 4DGS + SLAM）、DGGT（无位姿前馈动态 4D 重建）与 PointForward（点对齐前馈 + 场景图动态建模）。
4. **基础设施路线**：gsplat 作为高效渲染与扩展后端。

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
- [[NeRF]]
