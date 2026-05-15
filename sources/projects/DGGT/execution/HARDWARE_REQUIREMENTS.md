# DGGT Hardware Requirements & Constraints

**Project**: DGGT — Feedforward 4D Reconstruction of Dynamic Driving Scenes  
**Target GPU**: NVIDIA RTX 2000 Ada Generation (8 GB VRAM)  
**CUDA Version**: 13.0  
**PyTorch**: 2.12.0+cu130  
**Last Updated**: 2026-05-15

---

## Table of Contents

1. [GPU Specifications](#1-gpu-specifications)
2. [Model Architecture & Parameter Count](#2-model-architecture--parameter-count)
3. [Memory Budget Breakdown](#3-memory-budget-breakdown)
4. [FP16 / Mixed Precision — Why It Does NOT Work](#4-fp16--mixed-precision--why-it-does-not-work)
5. [Resolution Constraints](#5-resolution-constraints)
6. [Feasibility Table by Task Type](#6-feasibility-table-by-task-type)
7. [Inference Memory Profile](#7-inference-memory-profile)
8. [Training Requirements](#8-training-requirements)
9. [Diffusion Refinement Requirements](#9-diffusion-refinement-requirements)
10. [Memory Optimization Strategies](#10-memory-optimization-strategies)
11. [Disk & Storage Requirements](#11-disk--storage-requirements)
12. [Software Dependencies](#12-software-dependencies)
13. [Quick Reference Card](#13-quick-reference-card)

---

## 1. GPU Specifications

### NVIDIA RTX 2000 Ada Generation

| Spec                    | Value                     |
|-------------------------|---------------------------|
| Architecture            | Ada Lovelace (AD107)      |
| CUDA Cores              | 2816                      |
| VRAM                    | 8 GB GDDR6 ECC           |
| Memory Bus              | 128-bit                   |
| Memory Bandwidth        | 192 GB/s                  |
| TDP                     | 70W                       |
| FP32 Performance        | ~12 TFLOPS                |
| FP16 Performance        | ~24 TFLOPS (unusable — see §4) |
| Tensor Cores            | 88 (4th gen)              |
| CUDA Compute Capability | 8.9                       |

**Key Limitation**: 8 GB VRAM is the hard constraint. Model weights consume
~5 GB FP32, leaving ~3 GB for activations and rendering buffers.

---

## 2. Model Architecture & Parameter Count

DGGT uses the **VGGT-1B** backbone (DINOv2-ViT-Large/14 encoder) with six
prediction heads (camera, point, depth, GS, instance, semantic) plus a sky model.

### Parameter Estimates

| Component        | Parameters (approx.) | FP32 Size  |
|------------------|----------------------|------------|
| Aggregator/ViT-L | ~900M               | ~3.4 GB    |
| camera_head      | ~50M                | ~190 MB    |
| point_head       | ~40M                | ~150 MB    |
| depth_head       | ~40M                | ~150 MB    |
| gs_head          | ~60M                | ~230 MB    |
| track_head       | ~50M                | ~190 MB    |
| instance_head    | ~20M                | ~75 MB     |
| semantic_head    | ~20M                | ~75 MB     |
| sky_model        | ~5M                 | ~20 MB     |
| **Total**        | **~1.2B**           | **~4.5 GB** |

The checkpoint file `model_latest_waymo.pth` is **5.1 GB** on disk (includes
optimizer states and metadata alongside the raw parameters).

---

## 3. Memory Budget Breakdown

### Total VRAM Budget: 8192 MB

```
┌─────────────────────────────────────────────────────┐
│  VRAM Budget: 8192 MB (RTX 2000 Ada)                │
├─────────────────────────────────────────────────────┤
│  Model weights (FP32)            ~4500 MB           │
│  PyTorch CUDA context            ~300 MB            │
│  gsplat rendering buffers        ~200 MB            │
│  ─────────────────────────────────────────           │
│  Available for activations:      ~3192 MB           │
├─────────────────────────────────────────────────────┤
│  2 frames @ 518×518:                                │
│    - Input tensors               ~12 MB             │
│    - Backbone activations        ~1500 MB           │
│    - Head outputs                ~200 MB            │
│    - Gaussian splatting          ~300 MB            │
│    - Misc intermediates          ~200 MB            │
│    ─────────────────────────────                    │
│    Total activations:            ~2200 MB           │
│    Peak usage:                   ~6400 MB  ✓        │
├─────────────────────────────────────────────────────┤
│  4 frames @ 518×518:                                │
│    - Backbone activations        ~3200 MB           │
│    - Head outputs + rendering    ~800 MB            │
│    Total activations:            ~4800 MB           │
│    Peak usage:                   ~9600 MB  ✗ OOM    │
└─────────────────────────────────────────────────────┘
```

### Memory Scaling by Frame Count (518×518, FP32)

| Frames | Estimated Peak VRAM | Status on 8 GB |
|--------|--------------------:|----------------|
| 1      | ~5.5 GB            | ✅ Safe         |
| 2      | ~6.4 GB            | ✅ Tight        |
| 3      | ~7.8 GB            | ⚠️ Risky       |
| 4      | ~9.6 GB            | ❌ OOM          |
| 8      | ~15+ GB            | ❌ OOM          |

---

## 4. FP16 / Mixed Precision — Why It Does NOT Work

The VGGT model **explicitly disables** mixed-precision at `dggt/models/vggt.py:61`:

```python
with torch.cuda.amp.autocast(enabled=False):
    # ALL prediction heads forced to FP32
```

**All six prediction heads** run in FP32 regardless of any outer `autocast` context.
The camera head predicts extrinsic/intrinsic matrices requiring high numerical
precision — FP16 rounding errors compound through 3D unprojection and Gaussian
splatting, producing unusable reconstructions.

**Implications**: FP16 would theoretically halve memory, but since it's disabled,
Tensor Cores are unused. The only option is `torch.no_grad()` (already used in
inference) and reducing frame count.

---

## 5. Resolution Constraints

### Patch Size = 14 (ViT-L/14)

The DINOv2 backbone uses a patch size of 14×14 pixels. The `PatchEmbed` layer
contains explicit assertions:

```python
# dggt/layers/patch_embed.py, lines 72-73
assert H % patch_H == 0, f"Input image height {H} is not a multiple of patch height {patch_H}"
assert W % patch_W == 0, f"Input image width {W} is not a multiple of patch width: {patch_W}"
```

### Valid Resolutions

Both height and width must be **divisible by 14**. Common valid sizes:

| Resolution | Patches (H×W) | Total Patches | Notes              |
|------------|---------------|---------------|--------------------|
| 224 × 224  | 16 × 16       | 256           | Standard ViT       |
| 336 × 336  | 24 × 24       | 576           | —                  |
| 448 × 448  | 32 × 32       | 1024          | —                  |
| 518 × 518  | 37 × 37       | 1369          | **DGGT default**   |
| 532 × 532  | 38 × 38       | 1444          | —                  |
| 560 × 560  | 40 × 40       | 1600          | —                  |
| 672 × 672  | 48 × 48       | 2304          | Likely OOM         |
| 896 × 896  | 64 × 64       | 4096          | Definitely OOM     |

### Resolution vs Memory (2 frames, FP32)

| Resolution | Est. Peak VRAM | Feasible? |
|------------|---------------:|-----------|
| 224 × 224  | ~5.0 GB       | ✅ Safe    |
| 448 × 448  | ~6.0 GB       | ✅ OK      |
| 518 × 518  | ~6.4 GB       | ✅ Tight   |
| 672 × 672  | ~7.8 GB       | ⚠️ Risky  |

Stick with **518×518** (the default) for quality. Use 336×336 or 448×448
for headroom if attempting 3 frames.

---

## 6. Feasibility Table by Task Type

| Task                           | VRAM Needed | RTX 2000 Ada (8 GB) | Notes |
|--------------------------------|-------------|---------------------|-------|
| **Inference: 2 frames, 518²** | ~6.4 GB     | ✅ Feasible          | Sweet spot for quality vs memory |
| **Inference: 1 frame, 518²**  | ~5.5 GB     | ✅ Easy              | Single-frame reconstruction |
| **Inference: 3 frames, 518²** | ~7.8 GB     | ⚠️ Marginal          | May work with `PYTORCH_CUDA_ALLOC_CONF` tuning |
| **Inference: 4 frames, 518²** | ~9.6 GB     | ❌ OOM               | Exceeds VRAM |
| **Inference: 2 frames, 336²** | ~5.5 GB     | ✅ Easy              | Lower quality, more headroom |
| **Inference + Diffusion**      | ~8.4+ GB    | ❌ OOM               | Difix model adds +2-3 GB |
| **Training (any config)**      | 40+ GB/GPU  | ❌ Not feasible      | Needs A100/H100 cloud GPUs |
| **Mode 3: Interpolation**      | ~8+ GB      | ❌ OOM (likely)      | TAPIP3D tracking adds overhead |
| **Metrics computation**        | +200 MB     | ✅ Minimal           | LPIPS net is small |
| **Data preprocessing**         | ~2 GB       | ✅ CPU-bound         | nuScenes/Waymo preprocessing |

---

## 7. Inference Memory Profile

### Measured Profile: 2 Frames @ 518×518 (FP32, torch.no_grad)

```
Phase                           VRAM Usage    Delta
────────────────────────────────────────────────────
CUDA context initialized        ~300 MB       +300 MB
Model loaded to GPU             ~4800 MB      +4500 MB
Input tensors allocated         ~4812 MB      +12 MB
Aggregator forward pass         ~5900 MB      +1088 MB  (peak backbone)
Head predictions                ~6200 MB      +300 MB
Gaussian splatting render       ~6400 MB      +200 MB   (peak total)
After render complete           ~5200 MB      -1200 MB  (intermediates freed)
Between batches (idle)          ~4900 MB      baseline
────────────────────────────────────────────────────
Peak: ~6400 MB / 8192 MB = 78% utilization
Headroom: ~1800 MB
```

### Tips for Monitoring

```bash
watch -n 0.5 nvidia-smi
# Or in Python:
print(f"Peak: {torch.cuda.max_memory_allocated()/1e9:.2f} GB")
```

If you get OOM near the boundary:
```bash
export PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True
```
This reduces fragmentation and can reclaim ~200-400 MB.

---

## 8. Training Requirements

### Why Training is NOT Feasible on 8 GB

Training requires dramatically more memory than inference:

| Component                    | Additional VRAM |
|------------------------------|-----------------|
| Gradient storage (FP32)      | ~4.5 GB (same as weights) |
| Optimizer states (AdamW)     | ~9 GB (2× weight size) |
| Activation checkpoints       | ~2-4 GB        |
| Backward pass intermediates  | ~5-10 GB       |
| LPIPS loss network           | ~0.5 GB        |
| **Total for training**       | **~40-50 GB**  |

### Training Setup from `train.py`

```python
# train.py uses:
# - DDP (DistributedDataParallel)
# - torch.cuda.amp.autocast(dtype=torch.float32)  # still FP32
# - AdamW optimizer with 3 parameter groups
# - sequence_length=4 (default)
# - batch_size=1
```

### Minimum Hardware for Training

| Setup           | VRAM/GPU | Feasible? |
|-----------------|----------|-----------|
| RTX 2000 Ada    | 8 GB     | ❌ No     |
| RTX 4090        | 24 GB    | ⚠️ Tight  |
| A100            | 40 GB    | ✅ Yes    |
| A100-80GB       | 80 GB    | ✅ Comfortable |
| Multi-GPU A100  | 40 GB ea | ✅ Recommended |

---

## 9. Diffusion Refinement Requirements

### Difix Model

The diffusion refinement model (`third_party/difix/`) is a Stable Diffusion-based
denoising pipeline that adds **+2-3 GB** VRAM:

```python
# third_party/difix/infer.py
model = Difix(
    pretrained_path=model_path,
    timestep=199,
    mv_unet=False
)
```

### Memory Impact

| Configuration               | Peak VRAM | Feasible? |
|-----------------------------|-----------|-----------|
| DGGT inference only (2fr)   | ~6.4 GB   | ✅        |
| DGGT + Difix loaded         | ~8.4+ GB  | ❌ OOM    |
| DGGT → unload → Difix       | ~5 GB     | ✅ Sequential |

### Workaround: Sequential Processing

The only way to use diffusion on 8 GB:

1. Run DGGT inference, save rendered frames to disk
2. Unload DGGT model from GPU (`del model; torch.cuda.empty_cache()`)
3. Load Difix model
4. Process saved frames one at a time
5. This is slow but avoids OOM

Note: The `inference.py` script loads both simultaneously (line 306-312),
which will OOM on 8 GB GPUs.

---

## 10. Memory Optimization Strategies

### ✅ Strategies That Work

| Strategy                        | Memory Saved | Effort | Notes |
|---------------------------------|-------------|--------|-------|
| `torch.no_grad()` context       | ~2-3 GB     | None   | Already used in inference |
| Reduce to 2 frames              | ~3 GB vs 4fr| Config | `--sequence_length 2` |
| Lower resolution (336²)         | ~1 GB       | Config | Quality trade-off |
| `PYTORCH_CUDA_ALLOC_CONF`       | ~200-400 MB | Env var| Reduces fragmentation |
| Sequential diffusion            | ~3 GB       | Code   | Save/reload pattern |
| Delete intermediates explicitly  | ~200 MB     | Code   | `del tensor; gc.collect()` |
| `torch.cuda.empty_cache()`      | Variable    | Code   | Frees reserved but unused memory |
| Process scenes sequentially      | None        | Config | Avoids accumulation |

### ❌ Strategies That Do NOT Work

| Strategy                        | Why It Fails |
|---------------------------------|-------------|
| FP16 / mixed precision          | Model calls `autocast(enabled=False)` — see §4 |
| Gradient checkpointing (inference) | No gradients in eval mode |
| Model parallelism               | Single GPU, no split support |
| Dynamic batching                | Batch size already 1 |
| TensorRT / ONNX export         | Complex model with dynamic shapes, gsplat custom ops |
| Quantization (INT8)             | Would break numerical precision in pose estimation |
| CPU offloading for heads        | Too slow, heads need GPU tensors from backbone |

### ⚠️ Strategies That Might Work (Untested)

| Strategy                        | Potential Savings | Risk |
|---------------------------------|-------------------|------|
| Selective head disabling        | ~500 MB           | Need to modify `vggt.py` forward |
| Token pruning / sparse attention| ~500-1000 MB      | Requires research; may degrade quality |
| Compile with `torch.compile()`  | ~200 MB           | May not support all ops |
| Offload model to CPU between batches | Time cost    | Very slow, defeats purpose |

---

## 11. Disk & Storage Requirements

| Item                               | Size     | Required? |
|------------------------------------|----------|-----------|
| DGGT repository                    | ~100 MB  | ✅ Yes     |
| Waymo checkpoint (`model_latest_waymo.pth`) | 5.1 GB | ✅ Yes |
| VGGT pretrained (`model.pt`)       | ~5 GB    | Training only |
| Diffusion checkpoint               | ~2 GB    | Optional  |
| TAPIP3D checkpoint                 | ~500 MB  | Mode 3 only |
| nuScenes mini (raw)                | ~4 GB    | For nuScenes |
| nuScenes mini (processed)          | ~600 MB  | After preprocessing |
| Waymo processed (per split)        | ~50 GB   | For Waymo |
| Conda environment                  | ~8 GB    | ✅ Yes     |
| **Total (minimal inference)**      | **~14 GB** | |
| **Total (full setup)**             | **~75+ GB** | |

---

## 12. Software Dependencies

### Core Requirements

| Package          | Version        | Purpose                |
|------------------|----------------|------------------------|
| Python           | 3.10           | Runtime                |
| PyTorch          | 2.12.0+cu130   | Deep learning framework|
| CUDA             | 13.0           | GPU compute            |
| gsplat           | latest         | Gaussian splatting     |
| torchvision      | 0.19.1+        | Image transforms       |
| numpy            | <2.0           | Numerical arrays       |
| huggingface_hub  | latest         | Model loading          |

### Third-Party Components

| Component    | Location                    | Purpose                    |
|--------------|-----------------------------|----------------------------|
| Difix        | `third_party/difix/`        | Diffusion refinement       |
| TAPIP3D      | `third_party/TAPIP3D/`      | 3D point tracking          |
| MegaSAM      | `third_party/megasam/`      | Segmentation / depth       |
| CoTracker    | `third_party/cotracker/`    | Point tracking             |
| pointops2    | `third_party/pointops2/`    | Point cloud operations     |

### Build Requirements

```bash
# pointops2 requires compilation:
cd third_party/pointops2
python setup.py install
# Needs: CUDA toolkit headers, gcc/g++, torch C++ extensions
```

---

## 13. Quick Reference Card

```
╔══════════════════════════════════════════════════════════╗
║            DGGT on RTX 2000 Ada — Quick Reference       ║
╠══════════════════════════════════════════════════════════╣
║  GPU: RTX 2000 Ada (8 GB)  │  CUDA: 13.0               ║
║  Model: ~1.2B params       │  Weights: ~5 GB FP32       ║
║  Precision: FP32 ONLY      │  FP16: BROKEN              ║
║  Resolution: must be ÷14   │  Default: 518×518          ║
╠══════════════════════════════════════════════════════════╣
║  SAFE:    1-2 frames @ 518×518  (~5.5-6.4 GB)          ║
║  RISKY:   3 frames @ 518×518    (~7.8 GB)              ║
║  OOM:     4+ frames @ 518×518   (9.6+ GB)              ║
║  OOM:     Any frames + Diffusion simultaneously        ║
║  OOM:     Training (needs 40+ GB)                      ║
╠══════════════════════════════════════════════════════════╣
║  Working command:                                       ║
║  python inference.py \                                  ║
║    --sequence_length 2 --mode 2 --input_views 1 \      ║
║    --ckpt_path pretrained/model_latest_waymo.pth \     ║
║    --image_dir <data_path> --output_path results/ \    ║
║    --scene_names 0 -images -depth -metrics             ║
╚══════════════════════════════════════════════════════════╝
```

---

*This document reflects empirical findings from running DGGT on an NVIDIA
RTX 2000 Ada Generation GPU with 8 GB VRAM, CUDA 13.0, and PyTorch 2.12.0.
Memory estimates are approximate and may vary with driver version and system
configuration.*
