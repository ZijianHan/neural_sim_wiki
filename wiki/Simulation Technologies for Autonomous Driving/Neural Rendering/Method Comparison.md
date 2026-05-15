---
tags: [comparison, neural-rendering, autonomous-driving]
last_compiled: 2026-05-14
sources:
  - "sources/papers/arXiv-2308.04079-3D-Gaussian-Splatting-for-Real-Time-Rendering-of-Radiance-Fields.md"
  - "sources/papers/arXiv-2411.16816-SplatAD-Real-Time-Lidar-and-Camera-Rendering-with-3D-Gaussian-Splatting-for-AD.md"
  - "sources/papers/arXiv-2412.12507-3DGUT-3D-Gaussian-Unscented-Transform.md"
  - "sources/papers/arXiv-2509.15130-Taming-Video-Models-for-3D-and-4D-Generation-via-Zero-Shot-Camera-Control.md"
  - "sources/papers/arXiv-2511.18290-SwiftVGGT-A-Scalable-Visual-Geometry-Grounded-Transformer-for-Large-Scale-Scenes.md"
  - "sources/papers/arXiv-2603.05959-OVGGT-O1-Constant-Cost-Streaming-Visual-Geometry-Transformer.md"
  - "sources/papers/arXiv-2512.03004-DGGT-Feedforward-4D-Reconstruction-of-Dynamic-Driving-Scenes-using-Unposed-Images.md"
  - "sources/papers/arXiv-2604.22339-Flow4DGS-SLAM-Optical-Flow-Guided-4D-Gaussian-Splatting-SLAM.md"
  - "sources/papers/3DGRT-2024-3D-Gaussian-Ray-Tracing-Fast-Tracing-of-Particle-Scenes.md"
  - "sources/projects/gsplat/gsplat.md"
  - "sources/projects/SplatAD/SplatAD.md"
  - "sources/projects/3DGRUT/3DGRUT.md"
  - "sources/projects/DGGT/DGGT.md"
---

# Method Comparison

## Definition
This page summarizes method-level tradeoffs across currently imported neural-rendering and reconstruction approaches in the wiki.

## Synthesis
### Quick Comparison (Current Sources)
| Method | Primary Goal | Sensor Scope | Core Strategy | Runtime Orientation |
|---|---|---|---|---|
| 3DGS | High-quality real-time NVS | Camera | Anisotropic Gaussian splatting + density control | Real-time rendering focus |
| 3DGUT | Extend 3DGS to nonlinear cameras/effects | Camera (with secondary-effect compatibility) | Unscented-Transform projection in rasterization | Keep raster speed with more flexibility |
| 3DGRT | Richer rendering effects | Camera (complex cameras, secondary rays) | Gaussian-particle ray tracing | Higher flexibility, more hardware/runtime sensitivity |
| SplatAD | AD simulation with camera+lidar | Camera + Lidar | Sensor-specific rasterization + rolling-shutter modeling | Practical real-time AD rendering |
| Flow4DGS-SLAM | Dynamic SLAM + reconstruction | Primarily camera-driven dynamic SLAM | Optical-flow-guided dynamic 4DGS + hybrid modeling | Efficient dynamic tracking/reconstruction |
| DGGT | Pose-free dynamic 4D reconstruction | Camera input (unposed) | Feedforward joint prediction (pose + Gaussian + dynamics) + diffusion refinement | Fast feedforward reconstruction |
| SwiftVGGT | Large-scene dense reconstruction acceleration | Camera | Training-free pipeline speedup (non-iterative Sim(3), no external VPR) | Strong runtime reduction for kilometer-scale scenes |
| OVGGT | Long-horizon streaming geometry reconstruction | Camera/video stream | Training-free fixed-budget caching (SSC) + anchor-preserving eviction (DAP) | Constant-VRAM, constant-cost streaming focus |
| WorldForge | Controlled 3D/4D generation from video priors | Camera/video generation | Inference-time control stack (IRR/FLF/DSG) | No retraining, plug-and-play control |
| gsplat | Engineering backend | Infrastructure | CUDA Gaussian rasterization library | Throughput/memory-oriented infrastructure |

### Reading Guide
- Choose **SplatAD** when camera+lidar simulation realism is the core objective.
- Choose **DGGT** when unposed-input feedforward reconstruction and cross-dataset transfer are prioritized.
- Choose **SwiftVGGT** when VGGT-style large-scene runtime is the primary bottleneck.
- Choose **OVGGT** when long-horizon streaming must run under fixed VRAM and stable throughput.
- Use **3DGUT/3DGRT** as complementary routes for camera-model flexibility vs tracing-based effects.

### 中文版本
### 对比结论（基于当前来源）
- **SplatAD**：适合“相机+雷达”联合仿真真实性任务。
- **DGGT**：适合“无位姿输入 + 前馈式动态重建”任务。
- **SwiftVGGT**：适合“VGGT 大规模场景重建提速”任务。
- **OVGGT**：适合“固定显存预算下的长序列流式重建”任务。
- **3DGUT/3DGRT**：分别代表“保持栅格效率的相机泛化”与“光追式高级效果”两条路线。
- **gsplat**：属于工程基础设施层，不是单一场景方法。

## Graph Connections
- [[Neural Rendering]]
- [[Gaussian Splatting]]
- [[SplatAD]]
- [[DGGT]]
- [[SwiftVGGT]]
- [[OVGGT]]
- [[WorldForge]]
- [[gsplat]]
