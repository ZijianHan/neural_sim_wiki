---
tags: [ovggt, streaming, vggt, geometric-foundation-model]
last_compiled: 2026-05-14
---

# OVGGT

## Definition
OVGGT is a training-free constant-cost streaming framework for visual geometry transformers that bounds memory and per-step compute for long video 3D reconstruction.

## Synthesis
### Source-grounded Highlights
- Targets the KV-cache growth bottleneck in causal-attention streaming reconstruction.
- Uses Self-Selective Caching (SSC) to keep cache size within a fixed budget.
- Uses Dynamic Anchor Protection (DAP) to protect coordinate-critical tokens and reduce geometric drift.
- Reports fixed-VRAM long-sequence processing with strong geometric accuracy across indoor, outdoor, and ultra-long benchmarks.

### 中文版本
OVGGT 的核心是“常数资源流式重建”：通过 SSC 控制缓存预算、DAP 保护关键锚点，在长序列下维持固定显存与稳定几何质量。

## Graph Connections
- [[Neural Rendering]]
- [[SwiftVGGT]]
- [[Method Comparison]]
- [[Simulation Technologies for Autonomous Driving]]

## Sources
- [[sources/papers/arXiv-2603.05959-OVGGT-O1-Constant-Cost-Streaming-Visual-Geometry-Transformer]]
