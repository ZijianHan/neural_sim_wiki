---
tags: [3dgut, gaussian-splatting, neural-rendering]
last_compiled: 2026-05-14
sources: ["sources/papers/arXiv-2412.12507-3DGUT-3D-Gaussian-Unscented-Transform.md"]
---

# 3DGUT

## Definition
3DGUT extends Gaussian Splatting with Unscented-Transform-based projection to support nonlinear cameras and secondary-ray-compatible rendering workflows.

## Synthesis
### Source-grounded Positioning
3DGUT targets a key tradeoff: keep rasterization efficiency while improving support for distorted/time-dependent camera models and richer rendering effects.

### Novelty Snapshot
- UT sigma-point projection under arbitrary nonlinear camera functions.
- Better compatibility with tracing-style secondary effects within the same representation path.

### 中文版本
3DGUT 的定位是：在尽量保持光栅化速度的同时，增强非线性相机建模能力，并与二次光线渲染工作流对齐。

## Graph Connections
- [[3DGS]]
- [[Gaussian Splatting]]
- [[3DGRT]]
- [[gsplat]]
