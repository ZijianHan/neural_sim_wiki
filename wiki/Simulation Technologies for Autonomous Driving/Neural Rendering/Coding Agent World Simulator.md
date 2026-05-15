---
tags: [world-model, physics-simulation, code-generation, multi-agent]
last_compiled: 2026-05-15
---

# Coding Agent as World Simulator

## Definition
An agentic framework that constructs physics-based world models through executable simulation code, coordinating planning, code generation, visual review, and physics analysis agents.

## Synthesis
### Source-grounded Highlights
- **Code-as-world-model**: physics simulation via executable code rather than learned latent dynamics.
- **Multi-agent pipeline**: planning agent → code generation agent → visual review agent → physics analysis agent, iteratively refined.
- **Explicit physics constraints**: unlike video models that infer dynamics in latent space, this ensures constraints are satisfied by construction.
- Outperforms video-based world models (including advanced generators) on physical accuracy, instruction fidelity, and visual quality.
- Applicable to driving simulation and embodied robot tasks.

### Paradigm Contrast
| Aspect | Video World Models | Code World Models |
|---|---|---|
| Physics | Implicit (latent) | Explicit (constraints) |
| Visual realism | High (learned) | Depends on renderer |
| Physical accuracy | Often fails (see [[PDI-Bench]]) | Strong by construction |
| Generalization | Novel visual scenarios | Requires simulation primitives |

### 中文版本
通过可执行仿真代码构建物理世界模型的Agent框架。多Agent协调（规划→代码→视觉审查→物理分析），迭代修正直至满足物理约束。在物理准确性上超越视频模型，可应用于驾驶仿真和具身机器人。

## Graph Connections
- [[World Model]]
- [[PDI-Bench]]
- [[Simulation Technologies for Autonomous Driving]]
- [[Method Comparison]]

## Sources
- [[sources/papers/arXiv-2605.14398-Coding-Agent-Is-Good-As-World-Simulator]]
