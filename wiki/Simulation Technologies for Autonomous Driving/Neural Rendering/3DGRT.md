---
tags: [3dgrt, gaussian-splatting, neural-rendering]
last_compiled: 2026-05-14
sources: ["sources/papers/3DGRT-2024-3D-Gaussian-Ray-Tracing-Fast-Tracing-of-Particle-Scenes.md"]
---

# 3DGRT

## Definition
3DGRT is a Gaussian-particle ray-tracing approach that replaces pure splat rasterization to natively support complex camera effects and secondary rays.

## Synthesis
### Source-grounded Positioning
3DGRT emphasizes rendering flexibility (distortion, rolling shutter, reflections/refractions/shadows) via ray tracing, while aiming for practical performance on RT hardware.

### 中文版本
3DGRT 通过高斯粒子光追提升复杂相机与二次光照效果表达能力，但对硬件与效率权衡更敏感。

## Graph Connections
- [[3DGS]]
- [[3DGUT]]
- [[Gaussian Splatting]]
- [[gsplat]]
