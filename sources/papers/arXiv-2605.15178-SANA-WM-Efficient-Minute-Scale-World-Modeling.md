---
title: "SANA-WM: Efficient Minute-Scale World Modeling with Hybrid Linear Diffusion Transformer"
arxiv_id: 2605.15178v1
source: http://arxiv.org/abs/2605.15178v1
pdf: http://arxiv.org/pdf/2605.15178v1
date: 2026-05-14
Imported At: 2026-05-15
category: World Model
---

# SANA-WM: Efficient Minute-Scale World Modeling with Hybrid Linear Diffusion Transformer
## One-paragraph summary

SANA-WM is a 2.6B-parameter open-source world model for one-minute, 720p video generation with precise 6-DoF camera control. It achieves quality comparable to large-scale industrial baselines (LingBot-World, HY-WorldPlay) while being far more efficient. Key designs: hybrid linear attention (Gated DeltaNet + softmax), dual-branch camera control, two-stage long-video refiner, and a robust annotation pipeline for metric-scale poses. Trained on ~213K public video clips in 15 days on 64 H100s; distilled variant runs on a single RTX 5090 (60s 720p clip in 34s).

## Key ideas

- **Hybrid linear attention**: frame-wise Gated DeltaNet for memory efficiency + softmax attention for quality.
- **Dual-branch camera control**: precise 6-DoF trajectory adherence.
- **Two-stage generation**: long-video refiner improves quality/consistency.
- **Metric-scale pose annotation**: extracts accurate 6-DoF from public videos.
- **Extreme efficiency**: single-GPU deployment with NVFP4 quantization; 36× higher throughput than open-source baselines.

## Novelty (What is new)

- Hybrid linear attention (GDN + softmax) for memory-efficient minute-scale video generation.
- End-to-end world model natively trained for 60-second generation (not short clips stitched).
- Metric-scale camera pose extraction pipeline from public video data.
- Deployable on consumer GPU (RTX 5090) at real-time-ish speeds.

## Why it matters

Directly relevant to driving simulation and world model topics in this wiki. Demonstrates that open-source world models can match industrial baselines with efficient architectures, enabling controllable long-horizon video generation for simulation and data augmentation.

## Reported limitations/open challenges (from framing)

- Only ~213K training clips — quality may improve with more data.
- Action-following accuracy "stronger than prior open-source" but comparison to closed-source industrial models is "comparable visual quality" (not superior).

## Source snippets used

- arXiv abstract 2605.15178v1

---

## 一段话总结

SANA-WM是一个26亿参数的开源世界模型，原生支持一分钟720p视频生成和精确6-DoF相机控制。采用混合线性注意力、双分支相机控制和两阶段生成管线，在64块H100上训练15天；蒸馏版可在单块RTX 5090上34秒生成60秒720p视频。

## 核心思想

- 混合线性注意力（GDN + softmax）实现内存高效的长视频建模。
- 双分支6-DoF相机控制。
- 度量尺度位姿标注管线。
- 极致效率：消费级GPU可部署。

## 创新点（这篇论文新在哪里）

- 混合线性注意力用于分钟级视频生成。
- 原生训练60秒生成（非短片段拼接）。
- 消费级GPU实时部署能力。

## 重要性

展示开源世界模型可匹配工业基线，为驾驶仿真提供可控长时视频生成。

## 论文框架中提到的局限/挑战

- 训练数据仅~213K片段；与闭源工业模型对比为"可比"而非超越。

## 本页信息来源

- arXiv摘要 2605.15178v1
