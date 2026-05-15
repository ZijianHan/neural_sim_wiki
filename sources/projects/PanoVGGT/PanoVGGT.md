---
title: PanoVGGT Project
source: https://github.com/YijingGuo-June/PanoVGGT
project_page:
Imported At: 2026-05-15
category: Panoramic 3D Reconstruction
tags: [panoramic-vision, feedforward-reconstruction, vggt, transformer, spherical-geometry, depth-pose-joint-learning]
related_papers:
  - "sources/papers/arXiv-2603.17571-PanoVGGT-Feed-Forward-3D-Reconstruction-from-Panoramic-Imagery.md"
---

# PanoVGGT Project Summary

## One-paragraph summary
The `YijingGuo-June/PanoVGGT` repository is the official implementation of PanoVGGT, a feed-forward panoramic 3D reconstruction framework that predicts depth, camera poses, and point clouds from unordered equirectangular images. The repo includes training and evaluation code, an interactive Gradio demo (`app.py`), and inference scripts, and links to released Hugging Face assets for model weights and the PanoCity dataset. The project is positioned as an end-to-end panoramic geometry pipeline rather than a narrow single-task baseline.

## Key project features
1. Official training pipeline (`training/`) and evaluation scripts (`evaluation/`) for panoramic geometry tasks.
2. End-to-end inference and visualization path, including a local Gradio demo app.
3. Public pre-trained model weights hosted on Hugging Face.
4. Public dataset link to PanoCity on Hugging Face.
5. MIT-licensed open-source codebase with clear quick-start instructions.

## Linked paper
- [[../../papers/arXiv-2603.17571-PanoVGGT-Feed-Forward-3D-Reconstruction-from-Panoramic-Imagery.md|PanoVGGT (arXiv:2603.17571)]]

## New papers discovered from project scan
- No additional distinct paper beyond PanoVGGT was listed in the repository README.

## Sources used
- `https://github.com/YijingGuo-June/PanoVGGT`
- `https://raw.githubusercontent.com/YijingGuo-June/PanoVGGT/main/README.md`

---

# 中文版本

## 一段话总结
`YijingGuo-June/PanoVGGT` 是 PanoVGGT 的官方代码仓库，面向“无序全景输入下的前馈 3D 重建”，可输出深度、位姿与点云。仓库提供训练与评测代码、推理脚本以及 Gradio 交互演示，并在 Hugging Face 提供预训练权重与 PanoCity 数据集链接，整体定位为可复现的端到端全景几何重建工程实现。

## 项目关键特性
1. 提供 `training/` 与 `evaluation/`，覆盖训练和评测主流程。
2. 提供推理与可视化能力，含本地 Gradio 演示入口。
3. 发布 Hugging Face 预训练权重。
4. 发布 PanoCity 数据集访问链接。
5. 采用 MIT 开源许可并给出明确快速上手说明。

## 关联论文
- [[../../papers/arXiv-2603.17571-PanoVGGT-Feed-Forward-3D-Reconstruction-from-Panoramic-Imagery.md|PanoVGGT（arXiv:2603.17571）]]

## 本次扫描新增论文
- 仓库 README 未列出除 PanoVGGT 之外的独立新增论文条目。
