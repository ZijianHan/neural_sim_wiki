# DGGT Dataset Workflow — Comprehensive Reference

> **Last updated:** auto-generated from source analysis of the DGGT repository.
>
> This document covers every dataset supported by DGGT, the unified processed
> format the code expects at runtime, the preprocessing pipelines that produce
> that format, and the `WaymoOpenDataset` / `NuScenesDataset` loader classes in
> `datasets/dataset.py`.

---

## Table of Contents

1. [Supported Datasets](#1-supported-datasets)
   - [Waymo Open Dataset](#11-waymo-open-dataset)
   - [nuScenes](#12-nuscenes)
   - [Argoverse 2](#13-argoverse-2)
2. [Unified Processed Data Format](#2-unified-processed-data-format)
   - [Directory Layout](#21-directory-layout)
   - [File Naming Conventions](#22-file-naming-conventions)
   - [Per-Dataset Variations](#23-per-dataset-variations)
3. [Preprocessing Pipeline](#3-preprocessing-pipeline)
   - [Environment Setup](#31-environment-setup)
   - [Waymo Preprocessing](#32-waymo-preprocessing)
   - [nuScenes Preprocessing](#33-nuscenes-preprocessing)
   - [Argoverse 2 Preprocessing](#34-argoverse-2-preprocessing)
   - [Mask Extraction (All Datasets)](#35-mask-extraction-all-datasets)
4. [Dataset Loader — `WaymoOpenDataset`](#4-dataset-loader--waymopendataset)
   - [Constructor Parameters](#41-constructor-parameters)
   - [Directory Expectations](#42-directory-expectations)
   - [Modes of Operation](#43-modes-of-operation)
   - [Returned Dictionaries](#44-returned-dictionaries)
   - [`NuScenesDataset` Subclass](#45-nuscenesdataset-subclass)
5. [Key Paths & Naming at Runtime](#5-key-paths--naming-at-runtime)
6. [nuScenes-Specific Notes (v1.0-mini)](#6-nuscenes-specific-notes-v10-mini)
   - [Required Symlinks](#61-required-symlinks)
   - [Frame Interpolation](#62-frame-interpolation)
   - [Camera Identifiers](#63-camera-identifiers)
7. [Mask Extraction Details](#7-mask-extraction-details)
8. [Troubleshooting & FAQ](#8-troubleshooting--faq)
9. [Quick-Start Cheat Sheet](#9-quick-start-cheat-sheet)

---

## 1. Supported Datasets

### 1.1 Waymo Open Dataset

| Property | Value |
|---|---|
| **Source** | [waymo.com/open](https://waymo.com/open/) |
| **Raw format** | `.tfrecord` files (scene-flow version recommended) |
| **Splits** | `training` (799 scenes), `validation` (203 scenes) |
| **Cameras** | 5 cameras (IDs 0–4); front camera = `0` |
| **LiDAR** | 64-beam, stored as `.bin` |
| **Native FPS** | 10 Hz |
| **Scene naming** | Alphabetically sorted; index = line number − 1 in `data/waymo_val_list.txt` |
| **Preprocessing script** | `datasets/preprocess_waymo.py` (dedicated) **or** `datasets/preprocess.py --dataset waymo` |
| **Processor class** | `datasets.waymo.waymo_preprocess.WaymoProcessor` (via `preprocess.py`) / `datasets.waymo.waymo_preprocess_.WaymoProcessor` (via `preprocess_waymo.py`) |

**Download example:**

```bash
python datasets/waymo/waymo_download.py \
    --target_dir ./data/waymo/raw/validation \
    --split_file  data/waymo_val_list.txt \
    --scene_ids 8 23 150
```

### 1.2 nuScenes

| Property | Value |
|---|---|
| **Source** | [nuscenes.org](https://www.nuscenes.org/) |
| **Raw format** | nuScenes database (JSON + binary blobs) |
| **Splits** | `v1.0-mini` (10 scenes), `v1.0-trainval`, `v1.0-test` |
| **Cameras** | 6 cameras (IDs 0–5) |
| **LiDAR** | 32-beam, stored as `.bin` |
| **Native FPS** | 2 Hz (keyframes); interpolated up to 10 Hz |
| **Interpolation** | `interpolate_N = 4` → 10 Hz; `interpolate_N = 5` → 12 Hz (may drop frames) |
| **Dev toolkit** | `pip install nuscenes-devkit` |
| **Preprocessing script** | `datasets/preprocess.py --dataset nuscenes` |
| **Processor class** | `datasets.nuscenes.nuscenes_preprocess.NuScenesProcessor` |
| **Processed output dir** | `data/nuscenes/processed_10Hz/<split>/` |
| **Pose directory name** | `lidar_pose/` (not `ego_pose/`) |

### 1.3 Argoverse 2

| Property | Value |
|---|---|
| **Source** | [argoverse.org/av2.html](https://www.argoverse.org/av2.html) |
| **Raw format** | Sensor dataset (Feather/Parquet + images) |
| **Download tool** | `s5cmd` (S3 bucket) |
| **Cameras** | 7 ring cameras + 2 stereo cameras |
| **LiDAR** | 2 × 32-beam, stored as `.bin` |
| **Native FPS** | 10 Hz |
| **Dev toolkit** | `pip install av2` (requires Rust/rustup) |
| **Preprocessing script** | `datasets/preprocess.py --dataset argoverse` |
| **Processor class** | `datasets.argoverse.argoverse_preprocess.ArgoVerseProcessor` |
| **Scene ordering** | Alphabetically sorted UUIDs stored in `data/argoverse_train_list.txt` |

---

## 2. Unified Processed Data Format

All three datasets are converted into a **single canonical layout** so that
`WaymoOpenDataset` (and its subclasses) can load any of them without
dataset-specific branching at training/inference time.

### 2.1 Directory Layout

```
data/<dataset>/processed/<split>/
├── 000/                          # Scene directory (zero-padded 3-digit index)
│   ├── images/                   # RGB camera images
│   ├── sky_masks/                # Binary sky segmentation masks
│   ├── dynamic_masks/            # Bounding-box-based dynamic object masks
│   │   ├── human/                # Per-class sub-masks (human)
│   │   └── vehicle/              # Per-class sub-masks (vehicle)
│   ├── fine_dynamic_masks/       # SegFormer-refined dynamic masks
│   │   ├── all/                  # Union of human + vehicle (loaded at runtime)
│   │   ├── human/
│   │   └── vehicle/
│   ├── custom_masks/             # Multi-class semantic masks (10 classes)
│   ├── intrinsics/               # Camera intrinsic matrices
│   ├── extrinsics/               # Camera-to-ego extrinsic matrices
│   ├── ego_pose/                 # Ego-vehicle-to-world 4×4 poses (Waymo/Argo)
│   │   (or lidar_pose/)          # LiDAR-to-world poses (nuScenes)
│   ├── lidar/                    # LiDAR point clouds
│   ├── depth_flows_4/            # (Waymo only) Down-sampled depth flow maps
│   ├── ground_label_4/           # (Waymo only) Ground labels from point clouds
│   ├── objects/                  # Object tracking information
│   └── humanpose/                # SMPL body pose data (smpl.pkl)
├── 001/
├── 002/
└── ...
```

**Concrete examples per dataset:**

| Dataset | Processed root |
|---|---|
| Waymo | `data/waymo/processed/validation/000/` |
| nuScenes | `data/nuscenes/processed_10Hz/mini/001/` |
| Argoverse 2 | `data/argoverse/processed/training/000/` |

### 2.2 File Naming Conventions

All file names use a consistent `{timestep}_{camera_id}` pattern:

| Directory | Pattern | Example | Notes |
|---|---|---|---|
| `images/` | `{timestep:03d}_{cam_id}.jpg` | `007_0.jpg` | Frame 7, camera 0 |
| `sky_masks/` | `{timestep:03d}_{cam_id}.png` | `007_0.png` | Binary (0/255) |
| `dynamic_masks/` | `{timestep:03d}_{cam_id}.png` | `007_0.png` | Per sub-dir |
| `fine_dynamic_masks/all/` | `{timestep:03d}_{cam_id}.png` | `007_0.png` | Refined mask |
| `custom_masks/` | `{timestep:03d}_{cam_id}.png` | `007_0.png` | Multi-class |
| `intrinsics/` | `{cam_id}.txt` | `0.txt` | One file per camera |
| `extrinsics/` | `{cam_id}.txt` | `0.txt` | One file per camera |
| `ego_pose/` | `{timestep:03d}.txt` | `007.txt` | 4×4 matrix |
| `lidar/` | `{timestep:03d}.bin` | `007.bin` | Binary point cloud |
| `depth_flows_4/` | `{timestep:03d}_{cam_id}.npy` | `007_0.npy` | NumPy array |
| `ground_label_4/` | `{timestep:03d}.txt` | `007.txt` | Text labels |

**Camera IDs:**

- **Waymo:** 0 (front), 1 (front-left), 2 (front-right), 3 (side-left), 4 (side-right)
- **nuScenes:** 0–5 (six surround cameras)
- **Argoverse 2:** 0–6 (seven ring cameras) + stereo pair

### 2.3 Per-Dataset Variations

| Feature | Waymo | nuScenes | Argoverse 2 |
|---|---|---|---|
| Pose dir name | `ego_pose/` | `lidar_pose/` | `ego_pose/` |
| Dynamic mask dir | `fine-dynamic_masks/` **or** `fine_dynamic_masks/` | `dynamic_masks/` + `fine_dynamic_masks/` | `fine_dynamic_masks/` |
| Depth flow data | ✅ `depth_flows_4/` | ❌ | ❌ |
| Ground labels | ✅ `ground_label_4/` | ❌ | ❌ |
| Interpolated frames | ❌ | ✅ (configurable) | ❌ |
| Human pose | Optional | Optional | Optional |

---

## 3. Preprocessing Pipeline

### 3.1 Environment Setup

A **separate conda environment** is strongly recommended for data processing
because TensorFlow (Waymo) and mmcv (SegFormer) dependencies conflict with the
main training environment.

```bash
# Data processing env
conda create -n dggt_data python=3.10
conda activate dggt_data
pip install -r requirements_data.txt
```

For mask extraction, an additional env is needed:

```bash
# SegFormer env (PyTorch 1.8 required)
conda create -n segformer python=3.8
conda activate segformer
pip install torch==1.8.1+cu111 torchvision==0.9.1+cu111 torchaudio==0.8.1 \
    -f https://download.pytorch.org/whl/torch_stable.html
pip install timm==0.3.2 pylint debugpy opencv-python-headless attrs ipython \
    tqdm imageio scikit-image omegaconf
pip install mmcv-full==1.2.7 --no-cache-dir
git clone https://github.com/NVlabs/SegFormer && cd SegFormer && pip install .
```

### 3.2 Waymo Preprocessing

**Step 1 — Download raw data:**

```bash
python datasets/waymo/waymo_download.py \
    --target_dir ./data/waymo/raw/validation \
    --split_file data/waymo_val_list.txt \
    --scene_ids 8 23 150
```

**Step 2 — Run preprocessing (dedicated script):**

```bash
python datasets/preprocess_waymo.py \
    --data_root data/waymo/raw/ \
    --target_dir data/waymo/processed \
    --dataset waymo \
    --split validation \
    --scene_list_file data/waymo_val_list.txt \
    --num_workers 8 \
    --process_keys images lidar calib pose dynamic_masks ground \
    --json_folder_to_save data/annotations/waymo
```

**Or via the unified script:**

```bash
python datasets/preprocess.py \
    --data_root data/waymo/raw/ \
    --target_dir data/waymo/processed \
    --split training \
    --process_keys images lidar calib pose dynamic_masks objects \
    --workers 8 \
    --scene_ids 23 114 327 621 703 172 552 788
```

**Process keys** (selectable components):

| Key | Output |
|---|---|
| `images` | RGB images → `images/` |
| `lidar` | LiDAR point clouds → `lidar/` |
| `calib` | Intrinsics/extrinsics → `intrinsics/`, `extrinsics/` |
| `pose` | Ego poses → `ego_pose/` |
| `dynamic_masks` | Bounding-box masks → `dynamic_masks/` |
| `ground` | Ground labels → `ground_label_4/` (Waymo only) |
| `objects` | Tracking data → `objects/` |

**Scene list files** (used for index-to-name mapping):

- `data/waymo_val_list.txt` — validation split
- `data/dataset_scene_list/waymo_train_list.txt` — training split

The `preprocess_waymo.py` script will also auto-discover `.tfrecord` files if
the scene list file is empty.

### 3.3 nuScenes Preprocessing

**Step 1 — Download & symlink:**

```bash
mkdir -p ./data/nuscenes
ln -s $PATH_TO_NUSCENES ./data/nuscenes/raw
```

**Step 2 — Install toolkit:**

```bash
pip install nuscenes-devkit
```

**Step 3 — Run preprocessing:**

```bash
python datasets/preprocess.py \
    --data_root data/nuscenes/raw \
    --target_dir data/nuscenes/processed \
    --dataset nuscenes \
    --split v1.0-mini \
    --start_idx 0 \
    --num_scenes 10 \
    --interpolate_N 4 \
    --workers 32 \
    --process_keys images lidar calib dynamic_masks objects
```

> **Note:** The `--interpolate_N` flag is **only** valid for the `nuscenes`
> dataset. The script will error if used with other datasets.

**Output directory:** `data/nuscenes/processed_10Hz/<split>/` (the `_10Hz`
suffix is added by the processor when interpolation is enabled).

### 3.4 Argoverse 2 Preprocessing

**Step 1 — Install dependencies:**

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
export PATH=$HOME/.cargo/bin:$PATH
rustup default nightly
pip install av2
pip install "numpy==1.23.5"
```

**Step 2 — Download via s5cmd:**

```bash
export DATASET_NAME="sensor"
export TARGET_DIR="data/argoverse/raw"
mkdir -p $TARGET_DIR
s5cmd --no-sign-request cp "s3://argoverse/datasets/av2/$DATASET_NAME/*" $TARGET_DIR
```

**Step 3 — Run preprocessing:**

```bash
python -m datasets.preprocess \
    --data_root data/argoverse/raw \
    --target_dir data/argoverse/processed/training \
    --dataset argoverse \
    --start_idx 0 \
    --num_scenes 100 \
    --workers 64 \
    --process_keys images lidar calib pose dynamic_masks objects
```

### 3.5 Mask Extraction (All Datasets)

After base preprocessing, **sky masks** (required) and **fine dynamic masks**
(optional) are generated using SegFormer in a separate conda environment.

```bash
conda activate segformer
segformer_path=/path/to/SegFormer

python datasets/tools/extract_masks.py \
    --data_root data/<dataset>/processed/<split> \
    --segformer_path=$segformer_path \
    --checkpoint=$segformer_path/pretrained/segformer.b5.1024x1024.city.160k.pth \
    --start_idx 0 \
    --num_scenes 200 \
    --process_dynamic_mask
```

**What the script produces per scene:**

| Output directory | Content | Required? |
|---|---|---|
| `sky_masks/` | Binary sky masks (0 or 255) | **Yes** |
| `fine_dynamic_masks/all/` | Union of human + vehicle refined masks | Optional |
| `fine_dynamic_masks/human/` | SegFormer person + cyclist ∩ bbox mask | Optional |
| `fine_dynamic_masks/vehicle/` | SegFormer vehicle ∩ bbox mask | Optional |
| `custom_masks/` | Multi-class semantic (10 classes, 10–90 + 255) | Optional |

**Semantic class mapping in `custom_masks`:**

| Class | Cityscapes IDs | Pixel value |
|---|---|---|
| Road | 0 | 10 |
| Building | 2 | 20 |
| Vegetation | 8 | 30 |
| Vehicle | 13, 14, 15 | 40 |
| Person | 11 | 50 |
| Cyclist | 12, 17, 18 | 60 |
| Traffic Sign | 9 | 70 |
| Sidewalk | 1 | 80 |
| Sky | 10 | 90 |
| Other | — | 255 |

---

## 4. Dataset Loader — `WaymoOpenDataset`

Defined in `datasets/dataset.py`, this is the **primary data loader** for all
datasets. Despite its name, it handles Waymo, nuScenes, and Argoverse data
through the unified processed format.

### 4.1 Constructor Parameters

```python
class WaymoOpenDataset(Dataset):
    def __init__(
        self,
        image_dir,            # Root of processed data (e.g., "data/waymo/processed/validation")
        scene_names=None,     # List of scene directory names (e.g., ["000", "001", ...])
        sequence_length=None, # Number of frames per sample
        start_idx=-1,         # Starting frame index override
        mode=1,               # 1=train, 2=reconstruction, 3=interpolation
        views=1,              # 1=front-only, 3=multi-view (front + front-left + front-right)
        intervals=2,          # Frame stride for mode 3 (interpolation)
    )
```

**Defaults:**

- If `scene_names` is `None`, defaults to `["000", "001", ..., "098"]` (99 scenes).
- `mode` controls data loading behavior (see §4.3).
- `views=1` loads only camera `0`; `views=3` loads cameras `0`, `1`, `2`.

### 4.2 Directory Expectations

The constructor scans each scene directory and builds path lists. Here is
exactly what it expects:

```
{image_dir}/{scene_name}/
├── images/                      # REQUIRED — source of all frame paths
│   ├── 000_0.jpg                # {timestep:03d}_{cam_id}.jpg or .png
│   ├── 000_1.jpg
│   └── ...
├── sky_masks/                   # REQUIRED for training/inference
│   ├── 000_0.png
│   └── ...
├── fine_dynamic_masks/all/      # OPTIONAL — refined dynamic masks
│   ├── 000_0.png
│   └── ...
├── ego_pose/                    # REQUIRED — loaded as "extrinsic_paths"
│   ├── 000.txt                  # NOTE: variable name is misleading;
│   ├── 001.txt                  #   ego_pose/ stores world poses,
│   └── ...                      #   not camera extrinsics
├── extrinsics/                  # REQUIRED — camera-to-ego transform
│   └── 0.txt                    # Only camera 0's file is loaded
├── intrinsics/                  # REQUIRED — camera intrinsic matrix
│   └── 0.txt                    # (or 0.txt, 1.txt, 2.txt for views=3)
├── depth_flows_4/               # OPTIONAL — depth flow maps
│   ├── 000_0.npy
│   └── ...
└── custom_masks/                # OPTIONAL — multi-class semantic masks
    ├── 000_0.png
    └── ...
```

> **Important naming detail:** In the code, `self.extrinsic_paths` stores paths
> from `ego_pose/`, while `self.ego_paths` stores the single file from
> `extrinsics/0.txt`. This is counter-intuitive — the variable names are
> swapped relative to their semantic meaning.

### 4.3 Modes of Operation

| Mode | Purpose | Frame Selection | Key Behavior |
|---|---|---|---|
| **1** (Train) | Training with random sampling | Random start + random intervals within 20-frame window | `test_mode=False`, `load_flow=False` |
| **2** (Reconstruction) | Sequential reconstruction | Sequential from frame 0, stride = `interval` | `test_mode=True`, `load_flow=False`, includes depth |
| **3** (Interpolation) | Temporal interpolation eval | Sequential from frame 0, stride = `intervals` param | `test_mode=True`, `load_flow=True`, includes targets |

**Mode 1 — Training:**

- Picks random `start_idx` within `[0, len(images) - 21]`.
- Selects `sequence_length` frames: first is `start_idx`, rest are
  `start_idx + random_offsets` (offsets sampled from `[1, 19]`).
- Returns: `images`, `masks` (sky), `timestamps`, `dynamic_mask` (if available).

**Mode 2 — Pure Reconstruction:**

- Starts at frame 0, steps by `interval` (always 1 for mode 2).
- Loads exactly `sequence_length` frames.
- Returns: `images`, `masks`, `timestamps`, `gt_depth` (zero if unavailable).

**Mode 3 — Interpolation:**

- Starts at frame 0, steps by `intervals` parameter.
- Builds both sampled frames (`indices`) and dense target frames (`target_indices`).
- Returns: `images`, `targets`, `masks`, `target_masks`, `gt_depth`.

### 4.4 Returned Dictionaries

**Mode 1 (Training):**

```python
{
    "images":        Tensor[S, C, H, W],        # Preprocessed RGB (resized to 518px width)
    "masks":         Tensor[S, C, H, W],        # Sky masks
    "image_paths":   List[str],                  # Original file paths
    "timestamps":    ndarray[S],                 # Normalized timestamps
    "interval":      List[int],                  # Frame intervals
    "dynamic_mask":  Tensor[S, C, H, W],        # (optional) Fine dynamic masks
}
```

**Mode 2 (Reconstruction):**

```python
{
    "images":        Tensor[S, C, H, W],
    "image_paths":   List[str],
    "masks":         Tensor[S, C, H, W],
    "timestamps":    ndarray[S],
    "interval":      List[int],
    "dynamic_mask":  Tensor[S, C, H, W],        # (optional)
    "gt_depth":      Tensor[S, H, W],           # Depth flow or zeros
}
```

**Mode 3 (Interpolation):**

```python
{
    "images":        Tensor[S, C, H, W],        # Key frames
    "targets":       Tensor[T, C, H, W],        # All frames (key + interpolated)
    "masks":         Tensor[S, C, H, W],
    "target_masks":  Tensor[T, C, H, W],
    "image_paths":   List[str],
    "timestamps":    ndarray[S],
    "interval":      List[int],
    "dynamic_mask":  Tensor[T, C, H, W],        # (optional, on targets)
    "gt_depth":      Tensor[T, H, W],           # Depth flow or zeros
}
```

When `views=3`, tensor shapes become `[S*3, C, H, W]` (views interleaved:
frame0-cam0, frame0-cam1, frame0-cam2, frame1-cam0, ...).

### 4.5 `NuScenesDataset` Subclass

```python
class NuScenesDataset(WaymoOpenDataset):
    """Dataset wrapper for preprocessed nuScenes scenes in the DGGT layout."""
```

This is a thin subclass (currently inherits everything unchanged) that serves
as a semantic marker for nuScenes data. It relies entirely on the nuScenes
processed directory matching the same canonical layout that `WaymoOpenDataset`
expects. See §6 for the symlinks required to make this work.

---

## 5. Key Paths & Naming at Runtime

The code constructs paths using `os.path.join()`. Here is the exact mapping
from code variables to filesystem paths:

| Code expression | Filesystem path | Content |
|---|---|---|
| `os.path.join(image_dir, scene_name, "images")` | `<root>/<scene>/images/` | RGB images |
| `os.path.join(image_dir, scene_name, "sky_masks")` | `<root>/<scene>/sky_masks/` | Sky masks |
| `os.path.join(image_dir, scene_name, "fine_dynamic_masks/all")` | `<root>/<scene>/fine_dynamic_masks/all/` | Refined dynamic masks |
| `os.path.join(image_dir, scene_name, "ego_pose")` | `<root>/<scene>/ego_pose/` | Per-timestep pose `.txt` |
| `os.path.join(image_dir, scene_name, "extrinsics")` | `<root>/<scene>/extrinsics/` | Per-camera extrinsic `.txt` |
| `os.path.join(image_dir, scene_name, "intrinsics")` | `<root>/<scene>/intrinsics/` | Per-camera intrinsic `.txt` |
| `os.path.join(image_dir, scene_name, "depth_flows_4")` | `<root>/<scene>/depth_flows_4/` | Depth flow `.npy` |
| `os.path.join(image_dir, scene_name, "custom_masks")` | `<root>/<scene>/custom_masks/` | Semantic masks |

**File suffixes used for filtering:**

- Images: `_0.jpg`, `_0.png` (views=1) or `_{v}.jpg`, `_{v}.png` (views=3, v∈{0,1,2})
- Depth: `_0.npy` (views=1) or `_{v}.npy` (views=3)
- Poses: `.txt` (all files)
- Intrinsics: `{cam_id}.txt` — e.g., `0.txt`, `1.txt`, `2.txt`
- Extrinsics: `0.txt` (only camera 0 is loaded into `ego_paths`)

**Image preprocessing at load time:**

1. Open image, handle RGBA → RGB (blend onto white).
2. Resize width to **518 px**, height scaled proportionally and rounded to
   nearest multiple of 14.
3. If height > 518, center-crop to 518.
4. Convert to tensor (values in `[0, 1]`).
5. If images have different shapes, pad to max dimensions.

---

## 6. nuScenes-Specific Notes (v1.0-mini)

### 6.1 Required Symlinks

The nuScenes preprocessor outputs directories named differently from what
`WaymoOpenDataset` expects. You **must** create symlinks to bridge the gap:

```bash
cd data/nuscenes/processed_10Hz/mini

for scene_dir in */; do
    # The loader reads from "fine_dynamic_masks/" but nuScenes produces "dynamic_masks/"
    ln -sfn dynamic_masks "${scene_dir}fine_dynamic_masks"

    # The loader reads from "ego_pose/" but nuScenes produces "lidar_pose/"
    ln -sfn lidar_pose "${scene_dir}ego_pose"
done
```

**Why these symlinks are needed:**

| What the loader expects | What nuScenes preprocessing produces | Fix |
|---|---|---|
| `fine_dynamic_masks/all/` | `dynamic_masks/{timestep}_{cam}.png` | Symlink `fine_dynamic_masks` → `dynamic_masks` |
| `ego_pose/{timestep}.txt` | `lidar_pose/{timestep}.txt` | Symlink `ego_pose` → `lidar_pose` |

> **Note:** If you also run the SegFormer mask extraction step, it will create
> a real `fine_dynamic_masks/` directory with `all/`, `human/`, and `vehicle/`
> sub-directories — in that case the `fine_dynamic_masks` symlink is not needed
> (and should be removed first if it exists).

### 6.2 Frame Interpolation

nuScenes keyframes are captured at **2 Hz**. The `--interpolate_N` parameter
controls how many intermediate frames are synthesized between keyframes:

| `interpolate_N` | Effective FPS | Formula |
|---|---|---|
| 0 | 2 Hz | No interpolation |
| 4 | 10 Hz | (4 + 1) × 2 = 10 |
| 5 | 12 Hz | (5 + 1) × 2 = 12 (may drop frames) |

**Recommendation:** Use `interpolate_N = 4` (10 Hz). Higher values risk frame
drops because the camera hardware captures at ~12 Hz but occasionally misses
frames.

### 6.3 Camera Identifiers

nuScenes uses 6 cameras. After preprocessing, camera IDs map as follows:

| ID | nuScenes Channel |
|---|---|
| 0 | CAM_FRONT |
| 1 | CAM_FRONT_LEFT |
| 2 | CAM_FRONT_RIGHT |
| 3 | CAM_BACK_LEFT |
| 4 | CAM_BACK_RIGHT |
| 5 | CAM_BACK |

When using `views=3`, cameras 0, 1, 2 (front, front-left, front-right) are loaded.

---

## 7. Mask Extraction Details

The mask extraction script (`datasets/tools/extract_masks.py`) uses
**SegFormer B5** trained on Cityscapes (83.2% mIoU).

**Algorithm for fine dynamic masks:**

1. Run SegFormer inference on each image → per-pixel class predictions.
2. Extract sky mask: pixels with Cityscapes class ID 10 → binary mask.
3. For fine dynamic masks (if `--process_dynamic_mask`):
   - Load rough bounding-box masks from `dynamic_masks/human/` and
     `dynamic_masks/vehicle/`.
   - Compute SegFormer person/cyclist prediction mask.
   - Intersect with rough human bbox mask → refined human mask.
   - Compute SegFormer vehicle prediction mask.
   - Intersect with rough vehicle bbox mask → refined vehicle mask.
   - Union human + vehicle → `fine_dynamic_masks/all/`.
4. Generate multi-class `custom_masks/` with semantic class values.

**Scene selection options:**

- `--scene_ids 0 1 2` — specific scenes
- `--split_file data/waymo_example_scenes.txt` — from file
- `--start_idx 0 --num_scenes 200` — index range
- *(omit all three)* — defaults to `start_idx=0, num_scenes=200`

---

## 8. Troubleshooting & FAQ

### Q: The loader can't find `ego_pose/` for nuScenes data

**A:** nuScenes preprocessing creates `lidar_pose/` instead. Create a symlink:
```bash
ln -sfn lidar_pose ego_pose
```

### Q: `fine_dynamic_masks/all/` is empty or missing

**A:** You need to run the mask extraction step (§3.5) with
`--process_dynamic_mask`. Without this flag, only sky masks are generated.
For nuScenes without SegFormer, symlink `fine_dynamic_masks` → `dynamic_masks`.

### Q: `interpolate_N` causes an error with Waymo/Argoverse

**A:** The `--interpolate_N` flag is validated to only work with `--dataset nuscenes`.
The parser will raise an error for other datasets.

### Q: Images have different sizes across cameras

**A:** The loader resizes all images to 518px width with proportional height
(rounded to multiples of 14). If heights still differ, images are padded to
the max dimensions with white (value 1.0) padding.

### Q: What's the difference between `preprocess.py` and `preprocess_waymo.py`?

**A:** Both preprocess Waymo data but use different processor classes:
- `preprocess.py` → `datasets.waymo.waymo_preprocess.WaymoProcessor`
- `preprocess_waymo.py` → `datasets.waymo.waymo_preprocess_.WaymoProcessor`

The `preprocess_waymo.py` variant additionally requires `--json_folder_to_save`
and `--scene_list_file`, and supports auto-discovery of `.tfrecord` files.

### Q: Variable naming confusion — `extrinsic_paths` vs `ego_paths`

**A:** In `dataset.py`, the naming is swapped:
- `self.extrinsic_paths` → reads from `ego_pose/` (world poses)
- `self.ego_paths` → reads from `extrinsics/0.txt` (camera-to-ego transform)

This is a known quirk in the codebase.

---

## 9. Quick-Start Cheat Sheet

### Minimal Waymo Setup

```bash
# 1. Download
conda activate dggt_data
python datasets/waymo/waymo_download.py \
    --target_dir ./data/waymo/raw/validation \
    --split_file data/waymo_val_list.txt --scene_ids 8

# 2. Preprocess
python datasets/preprocess_waymo.py \
    --data_root data/waymo/raw/ \
    --target_dir data/waymo/processed \
    --dataset waymo --split validation \
    --scene_list_file data/waymo_val_list.txt --scene_ids 8 \
    --num_workers 4 \
    --process_keys images lidar calib pose dynamic_masks ground \
    --json_folder_to_save data/annotations/waymo

# 3. Extract masks
conda activate segformer
python datasets/tools/extract_masks.py \
    --data_root data/waymo/processed/validation \
    --segformer_path=/path/to/SegFormer \
    --checkpoint=/path/to/SegFormer/pretrained/segformer.b5.1024x1024.city.160k.pth \
    --scene_ids 8 --process_dynamic_mask
```

### Minimal nuScenes Setup

```bash
# 1. Symlink raw data
mkdir -p ./data/nuscenes
ln -s /path/to/nuscenes ./data/nuscenes/raw

# 2. Preprocess
conda activate dggt_data
pip install nuscenes-devkit
python datasets/preprocess.py \
    --data_root data/nuscenes/raw \
    --target_dir data/nuscenes/processed \
    --dataset nuscenes --split v1.0-mini \
    --start_idx 0 --num_scenes 10 --interpolate_N 4 \
    --workers 32 \
    --process_keys images lidar calib dynamic_masks objects

# 3. Extract masks
conda activate segformer
python datasets/tools/extract_masks.py \
    --data_root data/nuscenes/processed_10Hz/mini \
    --segformer_path=/path/to/SegFormer \
    --checkpoint=/path/to/SegFormer/pretrained/segformer.b5.1024x1024.city.160k.pth \
    --start_idx 0 --num_scenes 10 --process_dynamic_mask

# 4. Create required symlinks for the loader
cd data/nuscenes/processed_10Hz/mini
for d in */; do
    ln -sfn lidar_pose "${d}ego_pose"
    # Only if you skipped SegFormer mask extraction:
    # ln -sfn dynamic_masks "${d}fine_dynamic_masks"
done
```

### Minimal Argoverse 2 Setup

```bash
# 1. Download
pip install av2
s5cmd --no-sign-request cp "s3://argoverse/datasets/av2/sensor/*" data/argoverse/raw

# 2. Preprocess
conda activate dggt_data
python -m datasets.preprocess \
    --data_root data/argoverse/raw \
    --target_dir data/argoverse/processed/training \
    --dataset argoverse --start_idx 0 --num_scenes 10 \
    --workers 32 \
    --process_keys images lidar calib pose dynamic_masks objects

# 3. Extract masks
conda activate segformer
python datasets/tools/extract_masks.py \
    --data_root data/argoverse/processed/training \
    --segformer_path=/path/to/SegFormer \
    --checkpoint=/path/to/SegFormer/pretrained/segformer.b5.1024x1024.city.160k.pth \
    --start_idx 0 --num_scenes 10 --process_dynamic_mask
```

---

*End of document.*
