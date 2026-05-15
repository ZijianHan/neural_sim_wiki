---
title: "EponaV2: Driving World Model with Comprehensive Future Reasoning"
arxiv_id: 2605.14696v1
source: http://arxiv.org/abs/2605.14696v1
pdf: http://arxiv.org/pdf/2605.14696v1
date: 2026-05-14
Imported At: 2026-05-15
category: World Model
---

# EponaV2: Driving World Model with Comprehensive Future Reasoning
## One-paragraph summary

EponaV2 is a perception-free driving world model that achieves high-quality trajectory planning through comprehensive future reasoning. Unlike prior models that only predict next-frame images, EponaV2 forecasts future geometry and semantic maps, enabling deep 3D scene understanding. Inspired by LLM training recipes, it introduces flow matching group relative policy optimization for improved planning accuracy. Achieves SOTA among perception-free models on three NAVSIM benchmarks (+1.3 PDMS, +5.5 EPDMS).

## Key ideas

- **Comprehensive future representations**: forecasts future geometry maps and semantic maps beyond just RGB frames.
- **3D + semantic modalities**: enables deeper environmental understanding for planning.
- **Flow matching group relative policy optimization**: LLM-inspired training recipe for planning accuracy.
- **Perception-free paradigm**: no reliance on expensive manual perception annotations, improving data scalability.

## Novelty (What is new)

- Forecasting geometry and semantic future representations as auxiliary world-model outputs for planning.
- Applying LLM-style policy optimization (flow matching + group relative) to driving world models.
- Demonstrating that richer future predictions improve trajectory planning without perception supervision.

## Why it matters

Directly relevant to the wiki's "World Model" and driving simulation topics. Shows a scalable path to planning without perception annotations — critical for large-scale AD simulation data generation.

## Reported limitations/open challenges (from framing)

- Perception-free models still lag behind perception-planning paradigm in some scenarios (implied by framing as "among perception-free models").
- Scalability beyond NAVSIM benchmarks not evaluated.

## Source snippets used

- arXiv abstract 2605.14696v1

---

## 一段话总结

EponaV2是一种无感知驾驶世界模型，通过预测未来几何图和语义图实现全面的未来推理和高质量轨迹规划。引入流匹配组相对策略优化（受LLM训练启发），在三个NAVSIM基准上取得无感知模型SOTA。

## 核心思想

- 全面未来表征：预测几何图 + 语义图（不仅仅是RGB帧）。
- 3D + 语义模态实现更深环境理解。
- LLM风格策略优化用于规划。

## 创新点（这篇论文新在哪里）

- 几何/语义未来预测作为世界模型辅助输出。
- LLM式流匹配策略优化应用于驾驶世界模型。

## 重要性

直接关联wiki的"World Model"和驾驶仿真主题，展示了无感知标注的可扩展规划路径。

## 论文框架中提到的局限/挑战

- 无感知模型在某些场景仍落后于感知-规划范式。

## 本页信息来源

- arXiv摘要 2605.14696v1
