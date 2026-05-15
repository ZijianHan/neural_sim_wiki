---
title: "Efficient Dense Matching for Enhanced Gaussian Splatting Using AV1 Motion Vectors"
arxiv_id: 2605.14629v1
source: http://arxiv.org/abs/2605.14629v1
pdf: http://arxiv.org/pdf/2605.14629v1
project: https://sigmedia.tv/AV1-3DGS.github.io/
date: 2026-05-14
Imported At: 2026-05-15
category: Gaussian Splatting
---

# Efficient Dense Matching for Enhanced Gaussian Splatting Using AV1 Motion Vectors
## One-paragraph summary

This work introduces an AV1 video codec-based feature detection and matching pipeline for SfM that significantly reduces computational overhead while producing denser point clouds (up to 8× more points than classical SfM). By leveraging motion vectors inherent to the AV1 codec, it bypasses expensive exhaustive matching. The enhanced initialization directly improves 3DGS performance: +9 VMAF and 63% average reduction in training time to reach baseline quality.

## Key ideas

- **Codec motion vectors as features**: AV1 motion vectors replace expensive feature matching for SfM.
- **Denser initialization**: 8× more points than COLMAP-based classical SfM.
- **Faster SfM**: bypasses computationally expensive exhaustive matching.
- **Direct 3DGS improvement**: better init → faster convergence and higher quality.

## Novelty (What is new)

- Using video codec (AV1) motion vectors for SfM feature matching — novel cross-domain transfer.
- Demonstrating that codec-derived dense point clouds directly improve 3DGS training efficiency.
- 63% training time reduction with quality improvement (+9 VMAF).

## Why it matters

Addresses a key bottleneck in the 3DGS pipeline (SfM initialization quality and speed). Particularly relevant for video-based driving scene reconstruction where footage is already encoded. Could integrate with any 3DGS method tracked in this wiki.

## Reported limitations/open challenges (from framing)

- Depends on AV1-encoded input (not applicable to raw camera feeds without transcoding).
- Geometric robustness claims need validation beyond reported benchmarks.

## Source snippets used

- arXiv abstract 2605.14629v1

---

## 一段话总结

利用AV1视频编解码器的运动向量进行SfM特征检测和匹配，绕过昂贵的穷举匹配，生成比经典SfM密8倍的点云。更密集的初始化直接提升3DGS性能：VMAF提高9分，训练时间减少63%。

## 核心思想

- 编解码器运动向量替代传统特征匹配。
- 更密集初始化→更快收敛和更高质量。

## 创新点（这篇论文新在哪里）

- 视频编解码器运动向量用于SfM的跨领域迁移。
- 编解码器衍生点云直接改善3DGS训练效率。

## 重要性

解决3DGS管线的关键瓶颈（SfM初始化），对视频驾驶场景重建特别相关。

## 论文框架中提到的局限/挑战

- 依赖AV1编码输入；几何鲁棒性需更多验证。

## 本页信息来源

- arXiv摘要 2605.14629v1
