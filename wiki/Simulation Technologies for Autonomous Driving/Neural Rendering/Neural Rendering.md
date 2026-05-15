---
tags: [template, neural-rendering]
last_compiled: 2026-05-15
---

# Neural Rendering

## Definition
Neural rendering uses learned scene representations to synthesize images or sensor-like outputs from novel viewpoints or conditions.

## Synthesis
### Scope
Document neural rendering methods relevant to autonomous-driving simulation.

### Current Source-Grounded Update
Current sources indicate six practical branches in this wiki:
1. **Foundational Gaussian rendering branch**: 3DGS -> 3DGUT/3DGRT -> gsplat ecosystem.
2. **Automotive multimodal rendering branch**: SplatAD and Flow4DGS-SLAM (camera/lidar or dynamic SLAM focus).
3. **Generative control branch**: WorldForge for trajectory-controlled 3D/4D synthesis from video diffusion priors.
4. **Pose-free feedforward reconstruction branch**: DGGT for dynamic 4D reconstruction directly from unposed images; PointForward for point-aligned (non-pixel) feedforward reconstruction with scene-graph dynamics.
5. **VGGT efficiency branch**: SwiftVGGT (large-scene acceleration) and OVGGT (constant-cost streaming for long videos).
6. **Panoramic geometry branch**: PanoVGGT for feed-forward pose/depth/point reconstruction directly from unordered panoramas with spherical-aware modeling.

### Branch Notes (Evidence-based)
- **3DGS/3DGUT/3DGRT**: address speed-quality-camera-model/secondary-ray tradeoffs in Gaussian rendering.
- **SplatAD/Flow4DGS-SLAM**: target autonomous-driving dynamic scenes with stronger sensor and motion modeling.
- **WorldForge**: inference-time control stack for path-following 3D/4D generation without retraining.
- **DGGT/PointForward**: pose-free feedforward dynamic reconstruction; PointForward advances DGGT by replacing pixel-aligned prediction with point-aligned queries and scene-graph dynamics.
- **SwiftVGGT/OVGGT**: complementary VGGT-oriented efficiency routes for large-scene acceleration vs fixed-budget streaming.
- **PanoVGGT**: extends feed-forward visual geometry reconstruction to panoramic equirectangular inputs and introduces PanoCity for large-scale panoramic supervision.

### 中文版本
当前来源显示，神经渲染在本 wiki 中可归纳为六条主线：
1. **高斯渲染基础主线**：3DGS -> 3DGUT/3DGRT -> gsplat 工程生态。
2. **自动驾驶多模态主线**：SplatAD 与 Flow4DGS-SLAM（面向相机/雷达或动态 SLAM）。
3. **生成式控制主线**：WorldForge（基于视频扩散先验的轨迹可控 3D/4D 生成）。
4. **无位姿前馈重建主线**：DGGT（直接从 unposed 图像重建动态 4D 场景）；PointForward（用点对齐查询 + 场景图替代像素对齐预测）。
5. **VGGT 效率主线**：SwiftVGGT（大场景提速）与 OVGGT（长序列常数资源流式重建）。
6. **全景几何主线**：PanoVGGT（面向无序全景输入的前馈位姿/深度/点云联合重建）。

### Key Questions
- Which representation is used (implicit field, explicit primitives, hybrid)?
- What are the quality-speed-memory tradeoffs?
- How well does it generalize across scenes and dynamics?

### Notes
- Keep method comparisons evidence-based.

## Graph Connections
- [[Simulation Technologies for Autonomous Driving]]
- [[NeRF]]
- [[Gaussian Splatting]]
- [[World Model]]
- [[WorldForge]]
- [[3DGS]]
- [[3DGUT]]
- [[3DGRT]]
- [[Flow4DGS-SLAM]]
- [[gsplat]]
- [[SplatAD]]
- [[DGGT]]
- [[PointForward]]
- [[SwiftVGGT]]
- [[OVGGT]]
- [[PanoVGGT]]
- [[Method Comparison]]

## Sources
- [[sources/papers/arXiv-2308.04079-3D-Gaussian-Splatting-for-Real-Time-Rendering-of-Radiance-Fields]]
- [[sources/papers/arXiv-2412.12507-3DGUT-3D-Gaussian-Unscented-Transform]]
- [[sources/papers/3DGRT-2024-3D-Gaussian-Ray-Tracing-Fast-Tracing-of-Particle-Scenes]]
- [[sources/papers/arXiv-2509.15130-Taming-Video-Models-for-3D-and-4D-Generation-via-Zero-Shot-Camera-Control]]
- [[sources/papers/arXiv-2604.22339-Flow4DGS-SLAM-Optical-Flow-Guided-4D-Gaussian-Splatting-SLAM]]
- [[sources/papers/arXiv-2411.16816-SplatAD-Real-Time-Lidar-and-Camera-Rendering-with-3D-Gaussian-Splatting-for-AD]]
- [[sources/papers/arXiv-2512.03004-DGGT-Feedforward-4D-Reconstruction-of-Dynamic-Driving-Scenes-using-Unposed-Images]]
- [[sources/papers/arXiv-2511.18290-SwiftVGGT-A-Scalable-Visual-Geometry-Grounded-Transformer-for-Large-Scale-Scenes]]
- [[sources/papers/arXiv-2603.05959-OVGGT-O1-Constant-Cost-Streaming-Visual-Geometry-Transformer]]
- [[sources/papers/arXiv-2605.11594-PointForward-Feedforward-Driving-Reconstruction-through-Point-Aligned-Representations]]
- [[sources/papers/arXiv-2603.17571-PanoVGGT-Feed-Forward-3D-Reconstruction-from-Panoramic-Imagery]]
- [[sources/projects/gsplat/gsplat]]
- [[sources/projects/3DGRUT/3DGRUT]]
- [[sources/projects/SplatAD/SplatAD]]
- [[sources/projects/DGGT/DGGT]]
- [[sources/projects/PanoVGGT/PanoVGGT]]
