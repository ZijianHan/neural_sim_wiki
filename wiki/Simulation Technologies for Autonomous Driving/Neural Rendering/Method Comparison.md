---
tags: [comparison, neural-rendering, autonomous-driving]
last_compiled: 2026-05-15
---

# Method Comparison

## Definition
This page summarizes method-level tradeoffs across currently imported neural-rendering and reconstruction approaches in the wiki.

## Synthesis
### Quick Comparison (Current Sources)

#### Reconstruction & Rendering Methods
| Method | Primary Goal | Sensor Scope | Core Strategy | Runtime Orientation |
|---|---|---|---|---|
| 3DGS | High-quality real-time NVS | Camera | Anisotropic Gaussian splatting + density control | Real-time rendering focus |
| 3D Skew-Normal Splatting | Better boundary/thin-structure NVS | Camera | Azzalini Skew-Normal primitives with learnable skewness | Drop-in 3DGS replacement, same pipeline |
| 3DGUT | Extend 3DGS to nonlinear cameras/effects | Camera (with secondary-effect compatibility) | Unscented-Transform projection in rasterization | Keep raster speed with more flexibility |
| 3DGRT | Richer rendering effects | Camera (complex cameras, secondary rays) | Gaussian-particle ray tracing | Higher flexibility, more hardware/runtime sensitivity |
| SplatAD | AD simulation with camera+lidar | Camera + Lidar | Sensor-specific rasterization + rolling-shutter modeling | Practical real-time AD rendering |
| Real2Sim | Physics-aware editable AD scenes | Camera + Physics | 4DGS + differentiable MPM solver | Reconstruction + editing + physics sim |
| BlitzGS | City-scale fast reconstruction | Camera (multi-GPU) | Index-parity sharding + importance scoring + LOD gate | Order-of-magnitude training speedup |
| AV1-3DGS | Faster/denser SfM initialization | Camera (video input) | AV1 codec motion vectors for feature matching | 8× denser init, 63% less training time |
| Flow4DGS-SLAM | Dynamic SLAM + reconstruction | Primarily camera-driven dynamic SLAM | Optical-flow-guided dynamic 4DGS + hybrid modeling | Efficient dynamic tracking/reconstruction |

#### Feed-Forward Reconstruction Methods
| Method | Primary Goal | Sensor Scope | Core Strategy | Runtime Orientation |
|---|---|---|---|---|
| VGGT-Ω | Scaled feed-forward static+dynamic recon | Camera (multi-view) | Register attention + single dense head + self-supervised scaling | 30% memory of VGGT, 15× more data, SOTA quality |
| TurboVGGT | Fast multi-view reconstruction | Camera (multi-view) | Adaptive sparse global attention + frame attention | Fast inference with competitive quality |
| SwiftVGGT | Large-scene dense reconstruction acceleration | Camera | Training-free pipeline speedup (non-iterative Sim(3), no external VPR) | Strong runtime reduction for kilometer-scale scenes |
| OVGGT | Long-horizon streaming geometry reconstruction | Camera/video stream | Training-free fixed-budget caching (SSC) + anchor-preserving eviction (DAP) | Constant-VRAM, constant-cost streaming focus |
| PanoVGGT | Panoramic feed-forward reconstruction | Panoramic camera | Permutation-equivariant transformer + spherical-aware embedding + SO(3) augmentation | Strong panoramic geometric robustness |
| DGGT | Pose-free dynamic 4D reconstruction | Camera input (unposed) | Feedforward joint prediction (pose + Gaussian + dynamics) + diffusion refinement | Fast feedforward reconstruction |
| PointForward | Point-aligned feedforward driving reconstruction | Camera input (with 3D bbox) | Sparse world-space queries + spatial-temporal fusion + scene-graph dynamics | SOTA feedforward quality, multi-view consistent |

