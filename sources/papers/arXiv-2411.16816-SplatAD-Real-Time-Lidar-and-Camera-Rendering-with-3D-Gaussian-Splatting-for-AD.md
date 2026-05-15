---
title: SplatAD Real-Time Lidar and Camera Rendering with 3D Gaussian Splatting for Autonomous Driving
arxiv_id: 2411.16816v3
source: https://arxiv.org/abs/2411.16816
pdf: https://arxiv.org/pdf/2411.16816
project: https://research.zenseact.com/publications/splatad/
code: https://github.com/carlinds/splatad
date: 2025-03-13
Imported At: 2026-05-14
category: Autonomous Driving
related_project: sources/projects/SplatAD/SplatAD.md
---

# SplatAD: Real-Time Lidar and Camera Rendering with 3D Gaussian Splatting for Autonomous Driving

## One-paragraph summary
SplatAD proposes a unified 3D Gaussian Splatting framework for **real-time rendering of both camera and lidar** in dynamic autonomous-driving scenes. The paper argues that prior NeRF-based multimodal methods are realistic but too slow, while prior 3DGS automotive methods are fast but camera-only. SplatAD addresses this by introducing sensor-aware modeling and custom CUDA rasterization for lidar in spherical coordinates, plus rolling-shutter handling for both camera and lidar. Across three AD datasets, the authors report state-of-the-art quality and substantial speedups versus NeRF-based alternatives, with improved PSNR and order-of-magnitude faster rendering.

## Key ideas
1. Unified 3DGS representation for camera + lidar from the same scene graph (static background + dynamic actors).
2. Lidar-oriented rasterization in spherical coordinates for sparse, non-linear scan structure.
3. Sensor-specific feature modeling for lidar intensity and ray-drop behavior.
4. Rolling-shutter compensation for both modalities under ego-motion and dynamic scenes.
5. Efficiency-focused CUDA implementation to keep rendering practical for large-scale simulation.

## Novelty (What is new)
- **First 3DGS method claiming realistic real-time rendering for both camera and lidar** in dynamic AD scenes.
- **Purpose-built lidar rasterization pipeline** (instead of adapting camera-image rasterization or depth-image projection hacks).
- **Joint multimodal sensor realism in 3DGS**, including rolling shutter, intensity, and dropout effects.
- **Speed-quality bridge for AD simulation**: targets NeRF-like multimodal realism with substantially faster rendering.

## Why it matters
- Large-scale safety testing needs simulation that is both realistic and fast.
- Modern AD stacks rely heavily on lidar; camera-only simulators are incomplete.
- SplatAD directly targets practical deployment constraints in data-driven digital-twin simulation.

## Reported limitations/open challenges (from framing)
- Multimodal realism depends on careful sensor modeling and calibration quality.
- Dynamic-scene simulation still requires robust actor decomposition/tracking assumptions.
- The method focuses on rendering realism/efficiency; downstream closed-loop policy effects are outside paper scope.

## Source snippets used
- arXiv abstract page (`/abs/2411.16816`) for metadata, headline claims, and metrics.
- arXiv HTML (`/html/2411.16816v3`) for method framing and contribution list.
- Official repo README (`github.com/carlinds/splatad`) for implementation scope and release status.

---

# 中文版本

## 一段话总结
SplatAD 提出了一种面向自动驾驶动态场景的统一 3DGS 框架，可对**相机与激光雷达**进行实时渲染。论文指出：现有 NeRF 多模态方法真实感高但速度慢，现有 3DGS 自动驾驶方法速度快但大多只支持相机。SplatAD 通过传感器特化建模与面向雷达的 CUDA 光栅化（球坐标系）解决这一矛盾，并同时处理相机与雷达的 rolling shutter。作者在三个自动驾驶数据集上报告了 SOTA 质量和显著速度优势（相对 NeRF 为数量级加速）。

## 核心思想
1. 用统一 3DGS 场景表示同时渲染相机与雷达（静态背景 + 动态体）。
2. 在球坐标下进行稀疏雷达点云的专用光栅化。
3. 显式建模雷达强度与 ray dropout 等传感器特性。
4. 对相机与雷达同时进行 rolling shutter 补偿。
5. 通过 CUDA 工程优化保障大规模仿真所需效率。

## 创新点（这篇论文新在哪里）
- **首个宣称可在动态自动驾驶场景中实现相机+雷达实时 3DGS 渲染的方法**。
- **雷达专用渲染管线**：不是简单复用图像光栅化，也不是低效深度图投影替代。
- **多模态传感器现实性联合建模**：在 3DGS 中统一处理 rolling shutter、强度与掉点现象。
- **连接质量与速度鸿沟**：以更接近 NeRF 多模态真实感的目标，同时保持显著高吞吐。

## 重要性
- 自动驾驶安全验证要求“高真实 + 高速度”的仿真能力。
- 仅相机仿真无法覆盖现代 AD 系统关键感知模态（lidar）。
- SplatAD 面向实际工程可扩展性的痛点（大规模离线测试成本）。

## 论文框架中提到的局限/挑战
- 多模态效果仍依赖高质量标定与传感器建模细节。
- 动态场景效果仍受场景分解与目标轨迹质量影响。
- 论文主要关注渲染层，未直接覆盖闭环控制策略表现。

## 本页信息来源
- arXiv 摘要页（`/abs/2411.16816`）中的摘要与元数据。
- arXiv HTML 页（`/html/2411.16816v3`）中的方法与贡献描述。
- 官方代码仓库 README（`github.com/carlinds/splatad`）中的实现范围说明。
