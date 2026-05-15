---
tags: [world-model, neural-rendering]
last_compiled: 2026-05-15
---

# World Model

## Definition
A world model is a learned representation of environment dynamics used for prediction, planning, and simulation.

## Synthesis
### Type Taxonomy from Current Sources
From available sources, world models cluster into four routes:

1. **Inference-time controlled video-diffusion** (represented by [[WorldForge]]): pretrained video diffusion priors with trajectory control during generation. Training-free control stack (IRR, FLF, DSG) targeting camera-path adherence.

2. **Efficient camera-controlled generation** (represented by [[SANA-WM]]): 2.6B-parameter open-source model natively trained for 60-second 720p video with 6-DoF camera control. Hybrid linear attention (Gated DeltaNet + softmax), deployable on single consumer GPU. 36× higher throughput than open-source baselines.

3. **Perception-free driving world models** (represented by [[EponaV2]]): forecasts future geometry + semantic maps (not just RGB frames) to achieve comprehensive scene understanding for trajectory planning. LLM-inspired flow matching policy optimization. SOTA on NAVSIM without perception annotations.

4. **Code-based physics world models** (represented by [[Coding Agent as World Simulator]]): constructs world models via executable simulation code rather than learned neural dynamics. Multi-agent pipeline (planning → code gen → visual review → physics analysis) iteratively refines until physical constraints are met. Outperforms video-based models on physical accuracy.

### Evaluation
- [[PDI-Bench]]: quantitative framework for geometric coherence in generated videos. Measures scale-depth alignment, 3D motion consistency, and structural rigidity via projective-geometry residuals. Reveals systematic geometric failures in SOTA video generators invisible to perceptual metrics (FID/FVD).

### 中文版本
### 当前来源下的类型谱系
1. **推理期可控视频扩散路线**（[[WorldForge]]）：training-free 控制栈注入轨迹控制。
2. **高效相机可控生成路线**（[[SANA-WM]]）：2.6B参数开源模型，原生60秒720p生成，混合线性注意力，消费级GPU可部署。
3. **无感知驾驶世界模型路线**（[[EponaV2]]）：预测未来几何+语义图，LLM式策略优化，无需感知标注。
4. **代码式物理世界模型路线**（[[Coding Agent as World Simulator]]）：通过可执行仿真代码构建，多Agent管线保证物理约束。

### 评估
- [[PDI-Bench]]：视频世界模型几何一致性定量评估框架。

## Graph Connections
- [[Simulation Technologies for Autonomous Driving]]
- [[Neural Rendering]]
- [[WorldForge]]
- [[SANA-WM]]
- [[EponaV2]]
- [[Coding Agent as World Simulator]]
- [[PDI-Bench]]
- [[NeRF]]
- [[Gaussian Splatting]]

## Sources
- [[sources/papers/arXiv-2509.15130-Taming-Video-Models-for-3D-and-4D-Generation-via-Zero-Shot-Camera-Control]]
- [[sources/papers/arXiv-2605.15178-SANA-WM-Efficient-Minute-Scale-World-Modeling]]
- [[sources/papers/arXiv-2605.14696-EponaV2-Driving-World-Model-with-Comprehensive-Future-Reasoning]]
- [[sources/papers/arXiv-2605.14398-Coding-Agent-Is-Good-As-World-Simulator]]
- [[sources/papers/arXiv-2605.15185-PDI-Bench-Geometric-Consistency-Evaluation-for-Video-World-Models]]
