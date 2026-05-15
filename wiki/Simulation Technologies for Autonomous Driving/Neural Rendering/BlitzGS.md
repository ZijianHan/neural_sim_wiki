---
tags: [gaussian-splatting, scalability, distributed, city-scale]
last_compiled: 2026-05-15
---

# BlitzGS

## Definition
BlitzGS is a distributed 3DGS framework that enables city-scale reconstruction in tens of minutes through three-level workload reduction.

## Synthesis
### Source-grounded Highlights
- **System level — index-parity sharding**: distributes Gaussians across GPUs by index rather than spatial blocks, eliminating cross-block visibility redundancy. Single cross-GPU exchange routes projected Gaussians to tile owners.
- **Model level — importance scoring**: scheduled passes shrink the global Gaussian population; per-Gaussian visibility weights bias density control toward contributing primitives.
- **View level — LOD gate + culling**: distance-based LOD excludes overly fine primitives; importance-based mask skips negligible cross-view contributions.
- Matches rendering quality of recent large-scale baselines with order-of-magnitude training speedup.
- Code available: https://github.com/AkierRaee/BlitzGS

### Key Insight
Spatial partitioning (used by prior large-scale methods) causes redundancy because Gaussians near block boundaries must be replicated. Index-parity sharding avoids this entirely.

### 中文版本
BlitzGS 通过三级工作负载缩减实现城市尺度3DGS数十分钟训练。系统级索引奇偶分片消除跨块冗余，模型级重要性评分精简全局高斯数量，视图级LOD门控和剔除减少每帧渲染负载。质量匹配基线，训练加速一个数量级。

## Graph Connections
- [[Gaussian Splatting]]
- [[SplatAD]]
- [[gsplat]]
- [[Method Comparison]]

## Sources
- [[sources/papers/arXiv-2605.13794-BlitzGS-City-Scale-Gaussian-Splatting-at-Lightning-Speed]]
