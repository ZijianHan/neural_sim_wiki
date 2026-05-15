---
title: "Real2Sim: A Physics-driven and Editable Gaussian Splatting Framework for Autonomous Driving Scenes"
arxiv_id: 2605.13591v1
source: http://arxiv.org/abs/2605.13591v1
pdf: http://arxiv.org/pdf/2605.13591v1
date: 2026-05-13
Imported At: 2026-05-15
category: Gaussian Splatting
---

# Real2Sim: A Physics-driven and Editable Gaussian Splatting Framework for Autonomous Driving Scenes
## One-paragraph summary

Real2Sim combines 4D Gaussian Splatting (4DGS) with a differentiable Material Point Method (MPM) solver to create an editable, physics-aware simulation framework for autonomous driving. It explicitly reconstructs dynamic driving scenes as temporally continuous Gaussian primitives, supports instance-level editing, and simulates realistic object-object/object-environment interactions including collisions and post-impact trajectories. Validated on Waymo Open Dataset for rendering, reconstruction, editing, and physics simulation.

## Key ideas

- **4DGS + differentiable MPM**: unifies neural scene representation with physics-based simulation.
- **Instance-level editing**: supports scene manipulation at the object level.
- **Physics-aware interactions**: realistic collisions, post-impact trajectories, object-environment contact.
- **Corner case generation**: enables synthesis of rare scenarios (collisions) for downstream training.
- **Temporally continuous Gaussians**: smooth dynamic reconstruction without discrete timesteps.

## Novelty (What is new)

- First framework (per claims) combining 4DGS with differentiable MPM for AD scene simulation.
- Physics-aware Gaussian editing: not just appearance editing but physically plausible dynamics.
- Unified pipeline covering reconstruction → editing → physics simulation → data generation.

## Why it matters

Highly relevant to the wiki's core topic of simulation technologies for AD. Bridges the neural rendering ↔ physics simulation gap that most 3DGS methods ignore. Enables scalable corner-case generation for perception, tracking, prediction, and end-to-end policy learning.

## Reported limitations/open challenges (from framing)

- Physics fidelity of MPM approximation vs. full FEM not discussed.
- Scalability to very large scenes or many interacting objects unclear from abstract.
- Waymo-only validation.

## Source snippets used

- arXiv abstract 2605.13591v1

---

## 一段话总结

Real2Sim结合4D高斯泼溅和可微分MPM求解器，为自动驾驶创建可编辑、物理感知的仿真框架。支持实例级编辑和真实碰撞/碰后轨迹模拟，在Waymo数据集上验证了渲染、重建、编辑和物理仿真能力。

## 核心思想

- 4DGS + 可微MPM统一神经场景表征与物理仿真。
- 实例级编辑与物理感知交互（碰撞、接触）。
- 角落场景生成用于下游训练。

## 创新点（这篇论文新在哪里）

- 首个结合4DGS与可微MPM的AD场景仿真框架。
- 物理感知高斯编辑（非仅外观编辑）。

## 重要性

桥接神经渲染与物理仿真的鸿沟，为AD感知/预测/策略学习提供可扩展的角落场景数据生成。

## 论文框架中提到的局限/挑战

- MPM近似与完整FEM的物理保真度对比未讨论。
- 大规模场景/多物体交互的可扩展性不明确。

## 本页信息来源

- arXiv摘要 2605.13591v1
