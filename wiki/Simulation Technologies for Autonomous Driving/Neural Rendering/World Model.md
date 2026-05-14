---
tags: [world-model, neural-rendering]
last_compiled: 2026-05-14
sources: ["sources/papers/arXiv-2509.15130-Taming-Video-Models-for-3D-and-4D-Generation-via-Zero-Shot-Camera-Control.md"]
---

# World Model

## Definition
A world model is a learned representation of environment dynamics used for prediction, planning, and simulation.

## Synthesis
### Type Focus from Current Sources
From currently available sources, the most concrete world-model direction is:
- **Inference-time controlled video-diffusion world modeling** (represented by [[WorldForge]]).

This type uses pretrained video diffusion priors and adds trajectory control during generation, rather than retraining a dedicated model per task.

### What Is New in This Type (from paper evidence)
- A training-free control stack that combines:
  - Intra-Step Recursive Refinement (IRR),
  - Flow-Gated Latent Fusion (FLF),
  - Dual-Path Self-Corrective Guidance (DSG).
- The approach targets camera-path adherence while reducing motion/appearance entanglement and warping artifacts.

### Boundaries of Evidence
Current wiki sources provide one detailed paper entry. This page should be expanded when additional world-model papers are imported.

### 中文版本
### 当前来源下的类型重点
基于当前已收录来源，最明确的 world model 路线是：
- **推理期可控的视频扩散 world model 路线**（以 [[WorldForge]] 为代表）。

该路线强调利用预训练视频扩散先验，并在生成过程中注入轨迹控制，而不是针对每个任务重新训练模型。

### 该路线的新意（基于论文证据）
- 一个 training-free 的组合控制栈：
  - Intra-Step Recursive Refinement（IRR）
  - Flow-Gated Latent Fusion（FLF）
  - Dual-Path Self-Corrective Guidance（DSG）
- 目标是在提高相机轨迹跟随性的同时，减少运动/外观耦合与 warping 伪影。

### 证据边界
当前 wiki 仅有一篇详细论文来源；后续导入更多 world model 论文后应继续扩展本页类型谱系。

## Graph Connections
- [[Simulation Technologies for Autonomous Driving]]
- [[Neural Rendering]]
- [[WorldForge]]
- [[NeRF]]
- [[Gaussian Splatting]]
