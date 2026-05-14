---
title: Flow4DGS-SLAM Optical Flow-Guided 4D Gaussian Splatting SLAM
arxiv_id: 2604.22339v2
source: https://arxiv.org/abs/2604.22339
pdf: https://arxiv.org/pdf/2604.22339
project: https://wangys16.github.io/Flow4DGS-SLAM
date: 2026-04-28
Imported At: 2026-05-14
category: SLAM
---

# Flow4DGS-SLAM: Optical Flow-Guided 4D Gaussian Splatting SLAM

## One-paragraph summary
This paper proposes **Flow4DGS-SLAM**, a dynamic-scene SLAM framework that combines 3D/4D Gaussian Splatting with optical-flow guidance to jointly improve tracking, dynamic reconstruction, and efficiency. The key idea is to avoid treating all moving regions as outliers by explicitly separating static/dynamic Gaussians and modeling dynamic behavior over time. The method introduces camera-induced motion decomposition from depth + optical flow for category-agnostic dynamic masking and pose initialization, then uses a hybrid 4D Gaussian representation with explicit keyframe centers plus GMM-based temporal opacity/rotation. Dynamic Gaussian centers are propagated via scene-flow priors with adaptive insertion for newly appearing motion, targeting faster and more robust reconstruction in complex dynamic environments.

## Key ideas
1. **Camera-Induced Motion Decomposition**: fits a 6-DoF ego-motion model to flow/depth, derives rigid-flow residuals, and builds a category-agnostic dynamic mask.
2. **Flow-guided camera initialization**: reuses the decomposition module to provide cleaner pose initialization before optimization.
3. **Hybrid 4DGS dynamic representation**: explicit keyframe trajectories for centers + parametric (GMM) temporal opacity/rotation.
4. **Scene-flow propagation + adaptive insertion**: propagates dynamic Gaussians and inserts new ones when new motion regions appear.
5. **Efficiency-oriented design**: reduces dependence on expensive deformation-field training used by prior dynamic 4DGS-SLAM approaches.

## Novelty (What is new)
- **Category-agnostic motion segmentation inside SLAM tracking** via ego-motion-consistent rigid-flow decomposition, instead of relying purely on category-based dynamic masks.
- **A hybrid dynamic Gaussian parameterization** that mixes explicit time-varying centers with GMM temporal attributes, balancing expressiveness and speed.
- **Optical-flow-driven dynamic mapping modules** (scene-flow propagation + adaptive Gaussian insertion) to accelerate online dynamic reconstruction.
- **Unified dynamic SLAM pipeline** that simultaneously targets tracking robustness, dynamic reconstruction quality, and training/runtime efficiency.

## Why it matters
- Dynamic scenes are a core failure mode for many SLAM systems that assume static worlds.
- The paper offers a practical way to keep photorealistic Gaussian-map quality while improving robustness under moving objects.
- It advances the direction of real-time-capable dynamic 4D mapping for robotics/AR/autonomous scenarios.

## Reported limitations/open challenges (from framing)
- Dynamic SLAM remains hard in complex motion patterns (e.g., objects exiting/re-entering view).
- Robustness still depends on depth/optical-flow quality.
- The paper positions itself against expensive prior methods, implying efficiency-accuracy tradeoffs are still central.

## Source snippets used
- arXiv abstract page (`/abs/2604.22339`) for title, metadata, abstract claims.
- arXiv HTML (`/html/2604.22339v2`) for introduction, stated contributions, and methodology details.

---

# 中文版本

## 一段话总结
本文提出 **Flow4DGS-SLAM**：一个面向动态场景的 SLAM 框架，将 3D/4D Gaussian Splatting 与光流引导结合，以同时提升跟踪鲁棒性、动态重建能力和效率。核心思路是不再把所有动态区域都当作离群点，而是显式分离静态/动态高斯并进行时间建模。方法先利用深度 + 光流进行相机诱导运动分解，生成类别无关的动态掩码并提供位姿初始化；随后采用混合式 4D 高斯表示（关键帧显式中心 + GMM 时间不透明度/旋转），并通过场景流先验传播与自适应插入来处理动态高斯，从而在复杂动态环境中实现更高效的重建与跟踪。

## 核心思想
1. **相机诱导运动分解**：通过 6-DoF 自运动模型拟合光流/深度，利用刚性流残差做类别无关动态分割。
2. **光流引导位姿初始化**：将分解模块输出用于更稳定的相机初值估计。
3. **混合 4DGS 动态表示**：动态中心用关键帧显式轨迹表示，时间不透明度/旋转用 GMM 参数化。
4. **场景流传播 + 自适应插入**：传播已有动态高斯并为新出现运动区域插入新高斯。
5. **效率优先设计**：减少对高成本形变场训练的依赖。

## 创新点（这篇论文新在哪里）
- **在 SLAM 跟踪环节引入类别无关动态分割**：基于自运动一致性的刚性流分解，而非仅依赖类别语义分割。
- **提出混合动态高斯参数化**：显式时间中心 + GMM 时间属性，兼顾表达能力与训练/优化效率。
- **光流驱动的动态建图机制**：场景流传播与自适应高斯插入用于加速在线动态重建。
- **统一动态 SLAM 目标**：将跟踪、重建与效率优化整合在一个 pipeline 中。

## 重要性
- 动态场景是传统“静态假设”SLAM 的主要失效来源之一。
- 该方法提供了在动态环境下保持高质量高斯渲染与稳定跟踪的可行路线。
- 对机器人、AR/VR、自动驾驶等需要实时动态建图的任务具有实际价值。

## 论文框架中提到的局限/挑战
- 面对复杂动态模式（如目标离开/重入视野）仍然困难。
- 结果对深度与光流先验质量存在依赖。
- 效率与精度之间仍需权衡。

## 本页信息来源
- arXiv 摘要页（`/abs/2604.22339`）中的摘要与元数据。
- arXiv HTML 页（`/html/2604.22339v2`）中的引言、贡献与方法描述。
