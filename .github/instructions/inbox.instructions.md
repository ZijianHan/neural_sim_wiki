---
applyTo: "sources/inbox/**"
---

# Inbox Folder Instruction

`sources/inbox/` is a **holding area for unprocessed source material**.

## Rules

1. **Do NOT compile inbox contents into the wiki.** When processing "update wiki" or "Process New Sources" commands, skip everything under `sources/inbox/`.
2. **No structure required.** Files here can be in any format — raw notes, URLs, PDFs, screenshots, partial drafts, clipboard dumps.
3. **Manual promotion only.** Items leave the inbox only when the user explicitly requests processing (e.g., "process inbox item X" or moves the file to `sources/papers/` or `sources/projects/`).
4. **Inbox is not referenced by wiki pages.** Do not add `sources/inbox/` paths to any wiki page's `sources:` metadata.

## arXiv Report Processing

When the user asks to process an arxiv report from the inbox:

1. **Triage first.** Assess each paper's relevance against the wiki's existing topics (see `wiki/Index.md`).
2. **Only process highly relevant papers** — those that directly extend, compete with, or complement methods already tracked in the wiki (e.g., VGGT lineage, 3DGS variants, driving world models, sensor simulation, AD scene generation).
3. **Skip tangential papers** — e.g., clinical AI, military planning, general RL theory, anomaly detection, or other domains outside the wiki's scope — even if they appeared in the report due to broad keyword matching.
4. **Present a ranked shortlist** to the user before ingesting, grouping papers into High / Medium / Low relevance tiers so the user can confirm or adjust.

## What "Process Inbox" Means

When the user says "process inbox" or "process inbox item X", the agent must:

1. **Analyze** the raw file(s) in `sources/inbox/`.
2. **Extract** the relevant information (e.g., key findings, methods, contributions).
3. **Create structured summaries** in the appropriate `sources/` subfolder (e.g., `sources/papers/` for arxiv papers, `sources/projects/` for project notes) following the format conventions defined in their respective instruction files.
4. **Update the wiki** — compile the newly created source summaries into `wiki/` pages per the standard compilation logic.
5. **Delete the processed inbox file** from `sources/inbox/` to keep the holding area clean.

The inbox file is raw input; the output is a properly formatted source summary living in the correct `sources/` subfolder.

## Typical workflow

1. User drops raw material into `sources/inbox/`.
2. User reviews and decides what to do with it.
3. User asks the agent to process specific items → agent analyzes, extracts, and summarizes into `sources/` subfolders, updates the wiki, then deletes the inbox file.
