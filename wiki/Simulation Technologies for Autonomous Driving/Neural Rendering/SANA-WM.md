---
tags: [world-model, video-generation, camera-control, efficient]
last_compiled: 2026-05-15
---

# SANA-WM

## Definition
SANA-WM is a 2.6B-parameter open-source world model natively trained for one-minute, 720p video generation with precise 6-DoF camera control.

## Synthesis
### Source-grounded Highlights
- **Hybrid linear attention**: frame-wise Gated DeltaNet (GDN) for memory efficiency + softmax attention for quality.
- **Dual-branch camera control**: precise 6-DoF trajectory adherence throughout generation.
- **Two-stage generation pipeline**: long-video refiner improves quality and consistency across sequences.
- **Metric-scale pose annotation pipeline**: extracts accurate 6-DoF from public videos.
- **Extreme efficiency**: trained on ~213K public clips in 15 days on 64 H100s; distilled variant runs 60s 720p clip in 34s on single RTX 5090 with NVFP4 quantization.
- 36× higher throughput than prior open-source baselines; visual quality comparable to industrial baselines (LingBot-World, HY-WorldPlay).

### Key Differentiator
First open-source world model natively trained for minute-scale generation (not short clips stitched), with consumer-GPU deployment capability.

### 中文版本
SANA-WM 是26亿参数的开源世界模型，原生支持一分钟720p视频生成和6-DoF相机控制。混合线性注意力 + 双分支相机控制，蒸馏版可在单块RTX 5090上34秒生成60秒视频。吞吐量比开源基线高36倍。

## Graph Connections
- [[World Model]]
- [[WorldForge]]
- [[EponaV2]]
- [[Method Comparison]]

## Sources
- [[sources/papers/arXiv-2605.15178-SANA-WM-Efficient-Minute-Scale-World-Modeling]]
