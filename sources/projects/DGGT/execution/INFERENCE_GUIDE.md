# DGGT Inference Guide (nuScenes on RTX 2000 Ada)

## Prerequisites

```bash
# Environment
source /home/s1000617/miniconda3/bin/activate dggt

# Checkpoint (5.1 GB, one-time download)
mkdir -p pretrained
wget -O pretrained/model_latest_waymo.pth \
  "https://huggingface.co/xiaomi-research/dggt/resolve/main/model_latest_waymo.pt?download=true"
```

## Reconstruction Inference (Phase 4a)

```bash
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

### Output Structure
```
results/nuscenes_mode2/
├── 001/                    # Scene 000 (1-indexed in output)
│   ├── comparison.mp4      # GT vs predicted side-by-side
│   ├── rendered_video.mp4  # Rendered novel views
│   ├── view_0.png          # Rendered frame 0
│   ├── view_1.png          # Rendered frame 1
│   ├── view_0.npy          # Depth map frame 0
│   └── view_1.npy          # Depth map frame 1
└── 002/                    # Scene 001
    └── (same structure)
```

### Metrics Output (printed to stdout)
```
PSNR: 11.43    (cross-dataset transfer, Waymo→nuScenes)
SSIM: 0.706
LPIPS: 0.375
Avg Inference Time: 1.74 s/scene
```

---

## Constraints & Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| OOM with 4 frames | 8 GB not enough for FP32 + 4 frames | Use `--sequence_length 2` |
| "expected Float found Half" | Model disables autocast | Don't use FP16/half() |
| "not multiple of patch height 14" | ViT requires res÷14=0 | Use 518 (default) or 490, 504 |
| `args.difix` AttributeError | Already fixed in inference.py | Use the patched version |
| Missing `fine_dynamic_masks` | nuScenes uses `dynamic_masks` | Create symlink (see below) |
| Missing `ego_pose` | nuScenes uses `lidar_pose` | Create symlink (see below) |

### Data Symlinks (already created)
```bash
# Per scene directory:
cd data/nuscenes/processed_10Hz/mini/000
ln -sf dynamic_masks fine_dynamic_masks
ln -sf lidar_pose ego_pose
# Repeat for 001
```

---

## Inference Modes

| Mode | Description | Status |
|------|-------------|--------|
| 2 | Reconstruction (render + metrics) | ✅ Working |
| 3 | Frame interpolation | Needs TAPIP3D model |
| `-diffusion` | Diffusion refinement | Needs model_difix.pkl + extra 2GB VRAM |

---

## Fixes Applied to `inference.py`

1. **Line 22-24**: Wrapped `from models.tapip3d import TAPIP3D` in try/except (only needed for mode 3)
2. **Line 33-35**: Wrapped `from utils.interplation import interp_all` in try/except (only needed for mode 3)
3. **Line 306**: Changed `if args.difix:` → `if args.diffusion:` (CLI flag is `-diffusion`)

---

## Memory Profile

| Config | Peak VRAM | Status |
|--------|-----------|--------|
| 518×518, 2 frames, FP32 | ~6.4 GB | ✅ Works on 8 GB |
| 518×518, 4 frames, FP32 | ~7.2+ GB | ❌ OOM |
| 518×518, 2 frames, FP16 | N/A | ❌ Broken (model issue) |

---

## Optional Checkpoints

| Model | Size | Purpose | URL |
|-------|------|---------|-----|
| model_latest_waymo.pth | 5.1 GB | Main model (required) | huggingface.co/xiaomi-research/dggt |
| model_difix.pkl | ~2-3 GB | Diffusion refinement | Same repo |
| tapip3d_final.pth | ~1-2 GB | Frame interpolation | huggingface.co/zbww/tapip3d |

---

## Sky Masks

Generated via DeepLabV3 with spatial heuristic fallback (top 1/3 = sky).
- 387 masks total (191 + 196 per scene)
- Format: 1600×900 grayscale PNG, ~2 KB each
- Location: `data/nuscenes/processed_10Hz/mini/{scene}/sky_masks/`
- Quality: Adequate for inference (spatial heuristic, not semantic)

---

## Novel View Rendering (Phase 4b)

Render from offset camera trajectories after reconstructing the 3D scene.

> ⚠️ **DGGT uses normalized coordinates, NOT meters.**  
> Inter-frame translation ≈ 0.05 units. Use offsets of 0.01–0.05 for subtle shifts.  
> Large offsets (≥0.5) cause the scene to disappear (alpha → 0).

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

### Output Structure
```
results/novel_views/000/
├── frame0_original.png       # Original viewpoint reference
├── frame1_original.png
├── lateral_+0.000/frame*.png # Zero offset (exact match to original)
├── lateral_+0.010/frame*.png # Subtle rightward shift
├── lateral_-0.010/frame*.png # Subtle leftward shift
├── lateral_+0.050/frame*.png # Noticeable rightward shift
└── offset_sweep.mp4          # Frame 0 across all offsets (animated)
```

### Offset Parameters (in normalized scene units)
| Param | Meaning | Recommended Range |
|-------|---------|-------------------|
| `--lateral_offsets` | Left/right shift (positive=right) | ±0.01–0.05 |
| `--vertical_offsets` | Up/down shift (negative=elevate) | -0.01 to -0.05 |
| `--longitudinal_offsets` | Forward/back (positive=forward) | ±0.01–0.05 |

### Quality Validation Results (scene 000, lateral offsets)
| Offset | Mean Diff | Median Diff | % pixels >20 |
|--------|-----------|-------------|--------------|
| 0.000 | 0.00 | 0.0 | 0.0% |
| ±0.010 | ~10.4 | 3.0 | 14.3% |
| ±0.020 | ~15.2 | 4.0 | 21.1% |
| ±0.050 | ~27.2 | 10.5 | 35.8% |

### Technical Notes
- Sky background is pre-computed once (diff <0.001 at small offsets)
- No extra VRAM needed beyond reconstruction (only gsplat rasterization)
- Gaussian coverage drops at edges → some holes expected at larger offsets
- All inputs to `rasterization()` must be `.float()` (gsplat dtype requirement)
