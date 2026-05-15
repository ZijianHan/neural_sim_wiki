---
title: OVGGT: O(1) Constant-Cost Streaming Visual Geometry Transformer
arxiv_id: 2603.05959v3
source: https://arxiv.org/abs/2603.05959
pdf: https://arxiv.org/pdf/2603.05959
project: https://vaisr.github.io/OVGGT/
code: https://github.com/VAISR/OVGGT
date: 2026-03-06
Imported At: 2026-05-14
category: Streaming 3D Geometry Reconstruction
---

# OVGGT: O(1) Constant-Cost Streaming Visual Geometry Transformer

## One-paragraph summary
OVGGT is a training-free streaming 3D geometry framework designed to keep both memory and per-step compute bounded as video length grows. The paper identifies the KV-cache growth bottleneck in causal-attention geometric models (e.g., StreamVGGT), then proposes two plug-in mechanisms: Self-Selective Caching (SSC) for fixed-budget cache compression and Dynamic Anchor Protection (DAP) for retaining coordinate-critical tokens. According to the paper, this enables arbitrarily long video processing under a constant VRAM budget while achieving strong geometric reconstruction accuracy on indoor, outdoor, and ultra-long benchmarks.

## Key ideas
1. Reformulate long-horizon streaming reconstruction as a constant-resource inference problem.
2. Score token importance using FFN residual magnitudes (Activation Value Rating), with spatial smoothing for coherent retention.
3. Compress cache using a hybrid criterion balancing current-frame salience and historical key diversity.
4. Protect geometric reference tokens with a global initial anchor and dynamically registered historical anchors.
5. Keep the method training-free and compatible with existing causal-attention pipelines and FlashAttention.

## Novelty (What is new)
- **O(1)-style streaming objective for geometry transformers**: explicitly targets fixed memory and fixed per-step compute regardless of sequence length.
- **Self-Selective Caching (SSC)**: token-importance-driven KV compression based on FFN residual signals already present in forward pass.
- **Dynamic Anchor Protection (DAP)**: anchor-preservation mechanism to reduce long-sequence drift while aggressively evicting cache tokens.
- **Plug-in, training-free deployment**: improves long-video scalability without architecture retraining.

## Why it matters
- Long-sequence reconstruction is critical for robotics, AD, and digital-twin pipelines but often blocked by cache/memory growth.
- OVGGT addresses practical deployment constraints directly, not only benchmark accuracy.
- It provides a systems-level path to sustained streaming inference on bounded hardware.

## Reported limitations/open challenges (from framing)
- OVGGT is framed as an add-on to causal-attention geometric models; final quality still depends on the base model capacity.
- Token eviction control introduces a retention-policy tradeoff between compactness and geometric fidelity.
- Claims are centered on benchmarked settings; transfer robustness to unseen sensor/domain regimes needs case-by-case validation.

## Source snippets used
- arXiv abs page (`/abs/2603.05959`) for title, abstract, metadata, and links.
- arXiv HTML (`/html/2603.05959v3`) for method components (SSC/DAP), bottleneck framing, and contribution statements.

---

# 中文版本

## 一段话总结
OVGGT 是一个面向长序列视频 3D 几何重建的“免训练”流式框架，目标是在序列增长时仍保持固定内存与固定步长计算开销。论文指出因果注意力几何模型（如 StreamVGGT）的主要瓶颈是 KV cache 持续增长，并提出两项可插拔机制：用于固定预算缓存压缩的 Self-Selective Caching（SSC）和用于保护坐标关键 token 的 Dynamic Anchor Protection（DAP）。论文报告其可在恒定 VRAM 预算下处理超长视频，并在室内、室外与超长序列基准上保持较强几何精度。

## 核心思想
1. 将流式重建问题明确建模为“常数资源推理”问题。
2. 用 FFN 残差幅值评估 token 几何重要性，并通过空间平滑增强保留连贯性。
3. 通过融合当前帧显著性与历史 key 多样性进行缓存压缩。
4. 通过全局初始锚点与动态历史锚点保护长期几何参考，抑制漂移。
5. 保持方案免训练，并兼容现有因果注意力流水线与 FlashAttention。

## 创新点（这篇论文新在哪里）
- **面向几何 Transformer 的 O(1) 流式目标**：显式追求与序列长度无关的固定内存与固定单步计算。
- **Self-Selective Caching（SSC）**：利用前向已存在的 FFN 残差信号进行 token 级缓存压缩。
- **Dynamic Anchor Protection（DAP）**：在强压缩缓存时优先保护坐标关键 token，降低长程漂移。
- **可插拔且免训练**：无需重新训练即可提升长视频可扩展性。

## 重要性
- 自动驾驶、机器人和数字孪生都需要长序列重建，但常被缓存增长与显存限制卡住。
- OVGGT 直接针对工程部署中的资源上限问题，而不仅仅是离线精度。
- 为有限硬件上的持续流式几何推理提供了可行路径。

## 论文框架中提到的局限/挑战
- OVGGT 作为因果注意力几何模型的增强模块，其上限仍受底座模型能力约束。
- 缓存淘汰策略本质上存在“压缩率 vs. 几何保真度”的折中。
- 论文结论主要基于给定基准，跨传感器/跨域鲁棒性仍需按场景验证。

## 本页信息来源
- arXiv 摘要页（`/abs/2603.05959`）中的标题、摘要、元数据与链接。
- arXiv HTML 页（`/html/2603.05959v3`）中的方法细节（SSC/DAP）与贡献描述。
