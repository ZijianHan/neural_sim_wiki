---
title: "BlitzGS: City-Scale Gaussian Splatting at Lightning Speed"
arxiv_id: 2605.13794v1
source: http://arxiv.org/abs/2605.13794v1
pdf: http://arxiv.org/pdf/2605.13794v1
code: https://github.com/AkierRaee/BlitzGS
date: 2026-05-13
Imported At: 2026-05-15
category: Gaussian Splatting
---

# BlitzGS: City-Scale Gaussian Splatting at Lightning Speed
## One-paragraph summary

BlitzGS is a distributed 3DGS framework that enables city-scale reconstruction in tens of minutes by reducing active Gaussian workload at three levels: system (index-parity sharding across GPUs instead of spatial blocks), model (scheduled importance scoring to shrink global population), and view (distance-based LOD gate + importance-based culling). Matches rendering quality of recent large-scale baselines with an order-of-magnitude training speedup.

## Key ideas

- **Index-parity sharding**: distributes Gaussians across GPUs by index rather than spatial blocks, eliminating cross-block visibility redundancy.
- **Single cross-GPU exchange**: routes projected Gaussians to tile owners per rendering step.
- **Importance scoring passes**: per-Gaussian visibility weights bias density control toward contributing primitives.
- **Distance-based LOD gate**: excludes overly fine primitives for current frustum.
- **Importance-based culling mask**: skips Gaussians with negligible cross-view contribution.

## Novelty (What is new)

- Index-parity (non-spatial) GPU sharding for 3DGS — avoids redundancy of spatial partitioning.
- Three-level workload reduction (system/model/view) in a unified framework.
- City-scale 3DGS training in tens of minutes (order-of-magnitude over prior work).

## Why it matters

Directly relevant to large-scale driving scene reconstruction. City-scale training in minutes enables rapid iteration for AD simulation data, complementing methods like SplatAD and PointForward already in the wiki.

## Reported limitations/open challenges (from framing)

- Quality "matches" but does not exceed baselines — workload reduction may have quality ceiling.
- Multi-GPU requirement for city-scale (not single-GPU friendly).

## Source snippets used

- arXiv abstract 2605.13794v1

---

## 一段话总结

BlitzGS是一个分布式3DGS框架，通过三级工作负载缩减（系统级索引奇偶分片、模型级重要性评分、视图级LOD门控和剔除）实现城市尺度重建在数十分钟内完成，质量匹配现有基线同时训练加速一个数量级。

## 核心思想

- 索引奇偶分片（非空间分块）消除跨块冗余。
- 三级工作负载缩减：系统/模型/视图。
- 重要性评分引导密度控制。

## 创新点（这篇论文新在哪里）

- 非空间GPU分片策略。
- 统一三级框架实现城市尺度数十分钟训练。

## 重要性

为驾驶场景大规模重建提供快速迭代能力，补充wiki中的SplatAD和PointForward。

## 论文框架中提到的局限/挑战

- 质量匹配但未超越基线；需要多GPU。

## 本页信息来源

- arXiv摘要 2605.13794v1
