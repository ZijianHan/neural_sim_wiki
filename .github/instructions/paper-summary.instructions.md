---
applyTo: "sources/papers/*.md"
---

# Paper Summary Instruction

Use this structure for every paper summary in `sources/papers/`:

## Required YAML Frontmatter

Every paper file **must** begin with YAML frontmatter containing these fields:

```yaml
---
title: "Paper Title"
arxiv_id: XXXX.XXXXXvN          # omit if not on arXiv
source: https://arxiv.org/abs/... # canonical URL (arXiv or project page)
pdf: https://arxiv.org/pdf/...    # direct PDF link
project: https://...              # project page (omit if none)
code: https://github.com/...      # code repo (omit if none)
venue: "Conference/Journal Year"  # omit if preprint-only
date: YYYY-MM-DD                  # publication or upload date
Imported At: YYYY-MM-DD           # date added to this wiki
category: Main Topic              # e.g. Gaussian Splatting, World Model, Feed-Forward 3D Reconstruction
tags: [tag1, tag2]                # optional additional tags
related_project: sources/projects/X/X.md  # omit if no local project folder
---
```

**Canonical field names** (do NOT use alternatives):
- `pdf` (not `paper_pdf`)
- `code` (not `repo`)
- `venue` (not `journal`)

After frontmatter, the file starts with `# Paper Title` heading.

## Required English sections
1. `## One-paragraph summary`
2. `## Key ideas`
3. `## Novelty (What is new)` **(mandatory)**
4. `## Why it matters`
5. `## Reported limitations/open challenges (from framing)`
6. `## Source snippets used`

## Required Chinese sections (after English)
1. `## 一段话总结`
2. `## 核心思想`
3. `## 创新点（这篇论文新在哪里）` **(mandatory)**
4. `## 重要性`
5. `## 论文框架中提到的局限/挑战`
6. `## 本页信息来源`

## Writing rules
- English first, then Chinese.
- Keep claims source-grounded (abstract/introduction/official project page only unless more is available).
- Separate confirmed claims from assumptions.
- If missing evidence, explicitly state uncertainty.
- Prefer concise, high-information bullets.
