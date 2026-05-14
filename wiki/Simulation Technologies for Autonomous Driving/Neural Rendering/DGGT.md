---
tags: [dggt, autonomous-driving, feedforward, 4d-reconstruction]
last_compiled: 2026-05-14
sources:
  - "sources/papers/arXiv-2512.03004-DGGT-Feedforward-4D-Reconstruction-of-Dynamic-Driving-Scenes-using-Unposed-Images.md"
  - "sources/projects/DGGT.md"
---

# DGGT

## Definition
DGGT is a pose-free feedforward framework for reconstructing dynamic driving scenes in 4D directly from unposed image sequences.

## Synthesis
### Source-grounded Highlights
- Reformulates camera pose as a model output instead of a required input.
- Predicts camera parameters, Gaussian scene representation, and dynamics in a single forward pass.
- Uses temporal lifespan and motion heads to improve dynamic consistency and interpolation.
- Applies diffusion-based refinement to reduce ghosting/disocclusion artifacts.
- Targets scalable reconstruction without per-scene optimization.

### 中文版本
DGGT 的核心在于“无位姿 + 前馈式”动态 4D 重建：从 unposed 图像直接输出位姿、场景高斯表示和动态信息，并通过 lifespan/motion 与 diffusion 细化提升时序一致性与渲染质量。

## Graph Connections
- [[Neural Rendering]]
- [[Gaussian Splatting]]
- [[Simulation Technologies for Autonomous Driving]]
- [[SplatAD]]
- [[Flow4DGS-SLAM]]
