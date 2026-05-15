---
title: "PanoVGGT: Feed-Forward 3D Reconstruction from Panoramic Imagery"
arxiv_id: 2603.17571v1
source: https://arxiv.org/abs/2603.17571
pdf: https://arxiv.org/pdf/2603.17571
project: null
code: https://github.com/YijingGuo-June/PanoVGGT
date: 2026-03-18
Imported At: 2026-05-15
category: Panoramic 3D Reconstruction
tags: [panoramic-vision, feedforward-reconstruction, vggt, transformer, spherical-geometry, depth-pose-joint-learning]
related_project: sources/projects/PanoVGGT/PanoVGGT.md
---

# PanoVGGT: Feed-Forward 3D Reconstruction from Panoramic Imagery

## One-paragraph summary
PanoVGGT is a feed-forward, permutation-equivariant Transformer for panoramic multi-view reconstruction that jointly predicts camera poses, dense depth, and globally consistent point clouds from one or multiple unordered panoramas in a single pass. To adapt VGGT-style geometry learning to equirectangular inputs, it introduces spherical-aware positional embeddings, panorama-specific tri-axis SO(3) augmentation, and stochastic anchoring for stable global-frame training under unordered inputs. The paper also introduces PanoCity, a large-scale outdoor panoramic dataset (120k+ frames) with aligned RGB, 16-bit depth, and 6-DoF pose supervision, and reports competitive/strong performance and cross-domain robustness on panoramic benchmarks.

## Key ideas
1. Use a permutation-equivariant feed-forward architecture to jointly infer pose, depth, and point clouds from unordered panoramas.
2. Replace standard ViT positional signals with spherical-aware sin/cos angular embeddings to handle wrap-around and non-pinhole distortion.
3. Apply physically valid three-axis SO(3) augmentation jointly to RGB/depth/pose to improve rotation robustness in panoramic geometry learning.
4. Use stochastic anchoring during training to address global-frame ambiguity without introducing fixed-view ordering bias.
5. Provide PanoCity as a large-scale panoramic supervision source for joint geometric learning.

## Novelty (What is new)
- **Panoramic adaptation of VGGT-style feed-forward geometry**: a unified pose-depth-point model tailored to spherical imagery rather than pinhole assumptions.
- **Spherical-aware positional encoding + tri-axis SO(3) panorama augmentation**: an explicit design for robust geometric reasoning under equirectangular distortions.
- **Stochastic anchoring for unordered panoramic sets**: resolves global-frame ambiguity while preserving permutation equivariance.
- **PanoCity dataset**: large-scale, trajectory-structured outdoor panoramas with synchronized RGB-depth-pose annotations for multi-view feed-forward training.

## Why it matters
- Panoramic cameras are increasingly practical, but most feed-forward reconstruction pipelines are perspective-centric and degrade on 360-degree imagery.
- The paper advances a direct panoramic route that avoids crop-and-stitch pipelines and their seam/drift artifacts.
- It contributes both method and data, which is important for improving reproducibility and future panoramic geometry benchmarks.

## Reported limitations/open challenges (from framing)
- arXiv abstract still states code/dataset "will be released"; repository release state has evolved separately from this statement.
- The conclusion states pose estimation is still sensitive under extreme domain shift.
- The current design is tailored to equirectangular projections; transfer to other panorama parameterizations is not established in the paper text.

## Source snippets used
- arXiv abstract page (`/abs/2603.17571`) for metadata, abstract claims, and release status.
- arXiv HTML (`/html/2603.17571v1`) for method details (spherical embedding, SO(3) augmentation, stochastic anchoring), dataset framing, and conclusion limitations.
- Official repository README (`github.com/YijingGuo-June/PanoVGGT`) for released code/weights/dataset status.

---

# 中文版本

## 一段话总结
PanoVGGT 是一个面向全景图像的前馈式、置换等变 Transformer 框架，可从一个或多个无序全景输入中，在单次前向传播内联合预测相机位姿、稠密深度和全局一致点云。为适配等距柱状投影的球面几何特性，论文引入球面感知位置编码、全景三轴 SO(3) 旋转增强和随机锚定训练策略，以缓解非针孔畸变与全局坐标系歧义。同时提出 PanoCity 大规模户外全景数据集（12 万+ 帧，含对齐 RGB/16-bit 深度/6DoF 位姿），并报告了较强的精度与跨域泛化表现。

## 核心思想
1. 用置换等变的前馈框架，从无序全景集合联合预测位姿、深度与点云。
2. 用球面角度 sin/cos 位置编码替代常规 ViT 位置编码，处理全景缝合边界与畸变问题。
3. 对 RGB/深度/位姿同步施加三轴 SO(3) 旋转增强，提升球面几何鲁棒性。
4. 采用随机锚定策略，在保持无序输入等变性的同时稳定全局坐标学习。
5. 构建 PanoCity 数据集，为全景多视图前馈重建提供大规模监督。

## 创新点（这篇论文新在哪里）
- **将 VGGT 式前馈几何学习系统化扩展到全景域**：统一建模位姿-深度-点云，而非沿用针孔相机假设。
- **球面位置编码 + 三轴 SO(3) 旋转增强**：针对等距柱状全景畸变的专门几何设计。
- **随机锚定训练机制**：在无序全景输入下处理全局坐标系歧义并保持置换等变。
- **PanoCity 数据集**：提供大规模、轨迹连续且标注完整的全景 RGB-深度-位姿训练数据。

## 重要性
- 全景感知设备应用增长明显，但主流前馈重建方法仍以透视图为中心，迁移到 360 场景效果受限。
- 该工作提供“直接全景建模”路线，减少切块拼接方案导致的接缝和几何漂移问题。
- 同时贡献方法与数据，有助于推动全景几何重建的可复现研究与统一评测。

## 论文框架中提到的局限/挑战
- arXiv 摘要仍写作“代码/数据将发布”；与后续仓库发布节奏存在时间差。
- 结论中明确指出：在极端域偏移下，位姿估计仍然敏感。
- 当前设计主要针对等距柱状全景投影，对其他全景参数化方式的泛化尚未明确验证。

## 本页信息来源
- arXiv 摘要页（`/abs/2603.17571`）中的元数据、摘要与发布状态。
- arXiv HTML 页（`/html/2603.17571v1`）中的方法细节、数据集描述与结论限制说明。
- 官方仓库 README（`github.com/YijingGuo-June/PanoVGGT`）中的代码/权重/数据发布更新。
