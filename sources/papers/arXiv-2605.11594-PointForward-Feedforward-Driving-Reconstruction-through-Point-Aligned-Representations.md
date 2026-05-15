---
title: "PointForward: Feedforward Driving Reconstruction through Point-Aligned Representations"
arxiv_id: 2605.11594v1
source: https://arxiv.org/abs/2605.11594
pdf: https://arxiv.org/pdf/2605.11594
project: https://wm-research.github.io/PointForward
code: null
date: 2026-05-12
Imported At: 2026-05-15
category: Gaussian Splatting
tags: [gaussian-splatting, feedforward, point-aligned, scene-graph, dynamic-reconstruction, autonomous-driving]
---

# PointForward: Feedforward Driving Reconstruction through Point-Aligned Representations

## One-paragraph summary
PointForward proposes a **point-aligned feedforward** framework for driving-scene reconstruction that replaces the per-pixel Gaussian prediction paradigm with sparse 3D queries initialized in world space. Multi-view image features are aggregated onto these queries via spatial-temporal fusion, enforcing explicit cross-view consistency in a single forward pass. For dynamic scenes, a scene-graph formulation uses 3D bounding boxes to organize moving instances into canonical spaces, enabling instance-level motion propagation and temporally coherent dynamic representations. The method reports SOTA results on Waymo (28.48 PSNR global, 25.01 dynamic) and nuScenes (26.54 PSNR zero-shot), and explicitly positions itself as an improvement over pixel-aligned methods like DGGT.

## Key ideas
1. Replace per-pixel Gaussian prediction with sparse 3D queries in world space (point-aligned representation).
2. Project queries onto all input views to aggregate image features and geometric cues via spatial-temporal fusion.
3. Use scene graphs with 3D bounding boxes to model dynamic instances in canonical space, enabling instance-level motion propagation.
4. Single feedforward pass produces globally consistent, view-coherent 3D Gaussians without per-scene optimization.
5. Avoids redundant Gaussians and layering artifacts inherent to pixel-aligned methods.

## Novelty (What is new)
- **Point-aligned formulation** for feedforward 3DGS: sparse world-space queries replace per-pixel prediction, eliminating multi-view inconsistency by design.
- **Scene-graph-based dynamic modeling** in a feedforward setting: borrowed from per-scene methods (OmniRe, Street Gaussians) but made to work without optimization.
- **Instance-level motion consistency** via canonical-space transforms, replacing dense flow prediction used by DGGT/STORM.
- First feedforward method to demonstrate strong results on large-scale driving with point-unaligned Gaussians (prior pixel-unaligned works limited to indoor scenes).

## Why it matters
- Directly addresses the two main weaknesses of DGGT/STORM-style pixel-aligned feedforward methods: redundant Gaussians and incoherent dynamic motion.
- Demonstrates that sparse query-based representations (inspired by 3D detection) can scale to large outdoor driving reconstruction.
- Achieves SOTA on Waymo and nuScenes with clean qualitative results under novel viewpoints.
- From Xiaomi EV — same lab as DGGT, indicating an evolution of their reconstruction pipeline.

## Reported limitations/open challenges (from framing)
- Code not yet released ("will be available upon publication").
- Relies on 3D bounding box annotations for dynamic instance modeling — requires a detection/tracking pipeline as input.
- Performance on very sparse input views or extreme occlusion not extensively discussed.
- Waymo/nuScenes focus; cross-domain robustness to other datasets (e.g., Argoverse2) not evaluated.

## Source snippets used
- arXiv abstract page (`/abs/2605.11594`) for metadata and abstract.
- arXiv HTML (`/html/2605.11594v1`) for introduction, related work, method overview, and contribution list.
- Project page (`wm-research.github.io/PointForward`) for affiliation and pipeline figure context.

---

# 中文版本

## 一段话总结
PointForward 提出了一种基于"点对齐表示"的前馈式驾驶场景重建框架。与逐像素预测高斯（如 DGGT）不同，该方法在世界坐标下初始化稀疏 3D 查询点，通过空间-时间融合从多视图聚合图像特征，在单次前向传播中实现跨视图一致性。动态建模方面引入场景图结构，利用 3D 边界框将运动实例组织到规范空间中，实现实例级运动传播和时序一致的动态表示。在 Waymo 和 nuScenes 上取得 SOTA。

## 核心思想
1. 用世界空间稀疏 3D 查询替代逐像素高斯预测（点对齐表示）。
2. 查询点投影到所有输入视图，通过空间-时间融合聚合特征与几何线索。
3. 场景图 + 3D 边界框建模动态实例的规范空间运动。
4. 单次前向传播生成全局一致、视图协调的 3D 高斯表示。
5. 避免像素对齐方法常见的冗余高斯和分层伪影。

## 创新点（这篇论文新在哪里）
- 首次将点对齐（非像素对齐）前馈 3DGS 扩展到大规模驾驶场景重建。
- 在前馈设置中引入场景图动态建模（借鉴逐场景优化方法但无需优化）。
- 用规范空间变换实现实例级运动一致性，替代 DGGT/STORM 的稠密光流预测。
- 来自小米汽车（与 DGGT 同一实验室），代表其重建流水线的迭代演进。

## 重要性
- 直接解决 DGGT/STORM 类方法的两大弱点：冗余高斯与动态运动不一致。
- 证明稀疏查询表示（受 3D 检测启发）可扩展至大规模户外驾驶重建。
- 在 Waymo 和 nuScenes 上取得 SOTA，新视角下定性结果优秀。

## 论文框架中提到的局限/挑战
- 代码尚未发布。
- 动态建模依赖 3D 边界框标注，需要检测/跟踪流水线作为输入。
- 对极端稀疏输入或严重遮挡场景的性能未充分讨论。
- 仅在 Waymo/nuScenes 上验证，未评估跨域泛化到其他数据集（如 Argoverse2）。

## 本页信息来源
- arXiv 摘要页 (`/abs/2605.11594`)
- arXiv HTML (`/html/2605.11594v1`)
- 官方项目页 (`wm-research.github.io/PointForward`)
