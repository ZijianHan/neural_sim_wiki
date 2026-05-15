---
tags:
  - index
  - navigation
last_compiled: 2026-05-15
---
---

# Wiki Index

## Main Topic
- [[Simulation Technologies for Autonomous Driving/Simulation Technologies for Autonomous Driving|Simulation Technologies for Autonomous Driving]] — umbrella page for rendering, sensor modeling, and control-oriented simulation methods.

## Category: Neural Rendering
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/Neural Rendering|Neural Rendering]] — synthesis hub of rendering paradigms used in this wiki.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/Method Comparison|Method Comparison]] — side-by-side comparison of currently imported methods and tradeoffs.

### Gaussian Splatting Methods
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/Gaussian Splatting|Gaussian Splatting]] — taxonomy of 3DGS-family methods and variants.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/3DGS|3DGS]] — foundational real-time Gaussian splatting method.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/3DGUT|3DGUT]] — UT-based nonlinear-camera extension of 3DGS.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/3DGRT|3DGRT]] — Gaussian-particle ray tracing route.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/3D Skew-Normal Splatting|3D Skew-Normal Splatting]] — asymmetric Skew-Normal primitives for better boundary modeling.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/BlitzGS|BlitzGS]] — distributed city-scale 3DGS training in minutes via index-parity sharding.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/AV1-3DGS|AV1-3DGS]] — codec motion vectors for 8× denser SfM init and faster 3DGS training.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/gsplat|gsplat]] — infrastructure library node for efficient Gaussian rasterization.

### AD-Specific Reconstruction & Simulation
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/SplatAD|SplatAD]] — real-time camera+lidar rendering for AD.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/Real2Sim|Real2Sim]] — 4DGS + differentiable MPM physics for editable AD scene simulation.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/Flow4DGS-SLAM|Flow4DGS-SLAM]] — optical-flow-guided dynamic 4DGS SLAM.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/DGGT|DGGT]] — pose-free feedforward 4D reconstruction from unposed images.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/PointForward|PointForward]] — point-aligned feedforward driving reconstruction with scene-graph dynamics.

### Feed-Forward Reconstruction (VGGT Lineage)
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/VGGT-Omega|VGGT-Ω]] — scaled VGGT successor with register attention, 15× data, and dynamic scene support.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/TurboVGGT|TurboVGGT]] — adaptive sparse attention for fast multi-view VGGT-style reconstruction.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/SwiftVGGT|SwiftVGGT]] — training-free acceleration strategy for VGGT-based kilometer-scale dense reconstruction.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/OVGGT|OVGGT]] — fixed-budget streaming extension for VGGT-style long-horizon 3D reconstruction.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/PanoVGGT|PanoVGGT]] — panoramic feed-forward reconstruction with spherical geometry adaptation and PanoCity dataset contribution.

### World Models
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/World Model|World Model]] — world-model method types grounded by current sources.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/WorldForge|WorldForge]] — inference-time controlled video-diffusion generation.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/SANA-WM|SANA-WM]] — efficient 2.6B open-source world model for 60s 720p video with camera control.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/EponaV2|EponaV2]] — perception-free driving world model with future geometry/semantic reasoning.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/Coding Agent World Simulator|Coding Agent as World Simulator]] — code-based physics world model via multi-agent pipeline.
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/PDI-Bench|PDI-Bench]] — geometric coherence evaluation framework for video world models.

### Other
- [[Simulation Technologies for Autonomous Driving/Neural Rendering/NeRF|NeRF]] — placeholder for NeRF-based methods and comparisons.

## Category: Sensor Modelling
- [[Simulation Technologies for Autonomous Driving/Sensor Modelling/Sensor Modelling|Sensor Modelling]] — modality-specific sensor realism framework.
- [[Simulation Technologies for Autonomous Driving/Sensor Modelling/Radar|Radar]] — radar simulation scope and key modeling questions.
- [[Simulation Technologies for Autonomous Driving/Sensor Modelling/Lidar|Lidar]] — lidar modeling factors (sparsity, intensity, ray drop, temporal effects).

## Category: Q&A
- [[QA/QA-Template|QA Template]] — per-topic Q&A template for knowledge-space conversations.
- [[QA/DGGT-vs-PointForward|DGGT vs PointForward]] — architectural differences, tradeoffs, and deployment guidance.

## Category: Operations
- [[log|log]] — append-only timeline of ingest/refactor/update operations.
