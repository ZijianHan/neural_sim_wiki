---
title: 3D Gaussian Splatting for Real-Time Rendering of Radiance Fields
arxiv_id: 2308.04079v1
source: https://arxiv.org/abs/2308.04079
pdf: https://arxiv.org/pdf/2308.04079
project: https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/
venue: ACM Transactions on Graphics 42(4), 2023
date: 2023-08-08
Imported At: 2026-05-14
category: Gaussian Splatting
related_project: sources/projects/gsplat/gsplat.md
---

# 3D Gaussian Splatting for Real-Time Rendering of Radiance Fields

## One-paragraph summary
This paper introduces **3D Gaussian Splatting (3DGS)**, a scene representation and rendering pipeline that achieves high-quality novel-view synthesis with real-time rendering at 1080p. Instead of dense neural field evaluation, the method represents scenes as optimized anisotropic 3D Gaussians initialized from sparse calibration points, then combines interleaved optimization/density control with a fast visibility-aware splatting renderer. The result is a strong quality-speed balance: state-of-the-art visual quality while enabling display-rate rendering where prior methods struggled on full unbounded scenes.

## Key ideas
1. Represent scene content with **anisotropic 3D Gaussians** instead of costly per-sample neural network queries.
2. Start from sparse SfM/calibration points and **densify/optimize Gaussians interleaved** during training.
3. Optimize covariance (shape/orientation) to better fit geometry and appearance.
4. Use a **visibility-aware splatting renderer** for fast training and real-time inference.
5. Focus on full scenes (not only isolated objects) and practical high-resolution rendering.

## Novelty (What is new)
- **A Gaussian-first radiance-field alternative** that avoids heavy MLP/ray-marching costs while keeping high fidelity.
- **Interleaved density control + anisotropic covariance optimization** as a core training mechanism.
- **Fast visibility-aware anisotropic splatting pipeline** enabling practical real-time 1080p rendering.
- **Quality-speed frontier shift** for unbounded scene novel-view synthesis.

## Why it matters
- Established Gaussian Splatting as a major rendering paradigm after NeRF.
- Opened the door to many fast/interactive 3D reconstruction and simulation pipelines.
- Became a practical foundation for later systems (e.g., gsplat, automotive 3DGS variants, hybrid rasterization/tracing lines).

## Reported limitations/open challenges (from framing)
- Real-time quality still depends on effective density control and optimization quality.
- Method design emphasizes speed-quality tradeoffs; scenario-specific realism extensions (sensor effects, lighting complexity) require follow-up work.
- Representation scalability and memory/runtime constraints remain implementation-sensitive in large scenes.

## Source snippets used
- arXiv abstract page (`/abs/2308.04079`) for abstract and metadata.
- Official project page link from arXiv comments for paper context.

---

# 中文版本

## 一段话总结
本文提出 **3D Gaussian Splatting（3DGS）**，通过“各向异性 3D 高斯表示 + 可见性感知光栅化渲染”，在 1080p 下实现高质量实时新视角合成。方法以相机标定阶段得到的稀疏点为起点，采用交替进行的优化与密度控制来逐步完善高斯集合，并重点优化高斯协方差以准确表达场景结构与外观。相比传统高成本神经场采样，3DGS 在完整无界场景上显著推进了“质量-速度”边界。

## 核心思想
1. 用**各向异性 3D 高斯**替代高成本神经网络体渲染查询。
2. 从稀疏重建点出发，训练中交替执行**优化与增删密度控制**。
3. 显式优化协方差（尺度/方向）提升几何与外观拟合能力。
4. 采用**可见性感知 splatting 渲染器**提升训练与推理效率。
5. 面向完整场景与高分辨率实时渲染目标。

## 创新点（这篇论文新在哪里）
- **以高斯为核心的辐射场替代路径**：在保持高保真同时显著降低推理成本。
- **密度控制与各向异性协方差联合优化**：形成有效的场景表示学习机制。
- **可见性感知的快速 splatting 渲染**：支撑 1080p 实时显示级别。
- **在无界场景上推动质量-速度前沿**。

## 重要性
- 奠定了 Gaussian Splatting 作为主流渲染路线之一的基础。
- 推动了大量后续高效 3D 重建/仿真系统的发展。
- 为 gsplat 等工程化库提供了核心方法来源。

## 论文框架中提到的局限/挑战
- 实时与质量仍依赖密度控制和优化策略细节。
- 更复杂传感器/光照真实性需后续工作扩展。
- 大场景下内存与效率表现与实现细节高度相关。

## 本页信息来源
- arXiv 摘要页（`/abs/2308.04079`）中的摘要与元数据。
- arXiv 提供的官方项目页链接上下文。
