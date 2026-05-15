# DGGT Project Analysis & Reproduction Plan

## Project Overview
**DGGT** (Driving Gaussian Grounded Transformer) is a feedforward 4D reconstruction framework for dynamic driving scenes from unposed RGB images. It's a research project from Xiaomi EV and Tsinghua University that jointly predicts:
- Camera poses (eliminating the need for pose calibration)
- 3D Gaussian scene maps 
- Dynamic motion/tracking
- Per-frame rendering

Key innovation: **Pose-free feedforward** - processes images in a single pass without per-scene optimization, enabling scalable 4D reconstruction for autonomous driving applications.

## Core Technology Architecture

### Main Components:
1. **Aggregator** (dggt/models/aggregator.py)
   - Vision Transformer backbone that processes multi-view image sequences
   - Extracts hierarchical image tokens and scene context

2. **VGGT Model** (dggt/models/vggt.py) - Main inference engine with heads:
   - **CameraHead**: Predicts camera intrinsics & extrinsics (pose-free)
   - **DPTHead**: Dense prediction transformer for point clouds and depth
   - **GaussianHead**: Predicts 3D Gaussian attributes (RGB, opacity, scale, rotation)
   - **TrackHead**: Motion tracking for dynamic objects
   - **InstanceHead/SemanticHead**: Instance and semantic segmentation
   - **SkyGaussian**: Models background sky

3. **Rendering Pipeline**:
   - gsplat: Fast 3D Gaussian splatting
   - Optional diffusion-based refinement for artifact reduction

4. **Dataset Support**:
   - Waymo Open Dataset
   - nuScenes
   - Argoverse2

### Key Capabilities:
- **Zero-shot transfer**: Train on Waymo → evaluate on Argoverse2/nuScenes without retraining
- **Variable frame support**: Works with 1-N input frames
- **Metrics**: PSNR, SSIM, LPIPS for quality evaluation
- **Frame interpolation**: Mode 3 enables inter-frame prediction

---

## Implementation Plan

### Phase 1: Environment Setup & Dependency Understanding
**Goal**: Install all dependencies and verify environment

**Tasks**:
1. Review requirements.txt (29 key dependencies: torch, transformers, gsplat, diffusers, kornia, hydra, etc.)
2. Install PyTorch with appropriate CUDA support (GPU required for efficient inference)
3. Install specialized libraries:
   - **gsplat**: 3D Gaussian splatting renderer
   - **lpips**: Perceptual loss (LPIPS metric)
   - **open3d**: 3D visualization
   - **kornia**: Geometric computer vision
   - **diffusers**: Stable Diffusion for refinement
4. Verify pre-trained model availability (VGGT-1B from HuggingFace)
5. Set up rerun-sdk for visualization/debugging

**Expected output**: Clean environment with all dependencies installed, no import errors

---

### Phase 2: Codebase Architecture Analysis
**Goal**: Deep understanding of how components fit together

**Tasks**:
1. **Aggregator Analysis** - How multi-view fusion works
   - Study patch embeddings (patch_size=14)
   - Understand temporal token aggregation
   - Review attention mechanisms

2. **VGGT Forward Pass** - Trace data flow:
   - Input: Image batch [B, T, C, H, W]
   - Aggregator output: Image tokens, aggregated tokens
   - Head predictions: Camera poses, Gaussians, motion, depth
   - Output: Reconstruction-ready features

3. **Loss Functions** - Training objectives:
   - Rendering loss (MSE against ground truth)
   - Lifespan loss for temporal consistency
   - Alpha-t temporal weighting scheme

4. **Dataset Pipeline** - Data loader structure:
   - WaymoOpenDataset class
   - Data preprocessing/normalization
   - Batch composition

**Expected output**: Detailed architecture documentation with data flow diagrams

---

### Phase 3: Dataset Workflow Understanding
**Goal**: Know how to prepare data for inference and training

