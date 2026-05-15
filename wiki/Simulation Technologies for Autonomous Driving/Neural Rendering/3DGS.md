---
tags: [3dgs, gaussian-splatting, neural-rendering]
last_compiled: 2026-05-14
---

# 3DGS

## Definition
3DGS (3D Gaussian Splatting) is a real-time novel-view synthesis approach that represents scenes as optimized anisotropic 3D Gaussians.

## Synthesis
### Core Contributions (from source)
- Scene representation with anisotropic 3D Gaussians initialized from sparse calibration points.
- Interleaved optimization and density control during training.
- Visibility-aware splatting renderer for high-quality real-time 1080p rendering.

### Impact
3DGS established a quality-speed baseline that drove later variants and engineering libraries.

### 中文版本
3DGS 的关键贡献在于：各向异性高斯表示、优化与密度控制联动、以及可见性感知高效渲染，从而推动了高质量实时渲染路线。

## Graph Connections
- [[Gaussian Splatting]]
- [[Neural Rendering]]
- [[3DGUT]]
- [[3DGRT]]
- [[gsplat]]

## Sources
- [[sources/papers/arXiv-2308.04079-3D-Gaussian-Splatting-for-Real-Time-Rendering-of-Radiance-Fields]]
