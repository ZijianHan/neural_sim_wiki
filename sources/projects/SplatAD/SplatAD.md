---
title: SplatAD Project
source: https://github.com/carlinds/splatad
project_page: https://research.zenseact.com/publications/splatad/
Imported At: 2026-05-14
category: Autonomous Driving
tags: [gaussian-splatting, lidar, camera, real-time-rendering, autonomous-driving]
related_papers:
  - "sources/papers/arXiv-2411.16816-SplatAD-Real-Time-Lidar-and-Camera-Rendering-with-3D-Gaussian-Splatting-for-AD.md"
---

# SplatAD Project Summary

## One-paragraph summary
The `carlinds/splatad` repository is the official codebase for the CVPR 2025 SplatAD paper, focused on efficient multimodal rendering for autonomous driving using 3D Gaussian Splatting. The project extends `gsplat` with lidar-specific rasterization and rolling-shutter compensation for both camera and lidar, targeting practical real-time simulation pipelines. The README notes that core rendering components are present in this repo, while full model-level components (e.g., dataloading and decoders) are planned through `neurad-studio`.

## Key project features
1. Efficient lidar rendering with spherical-coordinate projection and non-linear-grid rasterization.
2. Rolling-shutter compensation for both camera and lidar paths.
3. CUDA-accelerated rendering entry points for camera and lidar in `gsplat/rendering.py`.
4. Example notebooks/tests for lidar rendering and rolling-shutter demonstrations.
5. Built on `gsplat`, with inspiration from `3dgs-deblur`.

## Linked paper
- [[../../papers/arXiv-2411.16816-SplatAD-Real-Time-Lidar-and-Camera-Rendering-with-3D-Gaussian-Splatting-for-AD.md|SplatAD (arXiv:2411.16816)]]

## New papers discovered from project scan
- No additional distinct paper beyond SplatAD was listed in the repository README.

## Sources used
- GitHub repository page and README:
  - `https://github.com/carlinds/splatad`
  - `https://raw.githubusercontent.com/carlinds/splatad/main/README.md`

---

# 中文版本

## 一段话总结
`carlinds/splatad` 是 SplatAD（CVPR 2025）官方代码仓库，目标是基于 3DGS 实现面向自动驾驶的高效多模态渲染。项目在 `gsplat` 基础上扩展了雷达专用渲染与相机/雷达 rolling shutter 补偿，强调可用于实时仿真的工程效率。README 说明该仓库包含高效渲染核心组件，而完整模型层（如 dataloader、decoder）计划通过 `neurad-studio` 发布。

## 项目关键特性
1. 雷达高效渲染：球坐标投影 + 非线性网格栅格化。
2. 相机与雷达双模态 rolling shutter 补偿。
3. 在 `gsplat/rendering.py` 提供相机与雷达渲染入口。
4. 提供示例与测试用于验证雷达渲染与滚动快门补偿。
5. 基于 `gsplat` 构建，并参考 `3dgs-deblur` 思路。

## 关联论文
- [[../../papers/arXiv-2411.16816-SplatAD-Real-Time-Lidar-and-Camera-Rendering-with-3D-Gaussian-Splatting-for-AD.md|SplatAD（arXiv:2411.16816）]]

## 本次扫描新增论文
- 仓库 README 中未发现除 SplatAD 之外的独立新增论文条目。
