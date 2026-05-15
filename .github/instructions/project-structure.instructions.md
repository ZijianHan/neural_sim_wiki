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
7. **Project metadata must include `tags` and `related_papers`.** The `category` field must match the primary paper's category. The `tags` field should share relevant tags with linked papers. The `related_papers` field lists full paths to associated paper source files.

## Required project summary metadata

```yaml
---
title: <Project Name>
source: <GitHub repo URL>
project_page: <optional project website>
Imported At: YYYY-MM-DD
category: <must match primary paper's category>
tags: [<shared tags with linked papers>]
related_papers:
  - "sources/papers/<paper-filename>.md"
---
```

## When adding a new project

1. Create `sources/projects/<ProjectName>/` and `sources/projects/<ProjectName>/execution/`.
2. Create or move the project summary to `sources/projects/<ProjectName>/<ProjectName>.md`.
3. Ensure `category` and `tags` align with the linked paper(s).
4. Add `related_papers` in the project file and `related_project` in the paper file (bidirectional link).
5. Update `wiki/Index.md` and any wiki pages that reference the project.
