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

## Typical workflow

1. User drops raw material into `sources/inbox/`.
2. User reviews and decides what to do with it.
3. User asks the agent to process specific items → agent moves/transforms them into the appropriate `sources/` subfolder and updates the wiki.
