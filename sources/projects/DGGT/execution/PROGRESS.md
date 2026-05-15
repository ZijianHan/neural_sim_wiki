# DGGT Reproduction Project - Progress Tracker

**Project**: DGGT - Feedforward 4D Reconstruction for Dynamic Driving Scenes  
**Repository**: ZijianHan/dggt  
**GPU**: NVIDIA RTX 2000 Ada (8GB VRAM)  
**Start Date**: 2026-05-14

---

## Phase Status

### Phase 1: Environment Setup ✅
- PyTorch 2.12.0+cu130, all 29+ packages verified, VGGT loads on GPU
- Duration: ~30 min (2026-05-14)

### Phase 2: Architecture Analysis ✅
- VGGT model (108 LOC), Aggregator (354 LOC), 6 prediction heads, loss functions mapped
- Duration: ~1 hour (2026-05-14)

### Phase 3: Dataset Workflow ✅
- nuScenes v1.0-mini preprocessed (2 scenes, 387 frames, 591 MB)
- Sky masks extracted (387 via DeepLabV3 spatial heuristic fallback)
- Duration: ~1 hour (2026-05-14)

### Phase 4a: Reconstruction Inference ✅
- **Script**: `inference.py` (mode 2, gsplat rendering)
- **Checkpoint**: `pretrained/model_latest_waymo.pth` (5.1 GB, Waymo-trained)
- **Data**: `data/nuscenes/processed_10Hz/mini/` (scenes 000, 001)
- **Metrics**: PSNR=11.43, SSIM=0.706, LPIPS=0.375 (cross-dataset, expected)
- **Outputs**: comparison.mp4, rendered_video.mp4, depth .npy, frame .png per scene
- **Fixes applied**: guarded TAPIP3D import, fixed `args.difix`→`args.diffusion`, created symlinks
- **Constraint**: FP32 only, max 2 frames at 518×518, ~6.4 GB peak
- Duration: ~2 hours (2026-05-14/15)

### Phase 4b: Novel View Rendering ✅
- **Script**: `render_novel_views.py`
- **Function**: Render from offset camera trajectories (lateral/vertical/longitudinal)
- **Key Discovery**: DGGT uses **normalized coordinates** (not meters). Inter-frame translation ≈ 0.05 units.
- **Correct offsets**: 0.01–0.05 scene units (≈ 0.5–2m real-world)
- **Tested**: 7 lateral offsets (-0.05 to +0.05) on scene 000
  - Zero offset = exact match (0.0 pixel diff)
  - dx=0.01: median diff 3/255, 14.3% pixels >20 diff (subtle shift)
  - dx=0.05: median diff 11/255, 36.4% pixels >20 diff (noticeable shift)
- **Output**: `results/novel_views/000/` with per-offset dirs + sweep video
- Duration: ~1 hour (2026-05-15, incl. coordinate system investigation)

### Phase 5: Training ⏳
- Document only (not feasible on 8 GB GPU)
- Needs multi-GPU cloud setup (40+ GB VRAM)

### Phase 6: Advanced Features ⏳
- Diffusion refinement (needs +2 GB → likely OOM)
- Frame interpolation (Mode 3, needs TAPIP3D)
- Cross-dataset evaluation

---

## Key Learnings

| Topic | Finding |
|-------|---------|
| FP16 | Broken — model calls `autocast(enabled=False)` internally |
| Resolution | Must be multiple of 14 (ViT patch). 518 default works. |
| Memory | Model ~5 GB FP32 + 2 frames ≈ 6.4 GB. 4 frames OOM on 8 GB. |
| `inference_optimized.py` | Abandoned — was incomplete wrapper, never rendered via gsplat |
| `inference.py` | The real deal — full rendering, metrics, video export |
| Data symlinks | `fine_dynamic_masks`→`dynamic_masks`, `ego_pose`→`lidar_pose` |
| **Coordinate system** | DGGT uses normalized coords, NOT meters. Scene range [-1.4, 12.5]. |
| **Novel view offsets** | Use 0.01–0.05 units. 1.0 "unit" ≈ 20× inter-frame distance → scene disappears |
| **dtype in gsplat** | Extrinsic decode may return float64 → must `.float()` all rasterization inputs |
| **Sky at small offsets** | Pre-compute once — diff <0.001 for offsets ≤0.05 |

---

## Resources

| Resource | Path/URL |
|----------|----------|
| Working inference | See INFERENCE_GUIDE.md |
| Master plan | plan/DGGT_REPRODUCTION_PLAN.md |
| Paper | https://arxiv.org/abs/2512.03004 |
| Model Zoo | https://huggingface.co/xiaomi-research/dggt |
| Results | results/nuscenes_mode2/ |