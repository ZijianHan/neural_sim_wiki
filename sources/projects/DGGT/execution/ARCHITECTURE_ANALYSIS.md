# DGGT Architecture Analysis

**Driving Gaussian Grounded Transformer — A Feedforward 4D Reconstruction Framework for Dynamic Driving Scenes**

> Generated from source-level analysis of the DGGT codebase.

---

## Table of Contents

1. [High-Level Overview](#1-high-level-overview)
2. [Model Architecture — VGGT](#2-model-architecture--vggt)
3. [Aggregator — Multi-View Alternating Attention](#3-aggregator--multi-view-alternating-attention)
4. [Vision Transformer Backbone — DINOv2](#4-vision-transformer-backbone--dinov2)
5. [Prediction Heads](#5-prediction-heads)
   - 5.1 [CameraHead](#51-camerahead)
   - 5.2 [DPTHead (Point & Depth)](#52-dpthead-point--depth)
   - 5.3 [GaussianHead](#53-gaussianhead)
   - 5.4 [TrackHead](#54-trackhead)
   - 5.5 [InstanceHead & SemanticHead](#55-instancehead--semantichead)
   - 5.6 [SkyGaussian](#56-skygaussian)
6. [Data Flow — End to End](#6-data-flow--end-to-end)
7. [Loss Functions & Training](#7-loss-functions--training)
8. [Rendering Pipeline](#8-rendering-pipeline)
9. [Key Design Patterns](#9-key-design-patterns)
10. [Memory & Parameter Analysis](#10-memory--parameter-analysis)

---

## 1. High-Level Overview

DGGT takes **multi-view, multi-frame driving images** and produces a **4D Gaussian Splatting** scene representation in a single feedforward pass. The architecture:

```
Input Images [B, S, 3, H, W]
        │
        ▼
┌─────────────────────────┐
│  DINOv2-ViT-L Backbone  │  ← Frozen pretrained patch embeddings
│  (Patch Embedding)       │
└────────────┬────────────┘
             │ patch tokens [B*S, P, 1024]
             ▼
┌─────────────────────────┐
│  Aggregator              │  ← 24 frame blocks + 24 global blocks
│  (Alternating Attention) │     alternating frame ↔ global attention
└────────────┬────────────┘
             │ 3 token streams:
             │  • aggregated_tokens (frame ∥ global = 2C)
             │  • image_tokens (dino ∥ frame ∥ global = 3C)
             │  • dino_tokens (raw DINOv2 features = C)
             ▼
┌─────────────────────────────────────────────────┐
│              Prediction Heads                    │
│  ┌──────────┐ ┌──────────┐ ┌───────────────┐   │
│  │ Camera   │ │ Point3D  │ │ Gaussian      │   │
│  │ Head     │ │ Head     │ │ Head          │   │
│  │ (2C→9)   │ │ (2C→4)   │ │ (3C→12)       │   │
│  └──────────┘ └──────────┘ └───────────────┘   │
│  ┌──────────┐ ┌──────────┐ ┌───────────────┐   │
│  │ Depth    │ │ Instance │ │ Semantic      │   │
│  │ Head     │ │ Head     │ │ Head          │   │
│  │ (2C→2)   │ │ (C→2)    │ │ (C→11)        │   │
│  └──────────┘ └──────────┘ └───────────────┘   │
│  ┌──────────┐ ┌──────────┐                      │
│  │ Track    │ │ Sky      │                      │
│  │ Head     │ │ Gaussian │                      │
│  └──────────┘ └──────────┘                      │
└─────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────┐
│  gsplat Rasterization   │  ← Differentiable Gaussian splatting
│  + Sky Background       │
└─────────────────────────┘
             │
             ▼
      Rendered Images [S, H, W, 3]
```

---

## 2. Model Architecture — VGGT

**File:** `dggt/models/vggt.py` (~108 lines)

The top-level `VGGT` class is a `nn.Module` that composes all sub-modules:

```python
class VGGT(nn.Module, PyTorchModelHubMixin):
    def __init__(self, img_size=518, patch_size=14, embed_dim=1024, semantic_num=10):
        self.aggregator   = Aggregator(img_size, patch_size, embed_dim)
        self.camera_head  = CameraHead(dim_in=2 * embed_dim)        # 2048-dim input
        self.point_head   = DPTHead(dim_in=2*embed_dim, output_dim=4, activation="inv_log")
        self.depth_head   = DPTHead(dim_in=2*embed_dim, output_dim=2, activation="exp")
        self.track_head   = TrackHead(dim_in=2*embed_dim, patch_size=patch_size)
        self.gs_head      = GaussianHead(dim_in=3*embed_dim, output_dim=12)  # 3+1+3+4+1
        self.instance_head = DPTHead(dim_in=embed_dim, output_dim=2, activation="linear")
        self.semantic_head = DPTHead(dim_in=embed_dim, output_dim=11, activation="linear")
        self.sky_model    = SkyGaussian()
```

### Forward Pass

The forward pass runs in two stages:

**Stage 1 — Aggregation:**
```python
aggregated_tokens_list, image_tokens_list, dino_token_list, image_feature, patch_start_idx = \
    self.aggregator(images)
```

**Stage 2 — Head Predictions (all in float32 via `autocast(enabled=False)`):**

| Head | Input Tokens | Output Key | Shape |
|------|-------------|------------|-------|
| `camera_head` | `aggregated_tokens_list` (2C) | `pose_enc` | `[B, S, 9]` |
| `point_head` | `aggregated_tokens_list` (2C) | `world_points`, `world_points_conf` | `[B, S, H, W, 3]`, `[B, S, H, W]` |
| `depth_head` | `aggregated_tokens_list` (2C) | `depth`, `depth_conf` | `[B, S, H, W, 1]`, `[B, S, H, W]` |
| `gs_head` | `image_tokens_list` (3C) | `gs_map`, `gs_conf` | `[B, S, H, W, 11]`, `[B, S, H, W]` |
| `instance_head` | `dino_token_list` (C) | `dynamic_conf` | `[B, S, H, W, 1]` |
| `semantic_head` | `dino_token_list` (C) | `semantic_logits` | `[B, S, H, W, 10]` |
| `track_head` | `aggregated_tokens_list` (2C) | `track`, `vis`, `conf` | trajectory outputs |

**Key insight:** Different heads consume **different token streams** at different dimensionalities. The geometry-related heads (camera, point, depth) use the fused frame+global tokens (2C=2048). The Gaussian head uses a richer 3C=3072 stream that includes DINOv2 features. The instance/semantic heads use raw DINOv2 features (C=1024) since they benefit from pretrained semantics.

---

## 3. Aggregator — Multi-View Alternating Attention

**File:** `dggt/models/aggregator.py` (~354 lines)

The Aggregator is the core of DGGT. It implements **alternating attention** between intra-frame and cross-frame contexts.

### Architecture

```python
class Aggregator(nn.Module):
    def __init__(self, img_size=518, patch_size=14, embed_dim=1024,
                 depth=24, num_heads=16, mlp_ratio=4.0,
                 patch_embed="dinov2_vitl14_reg",
                 aa_order=["frame", "global"], aa_block_size=1):

        self.patch_embed    # DINOv2 ViT-Large backbone
        self.frame_blocks   # 24 transformer blocks (intra-frame attention)
        self.global_blocks  # 24 transformer blocks (cross-frame attention)
        self.camera_token   # nn.Parameter(1, 2, 1, embed_dim) — 2 variants
        self.register_token # nn.Parameter(1, 2, num_register, embed_dim)
        self.rope           # 2D Rotary Position Embedding
```

### Alternating Attention Pattern

The aggregator alternates between two attention types across 24 iterations:

```
For each of 24 blocks:
    1. Frame Attention:  tokens shape [B*S, P, C]  → attend within each frame
    2. Global Attention: tokens shape [B, S*P, C]  → attend across all frames
```

- **Frame attention** keeps `B*S` in the batch dimension — each frame's tokens attend only to other tokens in the same frame.
- **Global attention** reshapes to `B` batch with `S*P` sequence length — all tokens across all frames can attend to each other.

```python
def _process_frame_attention(self, tokens, B, S, P, C, frame_idx, pos):
    # tokens: [B*S, P, C] — each frame is independent
    tokens = self.frame_blocks[frame_idx](tokens, pos=pos)
    return tokens

def _process_global_attention(self, tokens, B, S, P, C, global_idx, pos):
    # tokens: [B, S*P, C] — all frames cross-attend
    tokens = self.global_blocks[global_idx](tokens, pos=pos)
    return tokens
```

### Special Tokens

The aggregator prepends two types of special tokens before each frame's patch tokens:

1. **Camera token** (1 per frame) — carries camera pose information
2. **Register tokens** (4 per frame) — learnable "scratch pad" tokens from DINOv2

Both have **two variants** (index 0 for the first/reference frame, index 1 for all others), expanded via `slice_expand_and_flatten()`:

```python
self.camera_token = nn.Parameter(torch.randn(1, 2, 1, embed_dim))
# Token sequence per frame: [camera, reg0, reg1, reg2, reg3, patch0, patch1, ...]
self.patch_start_idx = 1 + num_register_tokens  # = 5
```

### Three Output Token Streams

The aggregator returns three distinct feature streams constructed from intermediates:

```python
# 1. aggregated_tokens: frame ∥ global concatenated → dim = 2C = 2048
concat_inter = torch.cat([frame_intermediates[i], global_intermediates[i]], dim=-1)

# 2. image_tokens: dino ∥ frame ∥ global concatenated → dim = 3C = 3072
concat_inter_with_tokens = torch.cat([dino_tokens[i], frame_intermediates[i],
                                       global_intermediates[i]], dim=-1)

# 3. dino_tokens: raw DINOv2 intermediate features → dim = C = 1024
# Extracted via patch_embed.get_intermediate_layers(images, n=24)
```

This yields 24 intermediate outputs per stream (one per block pair).

---

## 4. Vision Transformer Backbone — DINOv2

**File:** `dggt/layers/vision_transformer.py`

The patch embedding backbone is a **DINOv2 ViT-Large** with register tokens:

```python
# Default configuration: dinov2_vitl14_reg
DinoVisionTransformer(
    patch_size=14,
    embed_dim=1024,
    depth=24,
    num_heads=16,
    mlp_ratio=4,
    num_register_tokens=4,
)
```

### Key Properties (ViT-Large)
- **Input:** 518×518 images → 37×37 = 1369 patches
- **Patch size:** 14×14 pixels
- **Embedding dim:** 1024
- **Transformer depth:** 24 blocks
- **Attention heads:** 16 (head_dim = 64)
- **MLP ratio:** 4× (hidden dim = 4096)

### Patch Processing Pipeline

```
Image [B*S, 3, 518, 518]
  → PatchEmbed (Conv2d 14×14, stride 14) → [B*S, 1369, 1024]
  → Prepend cls_token + register_tokens
  → Add interpolated positional embedding
  → 24 transformer blocks
  → LayerNorm
  → Extract patch tokens (drop cls + register)
```

The DINOv2 backbone provides both:
- Final output tokens for initial patch features
- **All 24 intermediate layer outputs** via `get_intermediate_layers(images, n=24)` for the DPT multi-scale fusion

### Transformer Block

**File:** `dggt/layers/block.py`

Each block follows a standard pre-norm architecture with LayerScale:

```python
class Block(nn.Module):
    def forward(self, x, pos=None):
        x = x + LayerScale(Attention(LayerNorm(x), pos=pos))  # self-attention
        x = x + LayerScale(MLP(LayerNorm(x)))                 # feedforward
        return x
```

### Attention with RoPE

**File:** `dggt/layers/attention.py`, `dggt/layers/rope.py`

The attention uses **QK normalization** and **2D Rotary Position Embeddings (RoPE)**:

```python
class Attention(nn.Module):
    def forward(self, x, pos=None):
        q, k, v = self.qkv(x).chunk(3)
        q, k = self.q_norm(q), self.k_norm(k)      # QK normalization
        if self.rope:
            q = self.rope(q, pos)                    # 2D RoPE
            k = self.rope(k, pos)
        return F.scaled_dot_product_attention(q, k, v)  # fused attention
```

The 2D RoPE splits features into vertical and horizontal halves, applying 1D rotary embeddings independently to each spatial dimension. Base frequency is 100.

---

## 5. Prediction Heads

### 5.1 CameraHead

**File:** `dggt/heads/camera_head.py`

Predicts **camera extrinsics and intrinsics** using iterative refinement on camera tokens.

**Architecture:**
- Input: camera tokens from last aggregation block `[B, S, 2048]`
- Trunk: 4 transformer blocks with adaptive layer normalization (AdaLN)
- Output: 9D pose encoding per frame = `[T(3), quat(4), FoV(2)]`

**Iterative refinement (4 iterations):**
```python
def trunk_fn(self, pose_tokens, num_iterations=4):
    for iter in range(num_iterations):
        # AdaLN modulation from previous prediction
        shift, scale, gate = self.poseLN_modulation(embed_pose(prev_pred))
        modulated = gate * modulate(adaln_norm(pose_tokens), shift, scale)
        modulated = modulated + pose_tokens  # residual

        # Trunk processing + delta prediction
        delta = self.pose_branch(self.trunk(modulated))
        pred_pose_enc = pred_pose_enc + delta  # accumulate

        # Activate: translation=linear, quaternion=linear, FoV=relu
        activated = activate_pose(pred_pose_enc)
```

Key design: Each iteration **detaches** the previous prediction to prevent backprop through time, making each refinement step independently optimizable.

### 5.2 DPTHead (Point & Depth)

**File:** `dggt/heads/dpt_head.py`

Implements the **DPT (Dense Prediction Transformer)** architecture for pixel-dense outputs. Used by multiple heads with different configurations.

**Multi-scale feature fusion:**
```python
# Extract features from 4 intermediate transformer layers
intermediate_layer_idx = [4, 11, 17, 23]  # layers from aggregator

# Each layer's tokens → project → resize to different scales
layer_1 = ConvTranspose2d(×4 upsample)    # highest resolution
layer_2 = ConvTranspose2d(×2 upsample)
layer_3 = Identity()                       # native patch resolution
layer_4 = Conv2d(×0.5 downsample)          # lowest resolution

# Bottom-up refinement fusion
out = refinenet4(layer_4)                  # start from coarsest
out = refinenet3(out, layer_3)             # fuse with next scale
out = refinenet2(out, layer_2)
out = refinenet1(out, layer_1)             # finest scale

# Final convolutions → output_dim channels → activation
```

**Instantiations in VGGT:**

| Head | `dim_in` | `output_dim` | Activation | Conf Activation |
|------|----------|-------------|------------|-----------------|
| `point_head` | 2048 | 4 (xyz+conf) | `inv_log` | `expp1` |
| `depth_head` | 2048 | 2 (depth+conf) | `exp` | `sigmoid` |
| `instance_head` | 1024 | 2 | `linear` | — |
| `semantic_head` | 1024 | 11 (10 classes+conf) | `linear` | — |

**Activation functions** (`head_act.py`):
- `inv_log`: `sign(y) * (exp(|y|) - 1)` — maps network output to unbounded 3D coordinates
- `exp`: `exp(y)` — ensures positive depth values
- `expp1`: `1 + exp(y)` — confidence ≥ 1
- `sigmoid`: standard sigmoid for [0,1] confidence

### 5.3 GaussianHead

**File:** `dggt/heads/dpt_head.py` (class `GaussianHead`)

Variant of DPTHead specifically for predicting **3D Gaussian Splatting attributes** per pixel.

```python
class GaussianHead(DPTHead):
    # dim_in = 3 * embed_dim = 3072 (uses richer image_tokens)
    # output_dim = 3 + 1 + 3 + 4 + 1 = 12 channels:
    #   RGB color:     3 channels → sigmoid activation
    #   Opacity:       1 channel  → sigmoid activation
    #   Scale:         3 channels → 0.1 * softplus activation
    #   Rotation:      4 channels → L2 normalize (quaternion)
    #   Confidence:    1 channel  → relu → 1/(x + ε) (inverse)
```

The `gs_activate_head` function in `head_act.py` splits and activates:

```python
def gs_activate_head(out, sh_degree=None):
    fmap = out.permute(0, 2, 3, 1)  # B,H,W,C
    color    = sigmoid(fmap[..., :3])
    opacity  = sigmoid(fmap[..., 3:4])
    scale    = 0.1 * softplus(fmap[..., 4:7])
    rotation = normalize(fmap[..., 7:11])
    conf     = relu(fmap[..., -1])
    conf_out = 1 / (conf + 1e-6)  # inverse confidence
    return cat([color, opacity, scale, rotation]), conf_out
```

### 5.4 TrackHead

**File:** `dggt/heads/track_head.py`

Predicts **point trajectories** across frames, combining DPT feature extraction with a correlation-based tracker.

```python
class TrackHead(nn.Module):
    def __init__(self, dim_in, patch_size=14, features=128, iters=4):
        self.feature_extractor = DPTHead(
            dim_in=dim_in, features=128,
            feature_only=True,  # output features, no activation
            down_ratio=2,       # half resolution for efficiency
        )
        self.tracker = BaseTrackerPredictor(
            latent_dim=128,
            corr_levels=7,      # correlation pyramid levels
            corr_radius=4,      # search radius
            hidden_size=384,
        )

    def forward(self, aggregated_tokens_list, images, patch_start_idx, query_points):
        feature_maps = self.feature_extractor(aggregated_tokens_list, images, patch_start_idx)
        # feature_maps: [B, S, 128, H//2, W//2]
        coord_preds, vis_scores, conf_scores = self.tracker(
            query_points=query_points, fmaps=feature_maps, iters=4
        )
        return coord_preds, vis_scores, conf_scores
```

### 5.5 InstanceHead & SemanticHead

Both are standard `DPTHead` instances operating on **raw DINOv2 features** (dim=1024):

```python
# InstanceHead: binary dynamic/static classification
self.instance_head = DPTHead(dim_in=embed_dim, output_dim=2, activation="linear")
# Output: dynamic_conf [B, S, H, W, 1] — sigmoid applied at training time

# SemanticHead: 10-class semantic segmentation
self.semantic_head = DPTHead(dim_in=embed_dim, output_dim=11, activation="linear")
# Output: semantic_logits [B, S, H, W, 10] — CrossEntropyLoss at training
```

**Semantic classes (Waymo):** Road, Building, Vegetation, Vehicle, Person, Cyclist, Traffic Sign, Truck, Sidewalk, Sky.

### 5.6 SkyGaussian

**File:** `dggt/models/sky.py`

A dedicated module for **background/sky rendering** using a Fibonacci sphere of fixed Gaussians:

```python
class SkyGaussian(nn.Module):
    def __init__(self, resolution=300, radius=50):
        # 90,000 fixed 3D Gaussians on a Fibonacci sphere
        self.bg_pcd     # [90000, 3] — fixed positions on sphere
        self.bg_opacity  # [90000, 1] — all ones
        self.bg_quat     # [90000, 4] — identity quaternions
        self.bg_scales   # [90000, 3] — log of KNN-based avg distance

        # Learned color predictor
        self.bg_field = MLP(in_dim=3, layers=2, width=64, out_dim=6, out_act=Tanh)
```

**Pipeline:**
1. Project sphere points onto source views to sample image features
2. MLP predicts RGB color + scale residual from sampled features
3. Rasterize sky Gaussians using gsplat for each target view
4. Composite: `final = alpha * foreground + (1 - alpha) * sky_render`

---

## 6. Data Flow — End to End

### Input Processing

```
Raw images [B, S, 3, H, W]  (range [0,1])
  ↓ ImageNet normalization: (img - mean) / std
  ↓ Reshape to [B*S, 3, H, W]
  ↓ DINOv2 ViT-L patch embedding
  ↓ → patch_tokens [B*S, 1369, 1024]
  ↓ + get_intermediate_layers(n=24) → 24 intermediate feature tensors
  ↓ Prepend camera_token + register_tokens
  ↓ → tokens [B*S, 1374, 1024]   (5 special + 1369 patch tokens)
```

### Aggregation

```
tokens [B*S, 1374, 1024]
  ↓ 24 alternating attention iterations:
  │   Frame block:  [B*S, 1374, 1024] → self-attention within each frame
  │   Global block: [B, S*1374, 1024] → cross-attention across all frames
  │   → Collect frame_intermediates and global_intermediates
  ↓
  ↓ Construct 3 output streams:
  │   aggregated [24 × B, S, 1374, 2048] = frame ∥ global
  │   image_tokens [24 × B, S, 1374, 3072] = dino ∥ frame ∥ global
  │   dino_tokens [24 × B, S, 1374, 1024] = raw DINOv2
  ↓
  ↓ Also return: image_feature [B, S, 37, 37, 1024] (reshaped patches)
```

### Prediction

```
aggregated_tokens → CameraHead → pose_enc [B, S, 9]
                  → PointHead  → world_points [B, S, H, W, 3]
                  → DepthHead  → depth [B, S, H, W, 1]
image_tokens      → GaussianHead → gs_map [B, S, H, W, 11]
dino_tokens       → InstanceHead → dynamic_conf [B, S, H, W]
                  → SemanticHead → semantic_logits [B, S, H, W, 10]
```

### Rendering (in train.py / inference.py)

```
pose_enc → pose_encoding_to_extri_intri() → extrinsics [S,3,4], intrinsics [S,3,3]
depth + extrinsics + intrinsics → unproject_depth_map_to_point_map() → points [S,H,W,3]
gs_map → split into: RGB, opacity, scale, rotation per pixel

For each target view:
  1. Combine static + dynamic Gaussians
  2. Apply temporal opacity weighting: α(t) = α · exp(σ·(t₀-t)²)
  3. gsplat.rasterization(means, quats, scales, opacities, colors,
                          viewmats, Ks, width, height) → rendered [H,W,3], alpha [H,W,1]
  4. Sky compositing: final = alpha * rendered + (1-alpha) * sky_render
```

---

## 7. Loss Functions & Training

**File:** `train.py`

### Training Configuration
- **Frozen modules:** aggregator, camera_head, point_head, depth_head, track_head
- **Trainable modules:** `gs_head`, `instance_head`, `sky_model`
- **Optimizer:** AdamW with per-head learning rates
  - `gs_head`: 4e-5
  - `instance_head`: 4e-5
  - `sky_model`: 1e-4
- **Scheduler:** Warmup (1000 steps) + cosine annealing
- **Data:** WaymoOpenDataset, sequence_length=4, batch_size=1, DDP training

### Loss Components

| Loss | Weight | Formula |
|------|--------|---------|
| **Reconstruction (L1)** | 1.0 | `L1(rendered_image, target_image)` |
| **Sky mask** | 1.0 | `L1(alpha, 1 - sky_mask)` |
| **Lifespan regularization** | 0.01 | `mean(|1 / (γ + ε)|)` — prevents infinite confidence |
| **Dynamic mask (BCE)** | 0.05 | `BCEWithLogits(dynamic_pred, dynamic_gt)` |
| **LPIPS perceptual** | 0.05 (ramped) | `LPIPS_alex(rendered, target)` — enabled after warmup |
| **Semantic (CE)** | 0.01 | `CrossEntropy(semantic_logits, gt_labels)` — commented out |

### Temporal Opacity Weighting

A key innovation for 4D consistency — Gaussian opacity is modulated by temporal distance:

```python
def alpha_t(t, t0, alpha, gamma0=1, gamma1=0.1):
    """Temporal opacity: decays with squared time distance, modulated by learned confidence"""
    sigma = log(gamma1) / (gamma0² + ε)   # gamma0 = per-Gaussian learned confidence
    conf = exp(sigma * (t0 - t)²)          # Gaussian decay in time
    return alpha * conf
```

This allows each Gaussian to have a learned "lifespan" — Gaussians at time `t0` contribute less to views at distant times.

---

## 8. Rendering Pipeline

The rendering uses **gsplat** (`gsplat.rendering.rasterization`) for differentiable Gaussian splatting:

```python
renders, alphas, _ = rasterization(
    means=world_points,        # [N, 3] Gaussian centers
    quats=rotation,            # [N, 4] quaternion rotations
    scales=scales,             # [N, 3] log-scale
    opacities=opacity,         # [N] opacity values
    colors=rgbs,               # [N, 3] RGB colors
    viewmats=extrinsic[None],  # [1, 4, 4] camera-to-world
    Ks=intrinsic[None],        # [1, 3, 3] camera intrinsics
    width=W, height=H,
)
# renders: [1, H, W, 3], alphas: [1, H, W, 1]
```

**Static/Dynamic decomposition:**
- Static Gaussians: all points with opacity weighted by `(1 - dynamic_sigmoid)`
- Dynamic Gaussians: per-frame points with opacity weighted by `dynamic_sigmoid`
- Both sets are concatenated and rendered together per target view

---

## 9. Key Design Patterns

### 1. Three-Stream Token Architecture
Unlike standard ViTs that produce one feature stream, DGGT constructs three streams at different dimensionalities to serve different prediction tasks:
- **2C (2048):** Geometry (camera, points, depth) — needs both local and global context
- **3C (3072):** Gaussians — additionally needs raw appearance features
- **C (1024):** Semantics — benefits from pretrained DINOv2 representations

### 2. Frozen Backbone, Trainable Heads
The DINOv2 backbone and aggregator are pretrained (likely from VGGT). Only the Gaussian-related heads are fine-tuned, enabling efficient adaptation to driving scenes.

### 3. DPT as Universal Dense Head
The DPT (Dense Prediction Transformer) architecture is reused across 5 different heads with varying configurations, providing a consistent multi-scale feature fusion approach.

### 4. Iterative Refinement
Both the CameraHead (4 iterations with AdaLN) and TrackHead (4 correlation iterations) use iterative refinement with detached gradients between iterations.

### 5. First-Frame Asymmetry
Special tokens (camera + register) have two learned variants: one for the reference frame (index 0) and one for all other frames. This acknowledges the reference frame's special role in multi-view reconstruction.

---

## 10. Memory & Parameter Analysis

### Estimated Parameter Counts

| Component | Parameters (approx.) | Notes |
|-----------|---------------------|-------|
| **DINOv2 ViT-L backbone** | ~304M | 24 blocks × (4 × 1024² qkv + 4 × 1024² mlp) |
| **Aggregator frame_blocks** | ~304M | 24 blocks, same architecture as DINOv2 |
| **Aggregator global_blocks** | ~304M | 24 blocks, same architecture as DINOv2 |
| **CameraHead** | ~70M | 4 trunk blocks (2048-dim) + MLP |
| **PointHead (DPT)** | ~15M | 4 projection convs + 4 refinenet blocks |
| **DepthHead (DPT)** | ~15M | Same DPT architecture |
| **GaussianHead (DPT)** | ~20M | Larger input dim (3072) |
| **InstanceHead (DPT)** | ~10M | Smaller input dim (1024) |
| **SemanticHead (DPT)** | ~10M | Same as instance |
| **TrackHead** | ~5M | DPT feature extractor + tracker |
| **SkyGaussian** | ~0.1M | Small MLP + fixed buffers |
| **Total** | **~1.05B** | Approximate |

### Tensor Dimensions at 518×518 Input

| Tensor | Shape | Size (float32) |
|--------|-------|----------------|
| Input images (S=4) | `[1, 4, 3, 518, 518]` | 12.8 MB |
| Patch tokens | `[4, 1369, 1024]` | 22.4 MB |
| Tokens with specials | `[4, 1374, 1024]` | 22.5 MB |
| Aggregated output (1 layer) | `[1, 4, 1374, 2048]` | 45 MB |
| All 24 aggregated layers | | ~1.1 GB |
| DINOv2 24 intermediates | | ~0.5 GB |
| Per-pixel outputs (H×W = 518²) | `[1, 4, 518, 518, C]` | varies |

### Memory Optimization Strategies
- **Frame chunking:** DPT heads process frames in chunks of 8 to limit peak memory
- **Gradient freezing:** Only GS/instance/sky heads are trainable, reducing activation memory
- **Sky chunking:** SkyGaussian renders in chunks of 4 views
- **Float32 heads:** Prediction heads run in float32 (disabled autocast) for numerical precision
- **Explicit memory cleanup:** `del` statements after fusion stages

---

*Analysis generated from DGGT codebase at commit HEAD. All line numbers and code references correspond to the files as analyzed.*