#### World Models & Simulation
| Method | Primary Goal | Input/Output | Core Strategy | Key Differentiator |
|---|---|---|---|---|
| WorldForge | Controlled 3D/4D generation from video priors | Camera → video | Inference-time control stack (IRR/FLF/DSG) | Training-free, plug-and-play control |
| SANA-WM | Efficient minute-scale video world model | Camera (6-DoF) → 720p 60s video | Hybrid linear attention (GDN+softmax) + dual-branch camera control | 2.6B params, single consumer GPU deployable, 36× throughput |
| EponaV2 | Perception-free driving planning | Camera → trajectory | Future geometry+semantic map prediction + flow matching policy opt | No perception annotations needed, SOTA on NAVSIM |
| Coding Agent WS | Physics-accurate world simulation | NL prompt → executable sim code | Multi-agent (plan→code→review→physics) iterative refinement | Explicit physics constraints, outperforms video models |
| PDI-Bench | Geometric evaluation of video world models | Generated video → residuals | Segmentation + tracking + monocular 3D lift → projective residuals | Reveals failures invisible to FID/FVD |

#### Infrastructure
| Method | Primary Goal | Core Strategy | Runtime Orientation |
|---|---|---|---|
| gsplat | Engineering backend | CUDA Gaussian rasterization library | Throughput/memory-oriented infrastructure |

### Reading Guide

#### For Reconstruction & Rendering
- Choose **SplatAD** when camera+lidar simulation realism is the core objective.
- Choose **Real2Sim** when physics-aware editing and corner-case generation (collisions, interactions) are needed.
- Choose **BlitzGS** when city-scale training speed is the primary bottleneck (multi-GPU available).
- Choose **AV1-3DGS** as a preprocessing upgrade when input is video-encoded — denser init improves any downstream 3DGS method.
- Choose **3D Skew-Normal Splatting** when sharp boundaries and thin structures are critical under limited primitive budgets.

#### For Feed-Forward Reconstruction
- Choose **VGGT-Ω** for best overall quality with scaling (static + dynamic), especially when compute budget allows large models.
- Choose **TurboVGGT** for faster inference with adaptive efficiency (learned sparsity).
- Choose **SwiftVGGT** when VGGT-style large-scene runtime is the primary bottleneck (training-free).
- Choose **OVGGT** when long-horizon streaming must run under fixed VRAM and stable throughput.
- Choose **PanoVGGT** when input is panoramic (360-degree equirectangular).
- Choose **DGGT** when unposed-input feedforward reconstruction and cross-dataset transfer are prioritized.
- Choose **PointForward** when multi-view consistency and instance-level dynamic quality matter most (requires 3D bbox).

#### For World Models & Simulation
- Choose **SANA-WM** for efficient open-source controllable long-video generation (60s, 720p, 6-DoF).
- Choose **WorldForge** for training-free plug-and-play camera control on existing video diffusion models.
- Choose **EponaV2** for perception-free driving planning without annotation cost.
- Choose **Coding Agent WS** when physical accuracy and explicit constraints matter more than neural visual realism.
- Use **PDI-Bench** to evaluate geometric consistency of any video world model.

### 中文版本
### 对比结论（基于当前来源）

#### 重建与渲染
- **SplatAD**：适合"相机+雷达"联合仿真真实性任务。
- **Real2Sim**：适合需要物理感知编辑和角落场景生成（碰撞、交互）的任务。
- **BlitzGS**：适合城市尺度快速训练（需多GPU）。
- **AV1-3DGS**：视频编码输入的预处理升级——更密集初始化可改善任何下游3DGS方法。
- **3D Skew-Normal Splatting**：适合有限原语预算下对锐利边界和薄结构有要求的场景。
- **3DGUT/3DGRT**：分别代表"保持栅格效率的相机泛化"与"光追式高级效果"两条路线。

#### 前馈重建
- **VGGT-Ω**：最佳整体质量+缩放能力（静态+动态）。
- **TurboVGGT**：自适应稀疏注意力实现更快推理。
- **SwiftVGGT**：适合"VGGT 大规模场景重建提速"任务（无需训练）。
- **OVGGT**：适合"固定显存预算下的长序列流式重建"任务。
- **PanoVGGT**：适合"全景输入下的前馈式重建"任务。
- **DGGT**：适合"无位姿输入 + 前馈式动态重建"任务。
- **PointForward**：适合"多视角一致性 + 实例级动态质量"任务（需3D bbox）。

#### 世界模型与仿真
- **SANA-WM**：高效开源可控长视频生成（60秒、720p、6-DoF）。
- **WorldForge**：无训练即插即用相机控制。
- **EponaV2**：无感知标注的驾驶规划。
- **Coding Agent WS**：物理准确性优先于视觉真实感时选择。
- **PDI-Bench**：评估任何视频世界模型的几何一致性。

