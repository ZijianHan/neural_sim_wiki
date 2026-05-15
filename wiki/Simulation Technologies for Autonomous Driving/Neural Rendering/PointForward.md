---
tags: [pointforward, gaussian-splatting, feedforward, point-aligned, scene-graph, autonomous-driving]
last_compiled: 2026-05-15
sources:
  - "sources/papers/arXiv-2605.11594-PointForward-Feedforward-Driving-Reconstruction-through-Point-Aligned-Representations.md"
---

# PointForward

## Definition
PointForward is a feedforward driving-scene reconstruction framework that uses sparse point-aligned 3D queries (instead of per-pixel Gaussian prediction) with scene-graph-based dynamic modeling for multi-view consistent, instance-coherent 4D reconstruction.

## Synthesis
### Source-grounded Highlights
- Initializes sparse 3D queries in world space; projects them onto all input views to aggregate features via spatial-temporal fusion.
- Produces globally consistent Gaussians in a single forward pass — avoids redundant primitives and layering artifacts of pixel-aligned methods.
- Introduces scene graphs with 3D bounding boxes for dynamic instance modeling in canonical space, enabling instance-level motion propagation.
- Reports SOTA on Waymo (28.48 PSNR global, 25.01 dynamic) and nuScenes (26.54 zero-shot).
- Directly compared against DGGT: eliminates layering artifacts in fine-grained regions.

### Relation to DGGT
PointForward is from the same lab (Xiaomi EV) and explicitly builds upon DGGT's feedforward paradigm while replacing its two main design choices:
- **Pixel-aligned → Point-aligned**: eliminates multi-view inconsistency from independent per-pixel prediction.
- **Dense flow → Scene graph**: replaces 2D-mask-based flow prediction with 3D-bbox-organized canonical-space transforms.

### Limitations
- Code not yet released.
- Requires 3D bounding box annotations (detection/tracking pipeline as prerequisite).
- Only evaluated on Waymo and nuScenes; cross-domain robustness to other datasets unknown.
- Extreme sparse-view or heavy-occlusion scenarios not extensively discussed.

### 中文版本
PointForward 是小米汽车提出的点对齐前馈重建框架，用世界空间稀疏 3D 查询替代逐像素高斯预测，通过空间-时间融合在单次前传中实现跨视图一致性。动态建模采用场景图 + 3D 边界框的规范空间变换，替代 DGGT 的稠密光流预测，实现实例级运动一致性。在 Waymo 和 nuScenes 上取得 SOTA。

### 局限性（中文）
- 代码尚未开源。
- 依赖 3D 边界框标注（需要检测/跟踪前处理）。
- 仅在 Waymo/nuScenes 验证，跨域泛化未知。
- 极端稀疏视角或严重遮挡场景讨论不足。

## Graph Connections
- [[Neural Rendering]]
- [[Gaussian Splatting]]
- [[DGGT]]
- [[Simulation Technologies for Autonomous Driving]]
- [[Method Comparison]]
