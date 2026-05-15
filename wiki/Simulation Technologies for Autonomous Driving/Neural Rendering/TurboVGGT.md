---
tags:
  - vggt
  - efficiency
  - feed-forward-reconstruction
last_compiled: 2026-05-15
---

# TurboVGGT

## Definition
TurboVGGT is an efficient visual geometry transformer that uses adaptive alternating attention (adaptive sparse global + local frame attention) for fast multi-view 3D reconstruction.

## Synthesis
### Source-grounded Highlights
- **Adaptive sparse global attention**: learns representative tokens with varying sparsity levels per frame and layer (unlike fixed-ratio methods).
- **Alternating pattern**: sparse global attention captures cross-frame geometry; frame attention aggregates local details.
- **End-to-end trainable**: no separate modules or post-processing.
- Achieves fast reconstruction while maintaining competitive quality on multiple benchmarks.

### Comparison with Other VGGT Variants
- SwiftVGGT: training-free acceleration (fixed heuristics).
- TurboVGGT: **learned** adaptive sparsity (data-driven efficiency).
- OVGGT: streaming focus with fixed budget.

### 中文版本
TurboVGGT 采用自适应交替注意力实现高效多视角3D重建。结合自适应稀疏全局注意力（跨帧）和帧注意力（局部），在保持质量的同时加速推理。区别于SwiftVGGT的固定启发式，TurboVGGT的稀疏度是学习得到的。

## Graph Connections
- [[VGGT-Omega]]
- [[SwiftVGGT]]
- [[OVGGT]]
- [[Neural Rendering]]
- [[Method Comparison]]

## Sources
- [[sources/papers/arXiv-2605.14315-TurboVGGT-Fast-Visual-Geometry-Reconstruction-with-Adaptive-Alternating-Attention]]
