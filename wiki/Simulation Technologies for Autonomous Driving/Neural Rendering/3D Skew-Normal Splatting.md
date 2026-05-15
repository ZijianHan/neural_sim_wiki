---
tags: [gaussian-splatting, primitive, kernel, boundary-modeling]
last_compiled: 2026-05-15
---

# 3D Skew-Normal Splatting

## Definition
Skew-Normal Splatting (SNS) replaces symmetric Gaussian primitives with Azzalini Skew-Normal distributions, enabling continuous interpolation between symmetric and asymmetric shapes for better boundary and thin-structure modeling.

## Synthesis
### Source-grounded Highlights
- **Skew-Normal primitive**: Azzalini distribution with a bounded learnable skewness parameter per primitive.
- **Continuous shape control**: smoothly interpolates between symmetric Gaussian and Half-Gaussian-like shapes without hard truncation or distributional discontinuities.
- **Analytical tractability preserved**: closed-form under affine transformations and marginalization → seamless drop-in for existing 3DGS rasterization pipelines.
- **Decoupled parameterization**: separates scale, rotation, and skewness to address strong coupling during optimization.
- **Block-wise optimization**: stabilizes training with the additional skewness degree of freedom.
- Consistently improves reconstruction quality over Gaussian and non-Gaussian kernels on standard NVS benchmarks, particularly on sharp boundaries and thin/one-sided structures.

### When to Use
Most beneficial under finite primitive budgets where asymmetric structures (object edges, thin poles, one-sided surfaces) are important — common in driving scenes.

### 中文版本
Skew-Normal Splatting 用Azzalini偏正态分布替代对称高斯原语。可学习偏度参数实现连续不对称形状插值，同时保持仿射变换下的解析可追踪性。解耦参数化解决尺度-旋转-偏度耦合。在有限原语预算下对锐利边界和薄结构建模最有优势。

## Graph Connections
- [[Gaussian Splatting]]
- [[3DGS]]
- [[Neural Rendering]]
- [[Method Comparison]]

## Sources
- [[sources/papers/arXiv-2605.15010-3D-Skew-Normal-Splatting]]