#### 基础设施
- **gsplat**：属于工程基础设施层，不是单一场景方法。

## Graph Connections
- [[Neural Rendering]]
- [[Gaussian Splatting]]
- [[SplatAD]]
- [[DGGT]]
- [[PointForward]]
- [[SwiftVGGT]]
- [[OVGGT]]
- [[PanoVGGT]]
- [[VGGT-Omega]]
- [[TurboVGGT]]
- [[Real2Sim]]
- [[BlitzGS]]
- [[3D Skew-Normal Splatting]]
- [[AV1-3DGS]]
- [[WorldForge]]
- [[SANA-WM]]
- [[EponaV2]]
- [[Coding Agent as World Simulator]]
- [[PDI-Bench]]
- [[gsplat]]

## Sources
- [[sources/papers/arXiv-2308.04079-3D-Gaussian-Splatting-for-Real-Time-Rendering-of-Radiance-Fields]]
- [[sources/papers/arXiv-2411.16816-SplatAD-Real-Time-Lidar-and-Camera-Rendering-with-3D-Gaussian-Splatting-for-AD]]
- [[sources/papers/arXiv-2412.12507-3DGUT-3D-Gaussian-Unscented-Transform]]
- [[sources/papers/arXiv-2509.15130-Taming-Video-Models-for-3D-and-4D-Generation-via-Zero-Shot-Camera-Control]]
- [[sources/papers/arXiv-2511.18290-SwiftVGGT-A-Scalable-Visual-Geometry-Grounded-Transformer-for-Large-Scale-Scenes]]
- [[sources/papers/arXiv-2603.05959-OVGGT-O1-Constant-Cost-Streaming-Visual-Geometry-Transformer]]
- [[sources/papers/arXiv-2512.03004-DGGT-Feedforward-4D-Reconstruction-of-Dynamic-Driving-Scenes-using-Unposed-Images]]
- [[sources/papers/arXiv-2605.11594-PointForward-Feedforward-Driving-Reconstruction-through-Point-Aligned-Representations]]
- [[sources/papers/arXiv-2604.22339-Flow4DGS-SLAM-Optical-Flow-Guided-4D-Gaussian-Splatting-SLAM]]
- [[sources/papers/arXiv-2603.17571-PanoVGGT-Feed-Forward-3D-Reconstruction-from-Panoramic-Imagery]]
- [[sources/papers/3DGRT-2024-3D-Gaussian-Ray-Tracing-Fast-Tracing-of-Particle-Scenes]]
- [[sources/papers/arXiv-2605.15195-VGGT-Omega-Scaling-Feed-Forward-Reconstruction]]
- [[sources/papers/arXiv-2605.14315-TurboVGGT-Fast-Visual-Geometry-Reconstruction-with-Adaptive-Alternating-Attention]]
- [[sources/papers/arXiv-2605.14696-EponaV2-Driving-World-Model-with-Comprehensive-Future-Reasoning]]
- [[sources/papers/arXiv-2605.13591-Real2Sim-Physics-driven-Editable-Gaussian-Splatting-for-AD]]
- [[sources/papers/arXiv-2605.15178-SANA-WM-Efficient-Minute-Scale-World-Modeling]]
- [[sources/papers/arXiv-2605.14398-Coding-Agent-Is-Good-As-World-Simulator]]
- [[sources/papers/arXiv-2605.13794-BlitzGS-City-Scale-Gaussian-Splatting-at-Lightning-Speed]]
- [[sources/papers/arXiv-2605.14629-AV1-Motion-Vectors-for-Enhanced-Gaussian-Splatting]]
- [[sources/papers/arXiv-2605.15010-3D-Skew-Normal-Splatting]]
- [[sources/papers/arXiv-2605.15185-PDI-Bench-Geometric-Consistency-Evaluation-for-Video-World-Models]]
- [[sources/projects/gsplat/gsplat]]
- [[sources/projects/SplatAD/SplatAD]]
- [[sources/projects/3DGRUT/3DGRUT]]
- [[sources/projects/DGGT/DGGT]]
- [[sources/projects/PanoVGGT/PanoVGGT]]
