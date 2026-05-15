# DGGT Reproduction Project

**GPU**: NVIDIA RTX 2000 Ada (8GB VRAM) | **Start**: 2026-05-14 | **Status**: Phase 4 ✅ Complete

---

## Workspace Structure

```
.copilot-workspace/
├── README.md              ← This file (index + quick reference)
├── PROGRESS.md            ← Phase status tracker
├── INFERENCE_GUIDE.md     ← How to run inference (the working recipe)
└── plan/
    └── DGGT_REPRODUCTION_PLAN.md  ← Full 6-phase master plan
```

---

## Quick Reference

### Working Inference Command
```bash
source /home/s1000617/miniconda3/bin/activate dggt
cd /home/s1000617/repos/dggt
python inference.py \
  --image_dir data/nuscenes/processed_10Hz/mini \
  --scene_names 000 001 \
  --mode 2 \
  --sequence_length 2 \
  --ckpt_path pretrained/model_latest_waymo.pth \
  --output_path results/nuscenes_mode2 \
  -images -metrics -depth
```

### Results (nuScenes v1.0-mini, 2 scenes)
| Metric | Value |
|--------|-------|
| PSNR | 11.43 dB |
| SSIM | 0.706 |
| LPIPS | 0.375 |
| Time | ~1.7 s/scene |
| Memory | ~6.4 GB peak |

### Novel View Rendering Command
```bash
python render_novel_views.py \
  --image_dir data/nuscenes/processed_10Hz/mini \
  --scene_names 000 \
  --sequence_length 2 \
  --ckpt_path pretrained/model_latest_waymo.pth \
  --output_path results/novel_views \
  --lateral_offsets -0.05 -0.03 -0.01 0.01 0.03 0.05 \
  --save_video
```
> ⚠️ Offsets are in **normalized scene units** (not meters). Use 0.01–0.05 for subtle shifts.

### Hardware Constraints (RTX 2000 Ada 8GB)
- **FP16 does NOT work** — model internally disables autocast → FP32 only
- **Max 2 frames** at 518×518 resolution (4 frames → OOM)
- **Resolution must be divisible by 14** (ViT patch_size=14)
- Training not feasible locally (needs multi-GPU, 40+ GB VRAM)

---

## Project Overview

**DGGT** (Driving Gaussian Grounded Transformer) — feedforward 4D reconstruction from unposed driving images.
- **Pose-free**: Predicts camera poses directly
- **Feedforward**: Single-pass, no per-scene optimization
- **Multi-task**: Jointly predicts poses, 3D Gaussians, motion, depth
- **Cross-dataset**: Waymo-trained model generalizes to nuScenes/Argoverse2

### Key Files
| File | Purpose |
|------|---------|
| `inference.py` | Main inference script (rendering, metrics, video) |
| `render_novel_views.py` | Novel view rendering from offset trajectories |
| `dggt/models/vggt.py` | Core model (VGGT-1B, ~1.2B params) |
| `datasets/dataset.py` | WaymoOpenDataset loader |
| `pretrained/model_latest_waymo.pth` | Checkpoint (5.1 GB) |
| `data/nuscenes/processed_10Hz/mini/` | Preprocessed nuScenes (591 MB) |

---

## Phase Summary

| Phase | Status | Summary |
|-------|--------|---------|
| 1. Environment | ✅ | PyTorch 2.12+cu130, all deps installed |
| 2. Architecture | ✅ | VGGT model fully analyzed |
| 3. Dataset | ✅ | nuScenes v1.0-mini preprocessed |
| 4a. Reconstruction | ✅ | Working with images, video, metrics |
| 4b. Novel Views | ✅ | Offset trajectory rendering |
| 5. Training | ⏳ | Document only (needs cloud GPU) |
| 6. Advanced | ⏳ | Diffusion refinement, interpolation |

---

## Resources

- **Paper**: https://arxiv.org/abs/2512.03004
- **Project Page**: https://xiaomi-research.github.io/dggt/
- **Model Zoo**: https://huggingface.co/xiaomi-research/dggt
- **Waymo**: datasets/Waymo.md | **nuScenes**: datasets/NuScenes.md | **Argoverse2**: datasets/ArgoVerse2.md

