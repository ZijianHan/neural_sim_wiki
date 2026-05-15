---
title: "Coding Agent Is Good As World Simulator"
arxiv_id: 2605.14398v1
source: http://arxiv.org/abs/2605.14398v1
pdf: http://arxiv.org/pdf/2605.14398v1
date: 2026-05-14
Imported At: 2026-05-15
category: World Model
---

# Coding Agent Is Good As World Simulator
## One-paragraph summary

This paper presents an agentic framework that constructs physics-based world models through executable simulation code, as an alternative to video-based world models. The system coordinates planning, code generation, visual review, and physics analysis agents: the planning agent converts prompts to structured scene plans, the code agent implements executable simulation, and review/analysis agents provide iterative feedback until physical constraints are satisfied. Outperforms video-based models in physical accuracy, instruction fidelity, and visual quality for driving simulation and embodied robot tasks.

## Key ideas

- **Code-as-world-model**: physics simulation via executable code rather than learned latent dynamics.
- **Multi-agent coordination**: planning → code generation → visual review → physics analysis loop.
- **Iterative refinement**: code revised based on visual and physics feedback until constraints met.
- **Explicit physics constraints**: unlike video models that infer dynamics in latent space.
- **Cross-domain applicability**: driving simulation and embodied robotics.

## Novelty (What is new)

- Framing executable simulation code as a world model alternative to video generation.
- Multi-agent pipeline with physics analysis agent verifying physical consistency.
- Demonstrated superiority over video-based world models on physical accuracy metrics.

## Why it matters

Presents a fundamentally different paradigm for world simulation — code-based rather than neural. Highly relevant to driving simulation (explicit in paper) and offers complementary strengths to video/neural world models tracked in this wiki: perfect physics but potentially less visual realism for unseen scenarios.

## Reported limitations/open challenges (from framing)

- Relies on existing simulation engines (not fully generative for novel physics).
- Code generation quality bounded by LLM capability.
- Visual fidelity of simulation rendering vs. neural rendering not deeply compared.

## Source snippets used

- arXiv abstract 2605.14398v1

---

## 一段话总结

本文提出一个通过可执行仿真代码构建物理世界模型的Agent框架，作为视频世界模型的替代方案。系统协调规划、代码生成、视觉审查和物理分析Agent，迭代修正直至满足物理约束。在物理准确性、指令忠实度和视觉质量上超越视频模型，可应用于驾驶仿真和具身机器人。

## 核心思想

- 代码即世界模型：通过可执行代码实现物理仿真。
- 多Agent协调：规划→代码生成→视觉审查→物理分析循环。
- 显式物理约束（区别于潜空间隐式动力学）。

## 创新点（这篇论文新在哪里）

- 将可执行仿真代码框定为视频生成的世界模型替代。
- 包含物理分析Agent的多Agent管线。

## 重要性

为驾驶仿真提供代码式世界模型的全新范式，与wiki中的神经/视频世界模型形成互补。

## 论文框架中提到的局限/挑战

- 依赖现有仿真引擎；代码生成质量受限于LLM能力。

## 本页信息来源

- arXiv摘要 2605.14398v1
