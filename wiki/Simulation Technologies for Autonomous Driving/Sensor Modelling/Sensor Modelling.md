---
tags: [template, sensor-modelling]
last_compiled: 2026-05-14
sources: ["sources/papers/arXiv-2411.16816-SplatAD-Real-Time-Lidar-and-Camera-Rendering-with-3D-Gaussian-Splatting-for-AD.md"]
---

# Sensor Modelling

## Definition
Sensor modelling describes how sensing hardware and signal formation are represented in simulation.

## Synthesis
### Scope
Use this page to organize modality-specific sensor models and fidelity assumptions.

### Current Source-Grounded Update
SplatAD adds evidence that practical sensor modeling for autonomous-driving simulation should include:
- modality-specific rasterization/measurement modeling (camera vs lidar),
- rolling shutter effects for both modalities,
- lidar-specific phenomena such as intensity and ray dropouts.

### 中文版本
当前来源显示，自动驾驶仿真的传感器建模应重点覆盖：
- 模态特化建模（相机与雷达的不同成像/测量机制），
- 双模态 rolling shutter，
- 雷达强度与掉点（ray dropout）等现象。

### Key Questions
- Which physical effects are modeled explicitly?
- What abstractions are used for efficiency?
- How is simulator-to-real transfer evaluated?

## Graph Connections
- [[Simulation Technologies for Autonomous Driving]]
- [[Radar]]
- [[Lidar]]
