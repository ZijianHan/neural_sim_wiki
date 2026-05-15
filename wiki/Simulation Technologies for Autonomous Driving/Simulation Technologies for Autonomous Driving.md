---
tags: [template, autonomous-driving, simulation]
last_compiled: 2026-05-15
---

# Simulation Technologies for Autonomous Driving

## Definition
Simulation technologies for autonomous driving are methods and systems used to model environments, sensors, and agent behavior for development and evaluation.

## Synthesis
### Scope
Use this page to summarize core simulation paradigms, system architectures, and evaluation goals.

### Current Evidence-backed Structure
Current imported sources cluster into:
1. **Rendering backbone methods** (3DGS family and variants).
2. **Sensor-realistic simulation methods** (e.g., camera+lidar rendering in AD scenes).
3. **Control-oriented generative methods** (trajectory-conditioned world-model generation).
4. **Feedforward reconstruction methods** (pose-free dynamic reconstruction from sparse unposed inputs).
5. **VGGT efficiency methods** (large-scale acceleration and fixed-budget long-horizon streaming).
6. **Panoramic feed-forward geometry methods** (joint panoramic pose/depth/point reconstruction and panoramic dataset-centric learning).

### 中文版本
当前已导入来源可归纳为三类：
1. **渲染底层方法**（3DGS 及其扩展）；
2. **传感器真实感仿真方法**（如相机+雷达联合渲染）；
3. **控制导向生成方法**（轨迹约束的 world model 生成）。
4. **前馈重建方法**（从稀疏 unposed 输入直接重建动态场景）。
5. **VGGT 效率方法**（覆盖大规模提速与固定预算长序列流式重建）。
6. **全景前馈几何方法**（面向全景图像的位姿/深度/点云联合建模与数据集驱动学习）。

### Key Questions
- What problem does this simulation stack solve?
- What assumptions and constraints define fidelity?
- How is performance measured?

### Notes
- Add source-grounded claims only.
- Track disagreements across sources with explicit labels.

## Graph Connections
- [[Neural Rendering]]
- [[Sensor Modelling]]
- [[World Model]]
- [[DGGT]]
- [[SwiftVGGT]]
- [[OVGGT]]
- [[PanoVGGT]]

## Sources
- [[sources/papers/arXiv-2411.16816-SplatAD-Real-Time-Lidar-and-Camera-Rendering-with-3D-Gaussian-Splatting-for-AD]]
- [[sources/papers/arXiv-2604.22339-Flow4DGS-SLAM-Optical-Flow-Guided-4D-Gaussian-Splatting-SLAM]]
- [[sources/papers/arXiv-2509.15130-Taming-Video-Models-for-3D-and-4D-Generation-via-Zero-Shot-Camera-Control]]
- [[sources/papers/arXiv-2308.04079-3D-Gaussian-Splatting-for-Real-Time-Rendering-of-Radiance-Fields]]
- [[sources/papers/arXiv-2512.03004-DGGT-Feedforward-4D-Reconstruction-of-Dynamic-Driving-Scenes-using-Unposed-Images]]
- [[sources/papers/arXiv-2511.18290-SwiftVGGT-A-Scalable-Visual-Geometry-Grounded-Transformer-for-Large-Scale-Scenes]]
- [[sources/papers/arXiv-2603.05959-OVGGT-O1-Constant-Cost-Streaming-Visual-Geometry-Transformer]]
- [[sources/papers/arXiv-2603.17571-PanoVGGT-Feed-Forward-3D-Reconstruction-from-Panoramic-Imagery]]
