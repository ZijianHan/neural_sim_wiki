## [2026-05-14 21:27] ingest | DGGT paper + project
- Added paper summary: `sources/papers/arXiv-2512.03004-DGGT-Feedforward-4D-Reconstruction-of-Dynamic-Driving-Scenes-using-Unposed-Images.md`.
- Added project summary: `sources/projects/DGGT/DGGT.md`.
- Linked paper/project via `related_project` and wiki links.

## [2026-05-14 21:28] wiki-update | Index + topic merge
- Rebuilt `wiki/Index.md` as a full catalog with one-line summaries per page.
- Added wiki topic page: `wiki/Simulation Technologies for Autonomous Driving/Neural Rendering/DGGT.md`.
- Merged DGGT into:
  - `Neural Rendering.md`
  - `Gaussian Splatting.md`
  - `Simulation Technologies for Autonomous Driving.md`
- Updated graph links and source metadata for affected pages.

## [2026-05-14 22:14] wiki-update | DGGT limitations
- Added a dedicated "Limitations" subsection to `wiki/Simulation Technologies for Autonomous Driving/Neural Rendering/DGGT.md`.
- Added Chinese counterpart section "局限性（中文）".
- Kept claims source-grounded to paper/repo-visible evidence boundaries.

## [2026-05-14 22:30] wiki-update | SwiftVGGT + method comparison
- Added new method page: `wiki/Simulation Technologies for Autonomous Driving/Neural Rendering/SwiftVGGT.md`.
- Added new comparison page: `wiki/Simulation Technologies for Autonomous Driving/Neural Rendering/Method Comparison.md`.
- Updated `Index.md`, `Neural Rendering.md`, and `Simulation Technologies for Autonomous Driving.md` to include SwiftVGGT and comparison links.
- Updated source metadata to include `arXiv-2511.18290` where relevant.

## [2026-05-14 22:48] ingest+wiki-update | OVGGT
- Added paper summary: `sources/papers/arXiv-2603.05959-OVGGT-O1-Constant-Cost-Streaming-Visual-Geometry-Transformer.md`.
- Added method page: `wiki/Simulation Technologies for Autonomous Driving/Neural Rendering/OVGGT.md`.
- Merged OVGGT into `Neural Rendering.md`, `Method Comparison.md`, `Simulation Technologies for Autonomous Driving.md`, and `Index.md`.
## [2026-05-15 11:46] qa | DGGT vs PointForward analysis
- Created `wiki/QA/DGGT-vs-PointForward.md` with 5 Q&A entries covering:
  architectural differences, layering artifact analysis, dynamic modeling comparison,
  deployment guidance, and performance comparison.

## [2026-05-15 11:38] ingest+wiki-update | PointForward
- Added paper summary: `sources/papers/arXiv-2605.11594-PointForward-Feedforward-Driving-Reconstruction-through-Point-Aligned-Representations.md`.
- No code repo available yet (paper states "upon publication").
- Created wiki method page: `wiki/Simulation Technologies for Autonomous Driving/Neural Rendering/PointForward.md`.
- Merged PointForward into: `Neural Rendering.md`, `Gaussian Splatting.md`, `Method Comparison.md`, `Index.md`.

## [2026-05-15 08:32] structure | Inbox folder and agent instructions
- Created `sources/inbox/` as a holding area for unprocessed material.
- Added `.github/instructions/inbox.instructions.md` — inbox is excluded from wiki compilation.
- Created top-level `AGENTS.md` summarizing all agent operating rules.

## [2026-05-15 08:29] wiki-update | QA folder and template
- Created `wiki/QA/` folder for per-topic Q&A documents.
- Added `wiki/QA/QA-Template.md` as the standard template.
- Updated `wiki/Index.md` with QA category.

## [2026-05-15 08:10] structure | Project folder reorganization
- Restructured `sources/projects/` from flat files to per-project folders with `execution/` subdirs.
- Pattern: `sources/projects/<ProjectName>/<ProjectName>.md` + `sources/projects/<ProjectName>/execution/`.
- Added DGGT execution documentation (architecture analysis, dataset workflow, execution strategy, hardware requirements, inference guide, progress log, QA summary).
- Created `.github/instructions/project-structure.instructions.md` to codify the convention.
- Updated all cross-references in wiki and paper source files.


