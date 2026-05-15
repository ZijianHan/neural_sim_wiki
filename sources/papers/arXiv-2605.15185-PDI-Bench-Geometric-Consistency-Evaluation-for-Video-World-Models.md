---
title: "PDI-Bench: Quantitative Video World Model Evaluation for Geometric-Consistency"
arxiv_id: 2605.15185v1
source: http://arxiv.org/abs/2605.15185v1
pdf: http://arxiv.org/pdf/2605.15185v1
project: https://pdi-bench.github.io/
date: 2026-05-14
Imported At: 2026-05-15
category: World Model
---

# PDI-Bench: Quantitative Video World Model Evaluation for Geometric-Consistency
## One-paragraph summary

PDI-Bench (Perspective Distortion Index) is a quantitative framework for evaluating geometric coherence in generated videos. It obtains object-centric observations via segmentation and point tracking, lifts them to 3D via monocular reconstruction, and computes projective-geometry residuals across three failure dimensions: scale-depth alignment, 3D motion consistency, and 3D structural rigidity. The accompanying PDI-Dataset covers diverse scenarios designed to stress geometric constraints. Reveals consistent geometry-specific failure modes in SOTA video generators not captured by perceptual metrics.

## Key ideas

- **Three failure dimensions**: scale-depth alignment, 3D motion consistency, 3D structural rigidity.
- **Projective-geometry residuals**: quantitative metrics beyond perceptual quality.
- **Pipeline**: segmentation (SAM 2) → tracking (CoTracker3) → monocular 3D lift (MegaSaM) → residual computation.
- **Diagnostic signal**: identifies specific geometric failures that FID/FVD miss.

## Novelty (What is new)

- First quantitative geometric coherence benchmark for video world models (not perceptual/learned graders).
- Three-axis failure taxonomy with computable residuals.
- Demonstrates systematic geometric failures in SOTA generators invisible to standard metrics.

## Why it matters

Critical evaluation tool for world models used in driving simulation. If video world models produce geometrically inconsistent outputs, they cannot reliably generate training data for AD perception. Directly relevant to the wiki's World Model page for understanding model limitations.

## Reported limitations/open challenges (from framing)

- Relies on monocular depth estimation quality for 3D lifting.
- Dataset scenarios may not cover all real-world geometric stress cases.

## Source snippets used

- arXiv abstract 2605.15185v1

---

## 一段话总结

PDI-Bench是一个定量评估生成视频几何一致性的框架。通过分割、追踪和单目3D重建获取物体观测，计算三个失败维度（尺度-深度对齐、3D运动一致性、3D结构刚性）的投影几何残差。揭示了SOTA视频生成器中感知指标无法捕捉的系统性几何失败模式。

## 核心思想

- 三维失败分类：尺度-深度对齐、3D运动一致性、3D结构刚性。
- 投影几何残差作为定量指标。
- 分割→追踪→单目3D提升→残差计算管线。

## 创新点（这篇论文新在哪里）

- 首个视频世界模型的定量几何一致性基准。
- 三轴失败分类与可计算残差。

## 重要性

为驾驶仿真中的世界模型提供关键评估工具——几何不一致的输出不能可靠地生成AD训练数据。

## 论文框架中提到的局限/挑战

- 依赖单目深度估计质量；数据集可能未覆盖所有几何压力场景。

## 本页信息来源

- arXiv摘要 2605.15185v1
