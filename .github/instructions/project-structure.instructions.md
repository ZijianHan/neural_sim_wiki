---
applyTo: "sources/projects/**"
---

# Project Folder Structure Instruction

Every project in `sources/projects/` must follow this standardized folder layout:

```
sources/projects/<ProjectName>/
├── <ProjectName>.md        # Project summary (top-level description)
└── execution/              # Implementation & execution documentation
    ├── ...                 # Logs, guides, strategies, progress notes, etc.
```

## Rules

1. **One folder per project.** The folder name matches the project name exactly (case-sensitive).
2. **Project summary at root.** Each project folder contains a `<ProjectName>.md` file — this is the canonical project summary (repo overview, key features, linked papers). Follow the same bilingual format as paper summaries (English first, then Chinese).
3. **`execution/` subfolder for implementation notes.** All hands-on documentation — setup guides, architecture analyses, progress logs, QA summaries, hardware requirements, inference guides, execution strategies, etc. — goes inside `execution/`.
4. **No flat `.md` files at `sources/projects/` level.** All project files must live inside their project folder.
5. **Cross-references.** Paper files should reference projects via `related_project: sources/projects/<ProjectName>/<ProjectName>.md`. Wiki pages should use the same full path in their `sources:` metadata.
6. **Relative links within project files.** When linking to papers from a project summary, use `../../papers/<filename>` (two levels up from the project folder).

## When adding a new project

1. Create `sources/projects/<ProjectName>/` and `sources/projects/<ProjectName>/execution/`.
2. Create or move the project summary to `sources/projects/<ProjectName>/<ProjectName>.md`.
3. Update `wiki/Index.md` and any wiki pages that reference the project.
4. Update `related_project:` fields in associated paper files.
