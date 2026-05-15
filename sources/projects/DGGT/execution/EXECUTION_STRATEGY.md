# DGGT Execution Strategy

**Project**: DGGT — Feedforward 4D Reconstruction of Dynamic Driving Scenes  
**Target GPU**: NVIDIA RTX 2000 Ada Generation (8 GB VRAM)  
**Objective**: Reproduce DGGT results on constrained consumer hardware  
**Last Updated**: 2026-05-15

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Phase Overview](#2-phase-overview)
3. [Phase 1: Environment Setup](#3-phase-1-environment-setup)
4. [Phase 2: Architecture Analysis](#4-phase-2-architecture-analysis)
5. [Phase 3: Dataset Preparation](#5-phase-3-dataset-preparation)
6. [Phase 4: Inference (Core Reproduction)](#6-phase-4-inference-core-reproduction)
7. [Phase 5: Training](#7-phase-5-training)
8. [Phase 6: Advanced Features](#8-phase-6-advanced-features)
9. [RTX 2000 Ada Specific Workarounds](#9-rtx-2000-ada-specific-workarounds)
10. [Known Pitfalls & Solutions](#10-known-pitfalls--solutions)
11. [Recommended Configurations](#11-recommended-configurations)
12. [Cloud GPU Recommendations](#12-cloud-gpu-recommendations)
13. [Timeline Estimates](#13-timeline-estimates)
14. [Troubleshooting Guide](#14-troubleshooting-guide)
15. [What's Feasible vs What Needs Cloud](#15-whats-feasible-vs-what-needs-cloud)

---

## 1. Executive Summary

DGGT is a ~1.2 billion parameter model for 4D driving scene reconstruction.
On an RTX 2000 Ada (8 GB VRAM), we can run inference with up to 2 frames at
518×518 resolution in FP32. Training, diffusion refinement, and frame
interpolation exceed the 8 GB limit and require cloud GPUs.

### What Works Locally

- ✅ Single-pass inference (mode 2, reconstruction) — 2 frames at 518×518
- ✅ Depth map extraction
- ✅ Metric evaluation (PSNR, SSIM, LPIPS)
- ✅ Video generation from rendered frames
- ✅ Cross-dataset zero-shot inference (Waymo checkpoint → nuScenes)
- ✅ Dataset preprocessing (CPU-bound)

### What Requires Cloud

- ❌ Training (40+ GB VRAM per GPU)
- ❌ Diffusion refinement (model + Difix simultaneously = 8.4+ GB)
- ❌ Frame interpolation mode 3 (TAPIP3D adds memory overhead)
- ❌ Multi-view inference with 4+ frames
- ⚠️ 3-frame inference (borderline, may work with tuning)

---

## 2. Phase Overview

```
Phase 1: Environment Setup          ✅ Complete   (~30 min)
Phase 2: Architecture Analysis       ✅ Complete   (~1 hour)
Phase 3: Dataset Preparation         ✅ Complete   (~1 hour)
Phase 4: Inference (Core)           ✅ Complete   (~2 hours)
Phase 5: Training                    📋 Document   (cloud only)
Phase 6: Advanced Features           📋 Partial    (cloud for some)
```

### Dependency Graph

```
Phase 1 ──→ Phase 2 ──→ Phase 3 ──→ Phase 4
                │                       │
                └─── Phase 5 ←──────────┘ (cloud)
                                        │
                                  Phase 6 (cloud for diffusion/interp)
```

---

## 3. Phase 1: Environment Setup

### Duration: ~30 minutes

```bash
conda create -n dggt python=3.10 && conda activate dggt
pip install torch==2.12.0 torchvision torchaudio --index-url https://download.pytorch.org/whl/cu130
pip install -r requirements.txt
cd third_party/pointops2 && python setup.py install && cd ../..
```

Verify: `python -c "from dggt.models.vggt import VGGT; print(sum(p.numel() for p in VGGT().parameters()))"` → ~1.2B

---

## 4. Phase 2: Architecture Analysis

### Duration: ~1 hour

Study the model flow: `images → VGGT.forward() → predictions dict → gsplat rendering`.
Key constraint: `autocast(enabled=False)` at `vggt.py:61` means FP32 only.

**Key Files**: `dggt/models/vggt.py` (model, 108 LOC), `inference.py` (pipeline),
`train.py` (training loop), `dggt/models/aggregator.py` (ViT backbone).

```
Input Images [B,S,3,H,W] → Aggregator (DINOv2-ViT-L/14)
  → aggregated_tokens → camera_head, point_head, depth_head
  → image_tokens → gs_head (Gaussian attributes)
  → dino_tokens → instance_head, semantic_head
  → gsplat.rasterization() → rendered images
```

---

## 5. Phase 3: Dataset Preparation

### Duration: ~1 hour (nuScenes mini)

| Dataset      | Script                                    | Instructions |
|-------------|-------------------------------------------|-------------|
| Waymo       | `datasets/preprocess_waymo.py`            | `datasets/Waymo.md` |
| nuScenes    | `datasets/nuscenes/nuscenes_preprocess.py`| `datasets/NuScenes.md` |
| Argoverse2  | `datasets/argoverse/argoverse_preprocess.py` | `datasets/ArgoVerse2.md` |

**Critical: Directory Naming** — The `WaymoOpenDataset` class is used for all
datasets but expects specific directory names. Create symlinks as needed:

```bash
cd data/nuscenes/processed_10Hz/mini/000
ln -sf lidar_pose ego_pose
ln -sf dynamic_masks fine_dynamic_masks
```

Expected structure: `{scene}/images/`, `ego_pose/`, `intrinsics/`, `sky_masks/`,
`dynamic_masks/`, `gt_depth/`.

---

## 6. Phase 4: Inference (Core Reproduction)

### Duration: ~2 hours (including debugging)

### Minimum Working Command

```bash
python inference.py \
    --image_dir data/nuscenes/processed_10Hz/mini \
    --scene_names 0 \
    --input_views 1 \
    --sequence_length 2 \
    --start_idx 0 \
    --mode 2 \
    --ckpt_path pretrained/model_latest_waymo.pth \
    --output_path results/nuscenes_mode2 \
    -images -depth -metrics
```

### Critical Parameters for 8 GB GPU

| Parameter            | Value  | Why                                    |
|----------------------|--------|----------------------------------------|
| `--sequence_length`  | 2      | 4 frames = OOM, 2 is safe at ~6.4 GB  |
| `--mode`             | 2      | Reconstruction mode (most stable)      |
| `--input_views`      | 1      | Single camera (less memory)            |
| `-diffusion`         | OMIT   | Adding this causes OOM                 |
| `--intervals`        | N/A    | Only for mode 3 (not used here)        |

### What You Get

```
results/nuscenes_mode2/
├── 001/
│   ├── view_0.png            # Rendered frame 0
│   ├── view_1.png            # Rendered frame 1
│   ├── view_0.npy            # Depth map frame 0
│   ├── view_1.npy            # Depth map frame 1
│   ├── rendered_video.mp4    # Rendered sequence
│   └── comparison.mp4        # GT vs rendered side-by-side
```

### Expected Metrics (Cross-Dataset, Waymo→nuScenes)

| Metric | Value | Notes |
|--------|-------|-------|
| PSNR   | 11-14 dB | Low due to domain gap (expected) |
| SSIM   | 0.65-0.75 | Structural similarity OK |
| LPIPS  | 0.30-0.45 | Perceptual distance |

For Waymo→Waymo (in-domain), expect PSNR 20+ dB.

---

## 7. Phase 5: Training

### Status: Document Only (NOT feasible on 8 GB)

Training freezes the backbone and fine-tunes `gs_head`, `instance_head`, `sky_model`
using AdamW (lr=4e-5 to 1e-4) with L1 + LPIPS + sky mask + dynamic losses.
Even with frozen backbone, training needs ~20-25 GB. Full model: ~40-50 GB.

```bash
# Cloud setup (A100 40 GB):
torchrun --nproc_per_node=1 --master_port=29500 train.py \
    --image_dir /data/waymo/processed \
    --log_dir logs/experiment_01 \
    --ckpt_path pretrained/model.pt \
    --sequence_length 4 --batch_size 1 --max_epoch 50000
```

---

## 8. Phase 6: Advanced Features

### 8a. Diffusion Refinement (Difix)

**Status**: Requires sequential processing workaround or cloud GPU.

The Difix model is a Stable Diffusion-based refinement that "removes degradation"
from rendered frames. On 8 GB, it cannot coexist with DGGT in VRAM.

**Sequential workaround** (slow but functional):

```python
# Step 1: Run inference, save frames
# python inference.py ... -images  (without -diffusion)

# Step 2: In a separate script, load Difix and process frames
import torch
from third_party.difix.infer import process_images_with_difix
from torchvision import transforms

for frame_path in frame_paths:
    img = transforms.ToTensor()(Image.open(frame_path))
    refined = process_images_with_difix(img, "pretrained/diffusion_model.pth")
    transforms.ToPILImage()(refined).save(frame_path.replace('.png', '_refined.png'))
```

### 8b. Frame Interpolation (Mode 3)

**Status**: Likely requires cloud GPU.

Mode 3 uses TAPIP3D for 3D point tracking to interpolate between frames.
This adds significant memory overhead on top of the base DGGT model.

Requirements:
- TAPIP3D checkpoint (`pretrained/tracking_model.pth`)
- TAPIP3D imports must be available (guarded with try/except in inference.py)
- The `interp_all` function from `utils/interplation.py`
- Additional ~2 GB VRAM for tracking model

### 8c. Multi-View Inference (3 cameras)

**Status**: Feasible with 2 frames per camera, possibly 3.

```bash
python inference.py \
    --input_views 3 \
    --sequence_length 2 \
    --mode 2 \
    # ... other args
```

With `input_views=3`, the model processes 3 camera perspectives but uses the
same frame count. Memory is dominated by `sequence_length`, not `input_views`.

---

## 9. RTX 2000 Ada Specific Workarounds

### 9a. Environment Variable

```bash
export PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True
```
Reduces CUDA fragmentation, can free ~200-400 MB.

### 9b. Sequence Length Reduction

**Always use `--sequence_length 2`** — the single most impactful change.
Default 4 frames = OOM. 2 frames = ~6.4 GB (safe).

### 9c. Resolution Reduction

If you need more headroom, resize inputs to 336×336 or 448×448 (must be ÷14).

### 9d. Disable Unused Heads

If you only need depth (not Gaussian rendering), comment out heads in
`vggt.py` forward to save ~500 MB of activation memory. ⚠️ May break rendering.

---

## 10. Known Pitfalls & Solutions

### Pitfall 1: FP16 is Broken

**Symptom**: Enabling mixed precision produces garbage reconstructions or NaN.

**Root Cause**: `dggt/models/vggt.py` line 61:
```python
with torch.cuda.amp.autocast(enabled=False):
```
All prediction heads run in FP32 regardless of outer precision context.

**Solution**: Accept FP32. Do NOT try to force FP16. The memory savings aren't
realized because the expensive heads run in FP32 anyway.

---

### Pitfall 2: Resolution Must Be Divisible by 14

**Symptom**: `AssertionError: Input image height X is not a multiple of patch height 14`

**Root Cause**: `dggt/layers/patch_embed.py` lines 72-73 assert `H % 14 == 0`
and `W % 14 == 0`. The ViT-L/14 backbone uses 14×14 patches.

**Solution**: Always ensure input images are resized to dimensions divisible by 14.
The default 518×518 (= 37 × 14) works. Other valid sizes: 224, 336, 448, 532, 560, 672.

```python
# Quick check:
assert H % 14 == 0 and W % 14 == 0, f"Resolution {H}x{W} not divisible by 14"
```

---

### Pitfall 3: TAPIP3D Import Guards Needed for Mode 2

**Symptom**: `ImportError` when running mode 2, even though TAPIP3D is only
needed for mode 3 (interpolation).

**Root Cause**: The original `inference.py` has a top-level import:
```python
from third_party.TAPIP3D.utils.inference_utils import load_model, read_video, ...
```

**Solution**: The import should be wrapped in a try/except guard:
```python
try:
    from third_party.TAPIP3D.utils.inference_utils import (
        load_model, read_video, inference, get_grid_queries, resize_depth_bilinear
    )
except ImportError:
    load_model = None  # Only needed for mode 3
```

Similarly for `utils.interplation`:
```python
try:
    from utils.interplation import interp_all
except (ImportError, ModuleNotFoundError):
    interp_all = None  # Only needed for mode 3
```

These guards are already present in the current codebase.

---

### Pitfall 4: `args.difix` vs `args.diffusion` Bug

**Symptom**: `-diffusion` flag has no effect, or `AttributeError: Namespace
has no attribute 'difix'`.

**Root Cause**: The argparse flag is defined as `-diffusion` (stored as
`args.diffusion`), but the code may reference `args.difix` in some versions.

**Solution**: Ensure consistency. The current `inference.py` uses:
```python
parser.add_argument('-diffusion', action='store_true', ...)
# Later: if args.diffusion:
```

If you encounter `args.difix` references, change them to `args.diffusion`.

---

### Pitfall 5: nuScenes Directory Naming vs Code Expectations

**Symptom**: `FileNotFoundError` when loading nuScenes data. Missing directories
like `ego_pose/`, `fine_dynamic_masks/`.

**Root Cause**: The preprocessing scripts for nuScenes produce directory names
that may differ from what `WaymoOpenDataset` expects. The dataset class was
originally written for Waymo and reused for other datasets.

**Solution**: Create symlinks to bridge naming differences:

```bash
cd data/nuscenes/processed_10Hz/mini/000

# Common symlinks needed:
ln -sf lidar_pose ego_pose                    # Pose directory name
ln -sf dynamic_masks fine_dynamic_masks       # Dynamic mask directory
```

Check `datasets/dataset.py` to see exactly which directory names the loader
expects, and create symlinks accordingly.

---

### Pitfall 6: `model_latest_waymo.pth` vs `model_latest_waymo.pt` Extension

**Symptom**: File not found when loading checkpoint.

**Root Cause**: The README says `.pth` but the download might be `.pt` or
vice versa. PyTorch doesn't care about the extension.

**Solution**: Use the actual filename on disk. Rename if needed:
```bash
mv pretrained/model_latest_waymo.pt pretrained/model_latest_waymo.pth
# or just reference the correct path in --ckpt_path
```

---

### Pitfall 7: gsplat Version Compatibility

**Symptom**: `TypeError` or unexpected arguments in `rasterization()` call.

**Root Cause**: The gsplat API has changed across versions. DGGT uses
`render_mode='RGB+ED'` which requires a specific gsplat version.

**Solution**: Install the version that matches:
```bash
pip install gsplat  # Latest from PyPI, or pin to a specific version
```

---

### Pitfall 8: CUDA Out of Memory with Default Settings

**Symptom**: `RuntimeError: CUDA out of memory` during inference.

**Root Cause**: Default `--sequence_length 4` requires ~9.6 GB.

**Solution**: Use `--sequence_length 2` (see §9b).

---

## 11. Recommended Configurations

### Config A: Safe Minimum (2 frames, mode 2)

```bash
python inference.py \
    --image_dir data/nuscenes/processed_10Hz/mini \
    --scene_names 0 --input_views 1 --sequence_length 2 \
    --start_idx 0 --mode 2 \
    --ckpt_path pretrained/model_latest_waymo.pth \
    --output_path results/config_a -images -depth -metrics
```
**Memory**: ~6.4 GB | **Quality**: Good | **Speed**: ~3-5 sec/scene

### Config B: Waymo In-Domain

```bash
python inference.py \
    --image_dir data/waymo/processed \
    --scene_names 3 5 7 --input_views 1 --sequence_length 2 \
    --mode 2 --ckpt_path pretrained/model_latest_waymo.pth \
    --output_path results/waymo -images -depth -metrics
```
**Expected**: PSNR 20+ dB (in-domain).

---

## 12. Cloud GPU Recommendations

### For Training

| Provider    | GPU         | VRAM  | $/hr   | Notes |
|-------------|-------------|-------|--------|-------|
| Lambda Labs | A100 40 GB  | 40 GB | ~$1.10 | Best value |
| RunPod      | A100 80 GB  | 80 GB | ~$1.64 | Comfortable |
| Vast.ai     | A100 40 GB  | 40 GB | ~$0.80 | Budget option |

### For Diffusion/Advanced Inference

| Provider | GPU      | VRAM  | $/hr   | Use Case |
|----------|----------|-------|--------|----------|
| RunPod   | RTX 4090 | 24 GB | ~$0.44 | Diffusion + inference |
| Vast.ai  | RTX 4090 | 24 GB | ~$0.35 | Budget diffusion |

### Estimated Cloud Costs

| Task                     | GPU       | Time     | Cost   |
|--------------------------|-----------|----------|--------|
| Full Waymo eval (100 sc) | A100 40GB | ~2 hrs   | ~$2-3  |
| Training 50K steps       | A100 40GB | ~24-48h  | ~$30-60|
| Full reproduction        | A100 40GB | ~3 days  | ~$80-100|

---

## 13. Timeline Estimates

### Local (RTX 2000 Ada): ~5 hours total

| Phase | Task | Time |
|-------|------|------|
| 1 | Environment setup | 30 min |
| 2 | Architecture analysis | 1 hour |
| 3 | Dataset prep (nuScenes mini) | 1 hour |
| 4 | Inference (debugging + running) | 2-3 hours |

### Cloud (for full reproduction): ~4 days

Training (2-3 days) + diffusion eval (2-3 hrs) + interpolation (2-3 hrs)
+ cross-dataset eval (4-6 hrs).

---

## 14. Troubleshooting Guide

### OOM During Inference
1. Is `--sequence_length` ≤ 2?
2. Is `-diffusion` flag absent?
3. Set `PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True`?
4. No other process using GPU? (`nvidia-smi`)

### Model Loads But Bad Predictions
1. Correct checkpoint? (`model_latest_waymo.pth` for inference)
2. Model in eval mode? (`model.eval()`)
3. Using FP32? (FP16 produces garbage)

### Dataset Loading Errors
1. Directory structure matches `WaymoOpenDataset` expectations?
2. Symlinks created for naming mismatches?
3. Scene names zero-padded to 3 digits? ("000" not "0")

### Rendering Artifacts

| Artifact | Cause | Fix |
|----------|-------|-----|
| Black frames | Sky model normalization | Check bg_render normalization |
| Floating points | Depth error | Expected for cross-dataset |
| NaN in output | Precision issue | Ensure FP32 |

---

### 15. What's Feasible vs What Needs Cloud

| Task                         | Local (8 GB) | Cloud (≥24 GB) |
|------------------------------|-------------|----------------|
| Environment setup            | ✅           | ✅              |
| Dataset preprocessing        | ✅           | ✅              |
| Inference: 1-2 frames       | ✅           | ✅              |
| Inference: 3 frames         | ⚠️ maybe    | ✅              |
| Inference: 4+ frames        | ❌           | ✅              |
| Depth/metric extraction      | ✅           | ✅              |
| Zero-shot cross-dataset      | ✅           | ✅              |
| Diffusion refinement         | ❌ (seq*)    | ✅              |
| Frame interpolation (mode 3) | ❌           | ✅              |
| Training                     | ❌           | ✅ (40GB+)      |

\* Sequential diffusion possible with save/reload workaround (slow).

**Strategy**: Do all development locally (2-frame inference), use cloud only
for training, diffusion, and 4+ frame evaluation.

---

*Developed through hands-on experience running DGGT on RTX 2000 Ada (8 GB),
CUDA 13.0, PyTorch 2.12.0.*