**Tasks**:
1. **Review dataset documentation** (datasets/*.md):
   - Waymo download & preprocessing steps
   - nuScenes setup
   - Argoverse2 setup
   - Output format expectations

2. **Understand data structure**:
   - Input: Multi-view driving scene images (usually 5 cameras)
   - Output: 4D reconstructions with camera poses
   - Expected directory structure for each dataset

3. **Preprocessing requirements**:
   - Image normalization (T.ToTensor, color space)
   - Resolution handling (default 518x518)
   - Temporal sequences (default 4 frames)

**Expected output**: Document showing dataset setup steps for each format

---

### Phase 4a: Inference Pipeline - Reconstruction
**Goal**: Run zero-shot inference on the prepared nuScenes dataset using a Waymo-trained checkpoint

**Tasks**:
1. **Download and verify checkpoint**:
   - Required: `pretrained/model_latest_waymo.pth` from HuggingFace
   - Confirm file is readable by `inference_optimized.py`

2. **Use prepared nuScenes data (already available)**:
   - Dataset root: `data/nuscenes/processed_10Hz/mini`
   - Initial scenes: `000 001`
   - Expected structure: images, intrinsics/extrinsics, masks, dynamic masks

3. **Run optimized reconstruction inference**:
   - Script: `inference_optimized.py`
   - Command baseline:
     - `--image_dir data/nuscenes/processed_10Hz/mini`
     - `--scene_names 000 001`
     - `--use_fp16 --resolution 378 --sequence_length 2`
     - `--save_images --save_video --compute_metrics`

4. **Validate zero-shot run outputs**:
   - Confirm `results/nuscenes_inference/inference_summary.json`
   - Check scene-level debug timing files and success counts

5. **Evaluate and iterate settings**:
   - If stable, rerun at `--resolution 518 --sequence_length 4`
   - Record memory usage and per-scene timing

**Expected output**: Successful nuScenes inference run with saved rendered PNG/MP4 outputs, per-scene metrics, and summary/timing artifacts

---

### Phase 5: Training Pipeline - Understanding & Optional Execution
**Goal**: Understand training workflow; execute if datasets available

**Tasks**:
1. **Prepare training data**:
   - Download target dataset (Waymo/nuScenes/Argoverse2)
   - Run preprocessing scripts from datasets/*.md
   - Validate data structure

2. **Initialize training**:
   - Download pretrained VGGT-1B model
   - Set up distributed training (torchrun)
   - Configure hyperparameters (sequence_length=4, batch_size, epochs)

3. **Monitor training**:
   - Understand loss curves (rendering loss + lifespan loss)
   - Track metrics on validation set
   - Save checkpoints periodically

4. **Evaluate trained model**:
   - Run inference on test set
   - Compare with paper results
   - Test zero-shot transfer to other datasets

**Expected output**: Training logs, checkpoint saved, evaluation metrics

---

### Phase 4b: Novel View Rendering from Trajectory Offsets ✅
**Goal**: Render images from laterally/vertically offset camera positions to simulate lane changes or different viewpoints

**Status**: COMPLETE (2026-05-15)

**Key Discovery**: DGGT operates in **normalized coordinates**, not meters.
- Inter-frame camera translation ≈ 0.05 units
- Scene point cloud range: [-1.4, 12.5]
- Valid offset range: 0.01–0.05 (≈ 0.5–2m real-world)
- Offsets ≥0.5 cause scene to disappear (Gaussians fall off-screen)

**Script**: `render_novel_views.py`

**Implementation Details**:
1. Loads model, runs forward pass (same as inference.py mode 2)
2. Extracts 3D Gaussians from model predictions
3. Pre-computes sky background once (negligible diff at small offsets)
4. Applies world-coordinate offset to extrinsic matrix: `new_ext[:3,3] = ext[:3,3] + R @ offset`
5. Re-renders via gsplat `rasterization()` with `.float()` casts (dtype requirement)
6. Saves per-offset images + optional sweep video

**Validated Results** (scene 000, 7 lateral offsets):
| Offset | Mean Diff | Median | >20 pixels |
|--------|-----------|--------|------------|
| 0.000 | 0.00 | 0.0 | 0.0% |
| ±0.010 | ~10.4 | 3.0 | 14.3% |
| ±0.020 | ~15.2 | 4.0 | 21.1% |
| ±0.050 | ~27.2 | 10.5 | 35.8% |

**Output**: `results/novel_views/000/` with per-offset dirs + `offset_sweep.mp4`

---

### Phase 6: Advanced Features & Reproduction
**Goal**: Reproduce paper results with extensions

**Tasks**:
1. **Diffusion-based refinement**:
   - Understand diffusion model integration (third_party/DIFIX)
   - Impact on rendering quality and speed

2. **Frame interpolation**:
   - Mode 3: Interpolate between input frames
   - Configure interval parameter

3. **Cross-dataset evaluation**:
   - Replicate zero-shot + trained settings from paper
   - Quantitative comparison table

4. **Visualization & analysis**:
   - Dynamic motion tracking visualization
   - Camera pose estimation accuracy
   - 3D Gaussian distribution analysis

**Expected output**: Comparative results matching paper metrics

---

## Key Files & References

| File/Directory | Purpose |
|---|---|
| `train.py` | Training script (distributed) |
| `inference.py` | Inference script (all modes) |
| `dggt/models/vggt.py` | Main VGGT model |
| `dggt/models/aggregator.py` | Multi-view aggregation |
| `dggt/heads/` | Prediction heads (camera, Gaussian, tracking) |
| `dggt/layers/` | Vision transformer components |
| `datasets/` | Dataset preparation instructions |
| `requirements.txt` | All dependencies |
| `third_party/` | External utilities (DIFIX, TAPIP3D) |

## Dependencies Breakdown

**Core ML Framework**:
- torch, transformers, timm (Vision Transformer backbones)

**3D Graphics**:
- gsplat (3D Gaussian splatting)
- open3d (3D geometry)
- kornia (geometric transformations)
- sophuspy (Lie group math for poses)

**Rendering & Refinement**:
- diffusers (Stable Diffusion)
- lpips (perceptual loss)

**Data & Utilities**:
- h5py (data loading)
- hydra-core (configuration)
- av, imageio, scipy (media/math)

## Success Criteria

✅ **Phase 1**: All imports work, no missing dependencies
✅ **Phase 2**: Can trace end-to-end data flow through model
✅ **Phase 3**: Understand dataset structure and preprocessing
✅ **Phase 4a**: Inference produces visual outputs matching expected format
✅ **Phase 4b**: Novel views render correctly from offset trajectories
✅ **Phase 5**: (Optional) Model trains without errors on available data
✅ **Phase 6**: Results match or exceed paper benchmarks

## Notes & Considerations

- **GPU Required**: 3D Gaussian splatting and inference need GPU acceleration (CUDA)
- **Memory**: Inference typically requires 10-16GB VRAM; training may need 40GB+ for batch processing
- **Pose-free advantage**: No need for camera calibration, enabling true zero-shot transfer
- **Feedforward**: Single-pass inference (no per-scene optimization) = fast reconstruction
- **Research code**: May have rough edges; some paths might need adjustment
- **Pretrained weights**: Available for Waymo, Argoverse2, nuScenes from official release
