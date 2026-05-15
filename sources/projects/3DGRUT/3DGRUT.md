---
title: 3DGRUT Project (NVIDIA T-Labs)
source: https://github.com/nv-tlabs/3dgrut
project_pages:
  - https://research.nvidia.com/labs/toronto-ai/3DGRT
  - https://research.nvidia.com/labs/toronto-ai/3DGUT
Imported At: 2026-05-14
category: Gaussian Splatting
tags: [gaussian-splatting, ray-tracing, unscented-transform, real-time-rendering]
related_papers:
  - "sources/papers/3DGRT-2024-3D-Gaussian-Ray-Tracing-Fast-Tracing-of-Particle-Scenes.md"
  - "sources/papers/arXiv-2412.12507-3DGUT-3D-Gaussian-Unscented-Transform.md"
---

# 3DGRUT Project Summary

## One-paragraph summary
The **3DGRUT** repository is NVIDIA T-Labs’ unified codebase for two related rendering paradigms over Gaussian particle scenes: **3DGRT** (ray-tracing-first) and **3DGUT** (rasterization-first with Unscented Transform). 3DGRT emphasizes physically richer effects and camera-model flexibility (e.g., distorted cameras, rolling shutter, secondary rays), while 3DGUT aims to preserve rasterization efficiency while supporting nonlinear camera models and compatibility with secondary-ray workflows. The project positions **3DGRUT** as a hybrid strategy: rasterize primary rays for speed and trace secondary rays for richer effects.

## Key components
1. **3DGRT**: Ray tracing of Gaussian particles with BVH-based acceleration and support for secondary effects.
2. **3DGUT**: Unscented-Transform-based Gaussian projection under nonlinear camera models in rasterization.
3. **Hybrid 3DGRUT workflow**: combines both paths to balance speed and expressiveness.
4. **Engineering stack**: training/eval scripts, GUI/playground tooling, multi-platform setup, and production-oriented export paths (e.g., USDZ).

## Linked papers
- [[../../papers/3DGRT-2024-3D-Gaussian-Ray-Tracing-Fast-Tracing-of-Particle-Scenes.md|3DGRT (SIGGRAPH Asia 2024)]]
- [[../../papers/arXiv-2412.12507-3DGUT-3D-Gaussian-Unscented-Transform.md|3DGUT (CVPR 2025)]]

## What’s new from the project scan
- New (previously missing in your `sources/papers`): **3DGRT: 3D Gaussian Ray Tracing: Fast Tracing of Particle Scenes**.
- Already present: **3DGUT** summary.

## Sources used
- GitHub README: `https://github.com/nv-tlabs/3dgrut`
- Raw README and BibTeX files in repo assets (`3dgrt2024.bib`, `3dgut2025.bib`)
- Official project pages for 3DGRT and 3DGUT.

---

# 中文版本

## 一段话总结
**3DGRUT** 是 NVIDIA T-Labs 提供的统一代码库，整合了两条高斯粒子渲染路线：**3DGRT**（以光线追踪为主）和 **3DGUT**（以光栅化为主并引入 Unscented Transform）。3DGRT 更强调物理效果与相机模型灵活性（畸变相机、rolling shutter、二次光线），3DGUT 更强调在保持光栅化效率的同时支持非线性相机，并与 secondary rays 工作流对齐。项目整体提出一种折中式混合思路：主光线走 rasterization，次级光线走 ray tracing。

## 核心组成
1. **3DGRT**：基于 BVH 的高斯粒子光线追踪，支持反射/折射/阴影等效果。
2. **3DGUT**：在光栅化中用 UT 处理非线性投影与复杂相机模型。
3. **混合 3DGRUT**：结合两者以平衡速度和表达能力。
4. **工程化能力**：训练评测脚本、交互式 GUI、多平台安装与导出流程。

## 关联论文
- [[../../papers/3DGRT-2024-3D-Gaussian-Ray-Tracing-Fast-Tracing-of-Particle-Scenes.md|3DGRT（SIGGRAPH Asia 2024）]]
- [[../../papers/arXiv-2412.12507-3DGUT-3D-Gaussian-Unscented-Transform.md|3DGUT（CVPR 2025）]]

## 本次新增发现
- 你当前 `sources/papers` 中之前缺少：**3DGRT（SIGGRAPH Asia 2024）**，已补充总结。
- **3DGUT** 已存在并已与项目建立关联。
