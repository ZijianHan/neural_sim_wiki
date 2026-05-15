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

## [2026-05-15 08:10] structure | Project folder reorganization
- Restructured `sources/projects/` from flat files to per-project folders with `execution/` subdirs.
- Pattern: `sources/projects/<ProjectName>/<ProjectName>.md` + `sources/projects/<ProjectName>/execution/`.
- Added DGGT execution documentation (architecture analysis, dataset workflow, execution strategy, hardware requirements, inference guide, progress log, QA summary).
- Created `.github/instructions/project-structure.instructions.md` to codify the convention.
- Updated all cross-references in wiki and paper source files.

## [2026-05-14 22:48] ingest+wiki-update | OVGGT
- Added paper summary: `sources/papers/arXiv-2603.05959-OVGGT-O1-Constant-Cost-Streaming-Visual-Geometry-Transformer.md`.
- Added method page: `wiki/Simulation Technologies for Autonomous Driving/Neural Rendering/OVGGT.md`.
- Merged OVGGT into `Neural Rendering.md`, `Method Comparison.md`, `Simulation Technologies for Autonomous Driving.md`, and `Index.md`.
