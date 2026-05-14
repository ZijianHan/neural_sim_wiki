---
title: 3DGUT - Enabling Distorted Cameras and Secondary Rays in Gaussian Splatting
arxiv_id: 2412.12507v2
source: https://arxiv.org/abs/2412.12507
pdf: https://arxiv.org/pdf/2412.12507
project: https://research.nvidia.com/labs/toronto-ai/3DGUT
code: https://github.com/nv-tlabs/3dgrut
date: 2025-03-24
Imported At: 2026-05-14
category: Gaussian Splatting
related_project: sources/projects/3DGRUT.md
---

# 3DGUT: Enabling Distorted Cameras and Secondary Rays in Gaussian Splatting

## One-paragraph summary
This paper proposes **3DGUT (3D Gaussian Unscented Transform)** to remove two major limitations of standard 3DGS rasterization: weak support for nonlinear/distorted camera models and lack of secondary-light transport effects. Instead of linearizing camera projection with EWA Jacobians, 3DGUT approximates each 3D Gaussian using **sigma points** and projects those points exactly through arbitrary nonlinear camera functions (including rolling-shutter-style time-dependent effects), then re-estimates the projected 2D Gaussian. At the same time, the method aligns its rendering formulation with tracing-based approaches so the same representation can support secondary rays (e.g., reflections/refractions), while aiming to retain rasterization-level efficiency.

## Key ideas
1. Replace EWA projection linearization with an **Unscented Transform** over Gaussian sigma points.
2. Support **arbitrary nonlinear camera models** without deriving camera-specific Jacobians.
3. Model **time-dependent camera effects** (e.g., rolling shutter) via per-sigma-point transforms.
4. Align rasterization formulation with tracing-style evaluation to enable **secondary ray tracing**.
5. Keep high rendering speed close to rasterization-based 3DGS while expanding rendering flexibility.

## Novelty (What is new)
- **UT-based Gaussian projection in 3DGS rasterization**: approximate particles (not projection functions), enabling exact nonlinear projection of sigma points.
- **Camera-model generalization by design**: avoids dedicated per-camera Jacobian derivations and better handles distortion-heavy cameras.
- **Hybrid-compatible rendering formulation**: unifies representation so it can be rasterized efficiently and also traced for secondary effects.
- **Bridging speed-vs-physics gap**: targets tracing-like expressiveness (refraction/reflection) without fully giving up rasterization efficiency.

## Why it matters
- Extends Gaussian Splatting to more realistic capture settings (fisheye/distorted/rolling-shutter cameras).
- Adds a path to richer appearance effects (secondary rays) in the same 3DGS representation.
- Improves practicality for real-world datasets where ideal pinhole assumptions are often violated.

## Reported limitations/open challenges (from framing)
- The method is motivated by the tradeoff between rasterization speed and tracing expressiveness; this tradeoff still governs system design.
- Secondary effects remain tied to tracing components, so performance/quality balancing remains an engineering challenge.
- Distortion and temporal camera effects are better supported, but full physical realism is still constrained by representation and optimization choices.

## Source snippets used
- arXiv abstract page (`/abs/2412.12507`) for core claims, metadata, and acceptance context.
- arXiv HTML (`/html/2412.12507v2`) for introduction framing, stated contributions, and method rationale.
- Project/repo context: `https://github.com/nv-tlabs/3dgrut`.

---

# 中文版本

## 一段话总结
本文提出 **3DGUT（3D Gaussian Unscented Transform）**，旨在解决传统 3DGS 光栅化的两大痛点：对非线性/畸变相机支持不足，以及无法表达二次光照效应。其核心是用 **Unscented Transform（UT）** 替换 EWA 中对投影函数的雅可比线性化：先用 sigma points 近似 3D 高斯，再将这些点通过任意非线性相机模型进行精确投影（包括 rolling shutter 等时间相关效应），最后重估计 2D 高斯。与此同时，论文将渲染公式与 tracing 方法对齐，使同一表示既可保持光栅化效率，又可支持反射/折射等二次光线效果。

## 核心思想
1. 用 **UT sigma points** 替代 EWA 的一阶线性化投影。
2. 无需为不同相机模型手推雅可比，直接支持非线性/畸变相机。
3. 通过对 sigma points 施加时间相关变换，支持 rolling shutter 等效应。
4. 与 tracing 渲染公式对齐，在同一表示中引入二次光线能力。
5. 在扩展表达能力的同时尽量保持 3DGS 光栅化速度优势。

## 创新点（这篇论文新在哪里）
- **在 3DGS 光栅化里引入 UT 投影机制**：近似对象从“投影函数”改为“高斯粒子”，提升非线性投影适配能力。
- **相机模型无关的泛化路径**：不再依赖每种相机单独推导 Jacobian，天然适配复杂畸变。
- **统一表示下的光栅化/追踪协同**：同一高斯表示既能高效 rasterize，也能用于 secondary rays。
- **针对速度-物理效果矛盾给出折中方案**：尝试在保持实时性的同时获得 tracing 式效果增益。

## 重要性
- 让 Gaussian Splatting 更适合真实采集条件（鱼眼、强畸变、rolling shutter）。
- 在同一表示中引入更丰富光照/材质现象（反射、折射）。
- 提升 3DGS 在真实工业与机器人视觉场景中的可用性。

## 论文框架中提到的局限/挑战
- 光栅化效率与 tracing 表达力之间的权衡仍是核心约束。
- 引入 secondary rays 后，性能与质量平衡仍需工程优化。
- 即使支持更复杂相机模型，整体物理真实感仍受表示与优化策略限制。

## 本页信息来源
- arXiv 摘要页（`/abs/2412.12507`）中的摘要与元数据。
- arXiv HTML 页（`/html/2412.12507v2`）中的引言与贡献描述。
