---
title: image-blaster
source: https://github.com/neilsonnn/image-blaster
project_page:
Imported At: 2026-05-15
category: 3d-generation
tags: [3d-reconstruction, gaussian-splatting, image-to-3d, sound-effects, claude-code, agentic-workflow]
related_papers: []
---

# image-blaster

**One-line summary:** An agentic pipeline that converts a single image into a fully meshed 3D environment with sound effects in under 5 minutes, orchestrated by Claude Code skills.

---

## Overview

image-blaster is a Claude Code–powered toolkit that takes a single input image and produces:

1. **3D meshes** (`.glb`, `.obj`) of all dynamic objects in the scene
2. **Gaussian splat** (`.spz`) of the static environment
3. **Ambient & physics SFX** (`.mp3`) — looping soundscapes and object-specific sound effects

The entire pipeline is orchestrated through Claude Code "skills" (structured prompt modules) that call external generation APIs in sequence. The user interacts conversationally — dropping an image into `input/` and asking Claude to "blast it."

## Architecture

### Claude Skills Pipeline

The project defines 7 Claude skills under `.claude/skills/`:

| Skill | Purpose |
|-------|---------|
| `image-blast-project` | Orchestrates the full pipeline |
| `image-blast-world` | Generates explorable 3D world via World Labs |
| `image-blast-3d` | Creates 3D object meshes via Hunyuan-3D (FAL) |
| `image-blast-sfx` | Generates sound effects via ElevenLabs |
| `image-blast-plate` | Produces clean background plates |
| `image-blast-image-edit` | Handles image editing (cleanup, object refs) |
| `image-blast-uncover` | Reveals/extracts hidden scene elements |
| `image-blast-wildcard` | Handles freeform creative requests |

### Generation Models Used

- **marble-1.1** (World Labs) — Creates explorable 3D environments from images
- **nano-banana** — Default image edit model for source cleanup, clean plates, and object reference images
- **gpt-image-2** — Alternate image edit provider
- **hunyuan-3d** (FAL) — Generates 3D object meshes with configurable parameters
- **elevenlabs-sfx** — Produces ambient and object-specific sound effects

### Tech Stack

- **Runtime:** Bun (TypeScript/JavaScript)
- **Viewer:** React + Vite + Tailwind CSS (in `app/`)
- **Build tooling:** Bun workspaces, PostCSS
- **3D formats:** GLB, OBJ, SPZ (Gaussian splat)

## Key Features

- **End-to-end in < 5 minutes** — from a single photo to textured 3D assets + audio
- **Conversational interface** — operates through Claude Code; user confirms each step
- **Configurable 3D output** — face count (40k–1.5M), PBR materials, polygon type, LowPoly/Geometry modes
- **Extensible** — outputs can embed into Unity, Unreal, Godot, Blender, Three.js, etc.
- **Viewer included** — built-in React app to preview generated worlds

## Usage

```bash
git clone https://github.com/neilsonnn/image-blaster
cd image-blaster
claude  # requires Claude Code CLI
# Provide World Labs and FAL API keys
# Drop image into input/, ask Claude to "blast it"
```

## Hunyuan-3D Parameters

| Parameter | Values | Default |
|-----------|--------|---------|
| `--face-count` | 40,000 – 1,500,000 | 50,000 |
| `--enable-pbr` | true / false | true |
| `--generate-type` | Normal / LowPoly / Geometry | Normal |
| `--polygon-type` | triangle / quadrilateral | triangle |

---

## 中文概要

# image-blaster

**一句话概括：** 一个由 Claude Code 编排的智能管线，能在 5 分钟内将单张图片转化为带声效的完整 3D 环境。

## 概述

image-blaster 利用 Claude Code 的 "技能"（Skills）模块，串联 World Labs（3D 环境生成）、FAL/Hunyuan-3D（物体网格生成）和 ElevenLabs（音效生成）等外部 API，实现从一张输入图片到完整 3D 场景资产的全自动转换。

### 产出物

1. 场景中动态物体的 3D 网格（`.glb`、`.obj`）
2. 静态环境的高斯泼溅（`.spz`）
3. 环境音效及物体专属物理音效（`.mp3`）

### 技术栈

- 运行时：Bun（TypeScript）
- 查看器：React + Vite + Tailwind CSS
- 3D 格式：GLB、OBJ、SPZ

### 特点

- 端到端 < 5 分钟
- 对话式交互，用户可逐步确认
- 可配置的 3D 输出参数（面数、PBR 材质、多边形类型等）
- 输出可直接嵌入 Unity、Unreal、Godot、Blender、Three.js 等
