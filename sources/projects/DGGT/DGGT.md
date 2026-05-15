---
title: DGGT Project
source: https://github.com/xiaomi-research/dggt
project_page: https://xiaomi-research.github.io/dggt/
Imported At: 2026-05-14
category: Gaussian Splatting
tags: [gaussian-splatting, 4d-reconstruction, feedforward, pose-free, autonomous-driving]
related_papers:
  - "sources/papers/arXiv-2512.03004-DGGT-Feedforward-4D-Reconstruction-of-Dynamic-Driving-Scenes-using-Unposed-Images.md"
---

# DGGT Project Summary

## One-paragraph summary
The `xiaomi-research/dggt` repository is the official implementation of DGGT, a feedforward 4D reconstruction system for dynamic driving scenes from unposed images. The project packages inference/training code, dataset preprocessing guides (Waymo, nuScenes, Argoverse2), pretrained checkpoints, and optional diffusion refinement components. Its stated focus is practical, scalable reconstruction without per-scene optimization or camera calibration prerequisites.

## Key project features
1. Pose-free feedforward reconstruction pipeline from unposed RGB sequences.
2. Dynamic-scene reconstruction outputs including camera pose, Gaussian maps, depth, and motion-related outputs.
3. Dataset support and preprocessing docs for Waymo / nuScenes / Argoverse2.
4. Released training + inference code and pretrained checkpoints.
5. Optional diffusion-based rendered-image refinement path.

## Linked paper
- [[../../papers/arXiv-2512.03004-DGGT-Feedforward-4D-Reconstruction-of-Dynamic-Driving-Scenes-using-Unposed-Images.md|DGGT (arXiv:2512.03004)]]

## New papers discovered from project scan
- No additional distinct paper beyond DGGT was listed in the repository README.

## Sources used
- `https://github.com/xiaomi-research/dggt`
- `https://raw.githubusercontent.com/xiaomi-research/dggt/main/README.md`

---

# 中文版本

## 一段话总结
`xiaomi-research/dggt` 是 DGGT 的官方代码仓库，面向“从未标定位姿图像进行动态驾驶场景 4D 前馈重建”。项目提供了训练/推理代码、Waymo/nuScenes/Argoverse2 的数据处理说明、预训练模型和可选 diffusion 细化流程，核心目标是避免逐场景优化与显式相机标定依赖，提升规模化重建实用性。

## 项目关键特性
1. 无位姿前馈重建流程（unposed RGB 输入）。
2. 动态场景输出（位姿、高斯表示、深度、动态相关结果）。
3. 覆盖 Waymo / nuScenes / Argoverse2 的数据流程支持。
4. 已发布训练与推理代码及预训练权重。
5. 提供可选 diffusion 渲染结果优化。

## 关联论文
- [[../../papers/arXiv-2512.03004-DGGT-Feedforward-4D-Reconstruction-of-Dynamic-Driving-Scenes-using-Unposed-Images.md|DGGT（arXiv:2512.03004）]]

## 本次扫描新增论文
- 仓库 README 未列出除 DGGT 之外的独立新增论文条目。
