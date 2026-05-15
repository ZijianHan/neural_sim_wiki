---
tags: [splatad, autonomous-driving, lidar, gaussian-splatting]
last_compiled: 2026-05-14
sources:
  - "sources/papers/arXiv-2411.16816-SplatAD-Real-Time-Lidar-and-Camera-Rendering-with-3D-Gaussian-Splatting-for-AD.md"
  - "sources/projects/SplatAD/SplatAD.md"
---

# SplatAD

## Definition
SplatAD is a 3DGS-based autonomous-driving method for real-time rendering of both camera and lidar in dynamic scenes.

## Synthesis
### Source-grounded Highlights
- Unified camera+lidar rendering from a 3D Gaussian scene representation.
- Sensor-specific modeling for lidar intensity and ray dropouts.
- Rolling-shutter modeling for both camera and lidar.
- Focus on practical speed for large-scale simulation.

### 中文版本
SplatAD 的核心价值在于将相机与雷达统一到同一 3DGS 表示中，并显式建模雷达与 rolling shutter 等传感器效应，以支持高效仿真。

## Graph Connections
- [[Gaussian Splatting]]
- [[Neural Rendering]]
- [[Lidar]]
- [[Sensor Modelling]]
