---
title: "3D Skew-Normal Splatting"
arxiv_id: 2605.15010v1
source: http://arxiv.org/abs/2605.15010v1
pdf: http://arxiv.org/pdf/2605.15010v1
date: 2026-05-14
Imported At: 2026-05-15
category: Gaussian Splatting
---

# 3D Skew-Normal Splatting
## One-paragraph summary

Skew-Normal Splatting (SNS) replaces Gaussian primitives with Azzalini Skew-Normal distributions, introducing a learnable skewness parameter that enables continuous interpolation between symmetric Gaussians and Half-Gaussian-like shapes. This allows better modeling of sharp boundaries and one-sided structures under finite primitive budgets. SNS preserves analytical tractability under affine transforms and marginalization, enabling seamless integration into existing 3DGS rasterization pipelines. A decoupled parameterization and block-wise optimization address scale-rotation-skewness coupling.

## Key ideas

- **Skew-Normal primitive**: Azzalini distribution with bounded learnable skewness.
- **Continuous shape control**: interpolates between symmetric Gaussian and Half-Gaussian without hard truncation.
- **Analytical tractability**: closed-form under affine transforms and marginalization (drop-in replacement).
- **Decoupled parameterization**: separates scale, rotation, and skewness for stable optimization.
- **Block-wise optimization**: addresses parameter coupling during training.

## Novelty (What is new)

- First use of Skew-Normal distribution as 3DGS primitive (beyond elliptical family and hard truncation).
- Continuous asymmetric shape control while preserving full analytical tractability.
- Decoupled parameterization strategy for skewness-aware optimization.

## Why it matters

Extends fundamental 3DGS theory (Gaussian Splatting page in wiki). Better boundary and thin-structure representation under the same primitive budget is relevant to driving scene reconstruction where object edges and thin poles are common failure modes.

## Reported limitations/open challenges (from framing)

- Additional parameter (skewness) per primitive increases model complexity.
- Benefits most visible on sharp boundaries — may be marginal for smooth/diffuse scenes.

## Source snippets used

- arXiv abstract 2605.15010v1

---

## 一段话总结

Skew-Normal Splatting（SNS）用Azzalini偏正态分布替代高斯原语，引入可学习偏度参数实现对称高斯到半高斯形状的连续插值。在有限原语预算下更好地建模锐利边界和单侧结构，同时保持仿射变换和边缘化下的解析可追踪性。

## 核心思想

- 偏正态原语：有界可学习偏度的Azzalini分布。
- 连续形状控制：无硬截断的不对称建模。
- 解耦参数化：分离尺度、旋转和偏度。

## 创新点（这篇论文新在哪里）

- 首次将偏正态分布用作3DGS原语。
- 连续不对称形状控制 + 完整解析可追踪性。

## 重要性

扩展3DGS基础理论，改善边界/薄结构表征，对驾驶场景中的物体边缘和细杆建模相关。

## 论文框架中提到的局限/挑战

- 每原语增加偏度参数；对光滑场景收益可能有限。

## 本页信息来源

- arXiv摘要 2605.15010v1
