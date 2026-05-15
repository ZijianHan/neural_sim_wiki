---
tags: [worldforge, world-model, neural-rendering]
last_compiled: 2026-05-14
---

# WorldForge

## Definition
WorldForge is a training-free inference-time framework that adds camera-trajectory control to pretrained video diffusion models for 3D/4D generation tasks.

## Synthesis
### Core Method
WorldForge combines three mechanisms:
1. **IRR (Intra-Step Recursive Refinement):** enforces trajectory-consistent observations within each denoising step.
2. **FLF (Flow-Gated Latent Fusion):** selectively guides motion-related latent channels to reduce appearance degradation.
3. **DSG (Dual-Path Self-Corrective Guidance):** uses guided/unguided path differences to suppress warping-induced artifacts.

### Claimed Benefits
- Better trajectory adherence under target camera paths.
- Better perceptual quality than naive warping-only control.
- Plug-and-play usage without model retraining.

### Novelty Summary
The novelty is not a single module but a unified inference-time control stack that jointly improves controllability and visual fidelity.

### 中文版本
### 核心方法
WorldForge 由三个模块组成：
1. **IRR（步内递归纠偏）：** 在每个去噪 step 内注入轨迹一致观测。
2. **FLF（光流门控潜空间融合）：** 选择性作用于运动相关通道，降低外观细节损伤。
3. **DSG（双路径自校正引导）：** 利用引导/非引导路径差分抑制 warping 伪影。

### 声称收益
- 更好的目标相机轨迹跟随能力。
- 相比仅 warping 控制，具备更高感知质量。
- 无需重训练，可即插即用。

### 创新点概括
其创新在于将多个推理期控制机制统一为一个整体控制栈，同时兼顾可控性与视觉质量。

## Graph Connections
- [[World Model]]
- [[Neural Rendering]]
- [[Simulation Technologies for Autonomous Driving]]

## Sources
- [[sources/papers/arXiv-2509.15130-Taming-Video-Models-for-3D-and-4D-Generation-via-Zero-Shot-Camera-Control]]
