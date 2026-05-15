---
title: 3D Gaussian Ray Tracing Fast Tracing of Particle Scenes
source: https://research.nvidia.com/labs/toronto-ai/3DGRT
pdf: https://research.nvidia.com/labs/toronto-ai/3DGRT/res/3dgrt_compressed.pdf
code: https://github.com/nv-tlabs/3dgrut
venue: SIGGRAPH Asia 2024 (Journal Track, ACM TOG)
date: 2024
Imported At: 2026-05-14
category: Gaussian Splatting
related_project: sources/projects/3DGRUT/3DGRUT.md
---

# 3D Gaussian Ray Tracing: Fast Tracing of Particle Scenes

## One-paragraph summary
This paper introduces **3DGRT**, a rendering pipeline that ray-traces Gaussian particle scenes instead of rasterizing them. It builds a BVH over particle bounding primitives and traces per-pixel rays on GPU RT hardware, with specialized handling for large numbers of semi-transparent particles (including depth-ordered batching). The motivation is to retain strong reconstruction quality while unlocking ray-tracing-native capabilities—secondary rays (reflections/shadows/refractions), distorted/time-dependent camera models (including rolling shutter), and stochastic ray sampling—at practical speeds.

## Key ideas
1. **Ray tracing over Gaussian particles** with BVH acceleration rather than tile-based splat rasterization.
2. **Semi-transparent particle handling** via specialized sorting/accumulation strategy for efficient compositing.
3. **Bounding primitive design** to exploit fast ray-triangle intersection hardware.
4. **Camera flexibility** for distorted and time-dependent models without rasterization-specific projection constraints.
5. **Extended Gaussian representation tweaks** (including generalized kernel usage) to reduce hit counts.

## Novelty (What is new)
- **Primary shift of rendering regime**: replaces 3DGS-style rasterization with an efficient Gaussian-particle ray-tracing pipeline.
- **Practical semi-transparent tracing formulation**: proposes particle intersection/shading organization tailored to volumetric Gaussian scenes.
- **Direct support for advanced effects**: secondary lighting and complex camera effects become native rather than bolt-on approximations.
- **Throughput-oriented representation refinements**: adds kernel/primitive choices to reduce costly particle hits while keeping quality.

## Why it matters
- Expands Gaussian Splatting from fast rasterized view synthesis to richer physically-inspired rendering workflows.
- Better fits robotics/AV capture conditions with distorted cameras and rolling-shutter effects.
- Provides a foundation later bridged by 3DGUT/3DGRUT-style hybrid rendering.

## Reported limitations/open challenges (from framing)
- Relies on ray-tracing hardware for best performance.
- Compared with pure rasterization, speed still faces pressure in complex/high-overdraw scenes.
- Balancing physical effect richness against real-time constraints remains central.

## Source snippets used
- Official project page abstract and method description (`research.nvidia.com/labs/toronto-ai/3DGRT`).
- Repository README paper block and metadata (`github.com/nv-tlabs/3dgrut`).
- Project BibTeX metadata (`assets/3dgrt2024.bib`).

---

# 中文版本

## 一段话总结
本文提出 **3DGRT**：将高斯粒子场景从“光栅化渲染”改为“光线追踪渲染”。方法在高斯粒子包围体上构建 BVH，并利用 GPU RT 硬件进行逐像素追踪，同时针对大量半透明粒子设计了高效的排序与累积流程。其目标是在保持重建质量的同时，原生支持 rasterization 难以高效处理的能力：二次光线（反射/折射/阴影）、畸变与时间相关相机（如 rolling shutter）以及随机采样光线等。

## 核心思想
1. **高斯粒子光追替代 splat 光栅化**，通过 BVH 加速。
2. **半透明粒子高效处理**，采用针对性深度顺序批处理与累积。
3. **包围体与求交流程优化**，充分利用硬件光追能力。
4. **复杂相机模型原生支持**，无需受限于 rasterization 投影近似。
5. **表示层优化**（如 kernel 设计）以降低命中数量并提速。

## 创新点（这篇论文新在哪里）
- **渲染范式切换**：将 3DGS 的核心渲染从 rasterization 转到面向高斯粒子的高效 ray tracing。
- **半透明高斯粒子的专用光追组织方式**：针对粒子场景设计求交/着色/累积策略。
- **高级视觉效果原生化**：反射、折射、阴影与复杂相机效应不再是附加近似，而是主流程能力。
- **吞吐导向的表示改造**：通过 kernel 与包围结构优化减少无效命中。

## 重要性
- 将 Gaussian Splatting 从“快渲染”扩展到“更强物理表达”。
- 更适配机器人/自动驾驶中的畸变镜头与 rolling shutter 数据。
- 为后续 3DGUT/3DGRUT 的混合渲染路线提供基础。

## 论文框架中提到的局限/挑战
- 对 RT 硬件依赖较强。
- 在复杂场景下与纯光栅化相比仍存在速度压力。
- 物理效果表达与实时性之间仍需权衡。

## 本页信息来源
- 3DGRT 官方项目页（摘要与方法概述）。
- 3DGRUT GitHub README 中的论文说明。
- 项目 BibTeX 元数据（`3dgrt2024.bib`）。
