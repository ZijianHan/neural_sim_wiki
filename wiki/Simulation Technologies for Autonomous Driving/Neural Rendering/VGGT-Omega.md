---
tags:
  - vggt
  - feed-forward-reconstruction
  - scaling
last_compiled: 2026-05-15
---

# VGGT-Ω

## Definition
VGGT-Ω is the scaled successor to VGGT, demonstrating predictable quality scaling with model and data size for feed-forward 3D reconstruction of both static and dynamic scenes.

## Synthesis
### Source-grounded Highlights
- Replaces expensive global cross-frame attention with **register attention**: scene information aggregated into compact register tokens, inter-frame exchange restricted to registers only.
- Simplifies architecture to a **single dense prediction head** with multi-task supervision, removing high-resolution convolutional layers.
- Uses only ~30% of VGGT's GPU memory during training → enables 15× more supervised data and vast unlabeled video via self-supervised learning.
- Achieves 77% improvement over previous best camera estimation accuracy on Sintel.
- Learned registers shown useful for vision-language-action (VLA) models and language alignment.

### Position in VGGT Lineage
| Variant | Focus | Training Required |
|---|---|---|
| **VGGT-Ω** | Quality scaling (static + dynamic) | Yes (large-scale) |
| TurboVGGT | Adaptive efficiency | Yes (learned sparsity) |
| SwiftVGGT | Runtime acceleration | No (training-free) |
| OVGGT | Streaming under fixed VRAM | No (training-free) |
| PanoVGGT | Panoramic input | Yes |

### 中文版本
VGGT-Ω 是 VGGT 的规模化后续版本。通过寄存器注意力、单一密集预测头和自监督学习，训练显存降至前代的30%，支持15倍更多数据。在静态/动态重建上达到SOTA，并展示寄存器可迁移至VLA模型。

## Graph Connections
- [[Gaussian Splatting]]
- [[Neural Rendering]]
- [[TurboVGGT]]
- [[SwiftVGGT]]
- [[OVGGT]]
- [[PanoVGGT]]
- [[Method Comparison]]

## Sources
- [[sources/papers/arXiv-2605.15195-VGGT-Omega-Scaling-Feed-Forward-Reconstruction]]
