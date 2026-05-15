---
tags: [world-model, driving, planning, perception-free]
last_compiled: 2026-05-15
---

# EponaV2

## Definition
EponaV2 is a perception-free driving world model that achieves high-quality trajectory planning by forecasting comprehensive future representations including geometry and semantic maps.

## Synthesis
### Source-grounded Highlights
- Predicts future **geometry maps** and **semantic maps** beyond just RGB frames — enables deeper 3D scene understanding.
- Introduces **flow matching group relative policy optimization** (inspired by LLM training recipes) for improved planning accuracy.
- No reliance on expensive manual perception annotations → better data scalability.
- SOTA among perception-free models on three NAVSIM benchmarks (+1.3 PDMS, +5.5 EPDMS).

### Key Differentiation
Unlike prior perception-free driving world models that only predict next-frame images, EponaV2 forecasts richer modalities (geometry + semantics) that ground its planning in spatial understanding.

### 中文版本
EponaV2 是一种无感知驾驶世界模型，通过预测未来几何图和语义图实现全面的未来推理。引入LLM风格的流匹配策略优化，在三个NAVSIM基准上取得无感知模型SOTA，无需感知标注。

## Graph Connections
- [[World Model]]
- [[SANA-WM]]
- [[Simulation Technologies for Autonomous Driving]]
- [[Method Comparison]]

## Sources
- [[sources/papers/arXiv-2605.14696-EponaV2-Driving-World-Model-with-Comprehensive-Future-Reasoning]]
