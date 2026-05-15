---
tags: [swiftvggt, large-scale-reconstruction, vggt]
last_compiled: 2026-05-14
---

# SwiftVGGT

## Definition
SwiftVGGT is a training-free acceleration method for VGGT-based large-scale dense 3D reconstruction that reduces runtime while maintaining high reconstruction quality.

## Synthesis
### Source-grounded Highlights
- Targets kilometer-scale reconstruction bottlenecks in chunk alignment and loop closure.
- Replaces IRLS-heavy chunk alignment with reliability-guided sampling plus single-step Sim(3)-SVD.
- Removes external VPR dependency by transforming VGGT encoder tokens for loop detection.
- Reports strong quality-speed tradeoff (about 33% runtime of recent VGGT-based approaches).

### 中文版本
SwiftVGGT 的核心是“免训练提速”：通过替换 IRLS 对齐与外部 VPR 回环模块，在大规模场景中显著降低推理时间，同时保持高重建质量。

## Graph Connections
- [[Neural Rendering]]
- [[DGGT]]
- [[Method Comparison]]
- [[Simulation Technologies for Autonomous Driving]]

## Sources
- [[sources/papers/arXiv-2511.18290-SwiftVGGT-A-Scalable-Visual-Geometry-Grounded-Transformer-for-Large-Scale-Scenes]]
