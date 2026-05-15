---
tags: [4dgs, physics-simulation, autonomous-driving, editable]
last_compiled: 2026-05-15
---

# Real2Sim

## Definition
Real2Sim is a unified framework combining 4D Gaussian Splatting with a differentiable Material Point Method (MPM) solver for physics-aware, editable driving scene simulation.

## Synthesis
### Source-grounded Highlights
- **4DGS + differentiable MPM**: unifies neural scene representation with physics-based simulation in a single framework.
- **Instance-level editing**: supports manipulation of individual objects within reconstructed scenes.
- **Physics-aware interactions**: realistic collisions, post-impact trajectories, object-environment contact.
- **Corner case generation**: enables synthesis of rare scenarios (collisions, near-misses) for downstream training.
- **Temporally continuous Gaussians**: smooth dynamic reconstruction without discrete timesteps.
- Validated on Waymo Open Dataset across rendering, reconstruction, editing, and physics simulation.

### Unique Position
Most 3DGS methods focus on visual fidelity alone. Real2Sim bridges the neural rendering ↔ physics simulation gap, enabling not just novel views but physically plausible scene edits.

### 中文版本
Real2Sim 结合4D高斯泼溅和可微分MPM求解器，为自动驾驶创建可编辑、物理感知的仿真框架。支持实例级编辑和真实碰撞模拟，在Waymo数据集上验证。桥接了神经渲染与物理仿真的鸿沟。

## Graph Connections
- [[Gaussian Splatting]]
- [[SplatAD]]
- [[Flow4DGS-SLAM]]
- [[Simulation Technologies for Autonomous Driving]]
- [[Method Comparison]]

## Sources
- [[sources/papers/arXiv-2605.13591-Real2Sim-Physics-driven-Editable-Gaussian-Splatting-for-AD]]
