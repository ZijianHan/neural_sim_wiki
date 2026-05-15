---
title: "VGGT-Ω: Scaling Feed-Forward Reconstruction"
arxiv_id: 2605.15195v1
source: http://arxiv.org/abs/2605.15195v1
pdf: http://arxiv.org/pdf/2605.15195v1
project: http://vggt-omega.github.io/
date: 2026-05-14
Imported At: 2026-05-15
category: Feed-Forward 3D Reconstruction
---

# VGGT-Ω: Scaling Feed-Forward Reconstruction
## One-paragraph summary

VGGT-Ω is the scaled successor to VGGT, demonstrating that feed-forward reconstruction quality scales predictably with model and data size. It introduces architectural simplifications (single dense prediction head, register-based attention replacing expensive global attention), a dynamic-scene data pipeline, and self-supervised learning. The result uses only ~30% of VGGT's GPU memory during training, enabling 15× more supervised data and vast unlabeled video. It achieves SOTA on static and dynamic reconstruction (77% improvement on Sintel camera estimation) and shows learned registers can improve VLA models and support language alignment.

## Key ideas

- **Register attention**: aggregates scene information into compact register tokens; inter-frame exchange restricted to registers, replacing full global attention.
- **Single dense head**: multi-task supervision with one prediction head, removing expensive high-res conv layers.
- **Scaling law**: quality improves predictably with model size and data volume.
- **Self-supervised protocol**: leverages unlabeled video at scale.
- **Dynamic scene support**: new annotation pipeline extends VGGT to temporal sequences.
- **Register→VLA transfer**: learned registers shown useful for vision-language-action models.

## Novelty (What is new)

- First demonstration of predictable scaling laws for feed-forward 3D reconstruction models.
- Register attention mechanism as a memory-efficient replacement for global cross-frame attention.
- Architectural simplification that reduces training memory to 30% of predecessor while improving quality.
- Self-supervised training on unlabeled video at unprecedented scale.
- Bridging reconstruction and embodied AI via register-based language alignment.

## Why it matters

Directly extends the VGGT lineage (SwiftVGGT, OVGGT, PanoVGGT) already tracked in this wiki. Establishes the paradigm that feed-forward reconstruction benefits from LLM-style scaling, making it central to future driving simulation and large-scale scene understanding pipelines.

## Reported limitations/open challenges (from framing)

- Paper does not report specific failure modes; scaling advantages may plateau at very large model sizes (not yet explored).
- Dynamic scene annotation pipeline specifics not fully detailed in abstract.

## Source snippets used

- arXiv abstract 2605.15195v1

---

## 一段话总结

VGGT-Ω是VGGT的规模化后续版本，证明前馈重建质量随模型和数据规模可预测地提升。通过架构简化（单一密集预测头、寄存器注意力替代全局注意力）、动态场景数据管线和自监督学习，训练显存降至前代的30%，支持15倍更多监督数据。在静态/动态重建上达到SOTA（Sintel相机估计提升77%），并展示寄存器可迁移至VLA模型和语言对齐。

## 核心思想

- 寄存器注意力：将场景信息聚合到紧凑的寄存器token中，帧间信息交换仅通过寄存器。
- 单一密集头：多任务监督，移除高分辨率卷积层。
- 缩放定律：质量随模型/数据规模可预测提升。
- 自监督协议：利用大规模无标注视频。
- 动态场景支持与寄存器→VLA迁移能力。

## 创新点（这篇论文新在哪里）

- 首次展示前馈3D重建的可预测缩放定律。
- 寄存器注意力机制作为全局注意力的高效替代。
- 训练显存降至30%同时质量提升。
- 重建与具身AI通过寄存器对齐的桥接。

## 重要性

直接扩展wiki中已追踪的VGGT系列，确立前馈重建的LLM式缩放范式。

## 论文框架中提到的局限/挑战

- 未报告具体失败模式；极大模型规模下的缩放优势可能存在上限。

## 本页信息来源

- arXiv摘要 2605.15195v1
