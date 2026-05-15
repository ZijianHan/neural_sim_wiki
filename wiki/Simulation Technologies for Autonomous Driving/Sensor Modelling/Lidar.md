---
tags: [lidar, sensor-modelling]
last_compiled: 2026-05-14
---

# Lidar

## Definition
Lidar is an active ranging sensor that emits laser pulses and measures returns to produce sparse 3D geometric observations with intensity signals.

## Synthesis
### Current Source-Grounded Notes
From current sources (SplatAD), key lidar simulation concerns include:
- sparse and non-linear scan structure (often in spherical coordinates),
- intensity prediction/modeling,
- ray-drop (non-return) behavior,
- rolling-shutter-like temporal acquisition effects during scan sweeps.

### Role in AD Simulation
Lidar is a core modality in autonomous-driving stacks because it directly observes 3D scene geometry; realistic lidar rendering therefore affects downstream perception and planning validation.

### 中文版本
### 当前来源要点
基于当前来源（SplatAD），雷达仿真应重点建模：
- 稀疏且非线性的扫描结构（常见于球坐标表示），
- 强度（intensity），
- 掉点/无回波（ray drop），
- 扫描过程中的时间效应（rolling shutter 类现象）。

## Graph Connections
- [[Sensor Modelling]]
- [[Simulation Technologies for Autonomous Driving]]
- [[SplatAD]]

## Sources
- [[sources/papers/arXiv-2411.16816-SplatAD-Real-Time-Lidar-and-Camera-Rendering-with-3D-Gaussian-Splatting-for-AD]]
