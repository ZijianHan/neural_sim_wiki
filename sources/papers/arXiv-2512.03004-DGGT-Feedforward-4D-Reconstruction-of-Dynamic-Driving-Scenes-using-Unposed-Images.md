---
title: DGGT Feedforward 4D Reconstruction of Dynamic Driving Scenes using Unposed Images
arxiv_id: 2512.03004v1
source: https://arxiv.org/abs/2512.03004
pdf: https://arxiv.org/pdf/2512.03004
project: https://xiaomi-research.github.io/dggt/
code: https://github.com/xiaomi-research/dggt
date: 2025-12-02
Imported At: 2026-05-14
category: Autonomous Driving
related_project: sources/projects/DGGT.md
---

# DGGT: Feedforward 4D Reconstruction of Dynamic Driving Scenes using Unposed Images

## One-paragraph summary
DGGT proposes a **pose-free, feedforward** framework for dynamic driving-scene 4D reconstruction directly from sparse **unposed** RGB images. Instead of requiring camera poses or per-scene optimization, DGGT predicts camera parameters, per-frame Gaussian maps, dynamics, and motion in a single pass, then uses lifespan-based temporal visibility modeling and diffusion-based refinement to improve interpolation and novel-view quality. The paper positions this as a scalable alternative for autonomous-driving reconstruction/re-simulation, reporting strong accuracy-speed tradeoffs and zero-shot cross-dataset generalization across Waymo, nuScenes, and Argoverse2.

## Key ideas
1. Treat camera pose as a **model output**, not an input prerequisite.
2. Predict per-frame pixel-aligned Gaussian maps with temporal lifespan modulation.
3. Use dedicated dynamic/motion heads to disentangle moving content and support interpolation.
4. Apply diffusion-based refinement to reduce ghosting/disocclusion artifacts.
5. Keep the whole pipeline feedforward for fast reconstruction without per-scene fitting.

## Novelty (What is new)
- **Pose-free feedforward formulation** for dynamic driving-scene 4D reconstruction from unposed inputs.
- **Unified single-pass prediction** of camera parameters + dynamic Gaussian representation + motion.
- **Lifespan-based temporal visibility modeling** to stabilize temporal consistency.
- **Diffusion-assisted interpolation refinement** integrated with Gaussian rendering output.
- **Scalability claim to arbitrary view counts / longer sequences** without scene-specific optimization.

## Why it matters
- Directly targets a bottleneck in AD simulation pipelines: expensive scene-by-scene optimization.
- Improves practicality for large-scale reconstruction as a routine preprocessing step.
- Supports both in-domain training and cross-dataset transfer scenarios.

## Reported limitations/open challenges (from framing)
- Dynamic-scene quality still depends on reliable motion disentanglement and interpolation quality.
- Feedforward methods must balance speed with long-range temporal stability.
- Cross-domain robustness remains data- and distribution-dependent despite strong reported transfer.

## Source snippets used
- arXiv abs page (`/abs/2512.03004`) for abstract/metadata and headline claims.
- arXiv HTML (`/html/2512.03004v1`) for method framing, architecture overview, and contribution narrative.
- Official repository README (`github.com/xiaomi-research/dggt`) for implementation/release context.

---

# 中文版本

## 一段话总结
DGGT 提出一种**无位姿（pose-free）、前馈式（feedforward）**的动态驾驶场景 4D 重建框架，可直接从稀疏的未标定位姿图像输入中完成重建。与依赖位姿先验或逐场景优化的方法不同，DGGT 在单次前向中联合预测相机参数、每帧高斯表示、动态信息与运动，并通过 lifespan 时间可见性建模和 diffusion 细化减少插帧伪影、提升新视角质量。论文将其定位为面向自动驾驶大规模重建/重仿真的可扩展方案，并报告了在 Waymo、nuScenes、Argoverse2 上的准确率-速度优势与跨数据集泛化能力。

## 核心思想
1. 将相机位姿从“输入条件”改为“模型输出”。
2. 预测带时间 lifespan 的逐帧高斯图表示。
3. 通过 dynamic/motion 分支显式建模运动与动态区域。
4. 使用 diffusion 细化缓解 ghosting 与遮挡插值伪影。
5. 采用前馈范式，避免逐场景优化。

## 创新点（这篇论文新在哪里）
- **无位姿前馈动态重建范式**：直接从 unposed 图像重建 4D 动态场景。
- **单次前向统一输出**：相机参数 + 动态高斯 + 运动联合预测。
- **lifespan 时间可见性机制**：提升时间一致性与动态分解稳定性。
- **Diffusion 插值细化模块**：改善稀疏输入下的插帧与视图质量。
- **面向长序列/多视角扩展**：强调无需逐场景优化的可扩展重建流程。

## 重要性
- 直击自动驾驶仿真链路中的重建成本瓶颈。
- 有助于将 4D 重建从“昂贵离线步骤”推进为“常规预处理能力”。
- 兼顾跨数据集泛化与目标域适配需求。

## 论文框架中提到的局限/挑战
- 动态建模质量仍受运动估计与时序插值可靠性影响。
- 前馈方法在速度与长时序稳定性之间仍存在权衡。
- 跨域泛化仍受数据分布差异影响。

## 本页信息来源
- arXiv 摘要页（`/abs/2512.03004`）中的摘要与元数据。
- arXiv HTML 页（`/html/2512.03004v1`）中的方法与贡献描述。
- 官方仓库 README（`github.com/xiaomi-research/dggt`）中的实现与发布说明。
