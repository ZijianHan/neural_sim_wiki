---
tags: [evaluation, world-model, geometric-consistency, benchmark]
last_compiled: 2026-05-15
---

# PDI-Bench

## Definition
PDI-Bench (Perspective Distortion Index) is a quantitative framework for evaluating geometric coherence in generated videos, measuring failures that perceptual metrics (FID, FVD) cannot capture.

## Synthesis
### Source-grounded Highlights
- **Three failure dimensions**:
  1. **Scale-depth alignment** — do objects maintain correct scale relative to their depth?
  2. **3D motion consistency** — do object trajectories form physically plausible 3D paths?
  3. **3D structural rigidity** — do rigid objects deform unexpectedly across frames?
- **Pipeline**: segmentation (SAM 2) → point tracking (CoTracker3) → monocular 3D lift (MegaSaM) → projective-geometry residual computation.
- **PDI-Dataset**: diverse scenarios designed to stress geometric constraints.
- Reveals consistent geometry-specific failure modes in SOTA video generators that are invisible to standard perceptual metrics.
- Project: https://pdi-bench.github.io/

### Why It Matters for Driving Simulation
If video world models produce geometrically inconsistent outputs, they cannot reliably generate training data for AD perception. PDI-Bench provides the diagnostic signal to identify and quantify these failures.

### 中文版本
PDI-Bench 是定量评估生成视频几何一致性的框架。通过分割→追踪→单目3D重建→投影残差计算，在三个维度（尺度-深度对齐、3D运动一致性、3D结构刚性）揭示视频生成器中感知指标无法捕捉的系统性几何失败。对驾驶仿真数据生成的质量保证至关重要。

## Graph Connections
- [[World Model]]
- [[Coding Agent as World Simulator]]
- [[SANA-WM]]
- [[WorldForge]]
- [[Method Comparison]]

## Sources
- [[sources/papers/arXiv-2605.15185-PDI-Bench-Geometric-Consistency-Evaluation-for-Video-World-Models]]
