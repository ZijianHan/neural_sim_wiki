---
title: Taming Video Models for 3D and 4D Generation via Zero-Shot Camera Control
arxiv_id: 2509.15130v3
source: https://arxiv.org/abs/2509.15130
pdf: https://arxiv.org/pdf/2509.15130
project: https://worldforge-agi.github.io/
date: 2026-03-21
Imported At: 2026-05-14
category: World Model
---

# Taming Video Models for 3D and 4D Generation via Zero-Shot Camera Control

## One-paragraph summary
This paper proposes **WorldForge**, a training-free inference-time framework that turns pretrained video diffusion models into stronger engines for spatially controlled 3D/4D generation. The core problem is that existing video models have rich priors but weak camera-trajectory control, motion/appearance entanglement, and artifact-prone behavior when using warped inputs. WorldForge addresses this with three coordinated mechanisms: **Intra-Step Recursive Refinement (IRR)** to repeatedly enforce trajectory-consistent observations during denoising, **Flow-Gated Latent Fusion (FLF)** to inject guidance mainly into motion-related latent channels while preserving appearance channels, and **Dual-Path Self-Corrective Guidance (DSG)** to balance trajectory control against perceptual quality by comparing guided and unguided denoising paths. The authors report SOTA trajectory adherence and perceptual quality, while keeping the method plug-and-play and model-agnostic across many downstream tasks.

## Key ideas
1. **Inference-only control instead of retraining**: avoids per-task fine-tuning cost and preserves pretrained model priors.
2. **Micro-step correction during denoising (IRR)**: improves strict adherence to target camera paths.
3. **Motion-aware selective latent guidance (FLF)**: reduces visual degradation from overwriting all latent channels.
4. **Quality-control tradeoff handling (DSG)**: uses a guided vs. unguided dual path to suppress warping-induced artifacts.

## Novelty (What is new)
- **Unified inference-time control stack**: combines IRR + FLF + DSG in one training-free pipeline, rather than relying on only fine-tuning or only post-hoc warping.
- **Intra-step trajectory enforcement**: injects camera-path corrections *within* each denoising step (micro predict-correct), not just between steps or via external post-processing.
- **Flow-gated channel-level guidance**: uses optical-flow analysis to target motion-related latent channels and reduce appearance degradation from global latent overwrites.
- **Dual-path self-corrective denoising**: introduces a guided/unguided parallel path to dynamically correct warping-induced artifacts while preserving controllability.
- **Plug-and-play generalization claim**: positions the method as model-agnostic and transferable across many 3D/4D and editing tasks without retraining.

## Why it matters
- Frames a practical route to use general video diffusion priors for controllable 3D/4D tasks.
- Targets a key bottleneck for world-model-like spatial generation: precise camera control without destroying realism.
- Emphasizes broad transfer (editing, stabilization, virtual try-on) under a single training-free pipeline.

## Reported limitations/open challenges (from framing)
- Strong control remains difficult under imperfect depth/warping and occlusions.
- Balancing controllability, geometric consistency, and perceptual quality is still a core tradeoff.

## Source snippets used
- Abstract and metadata from arXiv abs page.
- Introduction framing and method overview from arXiv HTML page (`/html/2509.15130v3`).

---

# 中文版本

## 一段话总结
本文提出 **WorldForge**，这是一个**无需训练、仅在推理阶段运行**的框架，目标是将预训练视频扩散模型更可靠地用于可控的 3D/4D 生成。论文指出现有视频模型虽具备较强先验，但在相机轨迹控制、时空一致性以及“场景运动/相机运动”解耦方面存在不足，且在 warping 输入下容易出现伪影。WorldForge 通过三个协同模块解决这些问题：**IRR（Intra-Step Recursive Refinement）** 在每个去噪步内反复注入与目标轨迹一致的观测约束；**FLF（Flow-Gated Latent Fusion）** 基于光流相关性只在运动相关潜变量通道施加引导、尽量保留外观细节；**DSG（Dual-Path Self-Corrective Guidance）** 通过“引导路径 vs. 非引导路径”的差分自校正，在轨迹可控性与感知质量之间取得更好平衡。作者报告该方法在轨迹跟随与视觉质量上优于训练型和纯推理型基线，同时保持即插即用与模型无关性。

## 核心思想
1. **仅推理期控制，避免重训练**：降低每任务微调成本，并尽量保留预训练模型先验。
2. **去噪过程中的微步纠偏（IRR）**：强化对目标相机路径的严格跟随能力。
3. **面向运动的选择性潜空间引导（FLF）**：减少“全通道覆盖”导致的画质损伤。
4. **质量-控制折中的自校正（DSG）**：利用双路径差分抑制 warping 带来的结构与纹理伪影。

## 创新点（这篇论文新在哪里）
- **统一的推理期控制框架**：将 IRR、FLF、DSG 组合为一个 training-free 的整体方案，而非仅依赖微调或仅依赖后处理 warping。
- **步内轨迹约束注入**：在每个去噪 step 内进行微循环纠偏（predict-correct），而不是只在 step 间或后处理阶段施加约束。
- **基于光流的通道级选择性引导**：定位运动相关潜变量通道，降低“全通道覆盖”对外观细节的破坏。
- **双路径自校正去噪**：并行引导/非引导路径，动态抑制 warping 伪影，同时尽量保持轨迹可控性。
- **即插即用泛化定位**：强调无需重训练即可迁移到多类 3D/4D 生成与视频编辑任务。

## 重要性
- 为“利用通用视频扩散先验完成可控 3D/4D 任务”提供了工程上可落地的路线。
- 直击类似 world model 空间生成中的关键瓶颈：既要轨迹精确可控，又要保持逼真度。
- 在单一训练自由（training-free）框架下，强调了较强的任务迁移能力（编辑、稳像、虚拟试穿等）。

## 论文框架中提到的局限/挑战
- 在深度误差、warping 误差与遮挡存在时，强控制仍然困难。
- 可控性、几何一致性与感知质量三者之间依然存在核心权衡。

## 本页信息来源
- arXiv abs 页面中的摘要与元数据。
- arXiv HTML 页面（`/html/2509.15130v3`）中的引言问题定义与方法概述。
