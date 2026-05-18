## [2026-05-18] ingest | Hybrid Reconstruction Pipeline
- Source: `sources/notes/Hybrid 3d reconstruction thoughts.md` (ChatGPT conversation summary).
- Created new wiki page: `wiki/Simulation Technologies for Autonomous Driving/Neural Rendering/Hybrid Reconstruction Pipeline.md`.
- Covers: feed-forward vs per-scene optimization tradeoffs, three-layer hybrid architecture, existing papers (GeSplat, HO-Gaussian, VolSplat, ConFixGS, VGD, ReconDrive), convergence trend.
- Updated Index.md with new "Architecture & Pipeline Design" subsection.

## [2026-05-15 16:39] wiki-update | Created 10 missing topic pages
- Created wiki pages for all recently ingested methods:
  - `VGGT-Omega.md`, `TurboVGGT.md`, `EponaV2.md`, `Real2Sim.md`, `SANA-WM.md`
  - `Coding Agent World Simulator.md`, `BlitzGS.md`, `AV1-3DGS.md`, `3D Skew-Normal Splatting.md`, `PDI-Bench.md`
- All pages include Definition, Synthesis, Chinese version, and Graph Connections.
- Restructured Index.md Neural Rendering section into subgroups: Gaussian Splatting Methods, AD-Specific Reconstruction, VGGT Lineage, World Models.

## [2026-05-15 16:17] ingest | arXiv inbox batch (10 papers)
- Processed `sources/inbox/arxiv_report_2026-05-15_140722.md` (50 papers total, 10 high-relevance extracted).
- Created paper summaries:
  - `arXiv-2605.15195-VGGT-Omega-Scaling-Feed-Forward-Reconstruction.md` (VGGT-Ω)
  - `arXiv-2605.14315-TurboVGGT-Fast-Visual-Geometry-Reconstruction-with-Adaptive-Alternating-Attention.md`
  - `arXiv-2605.14696-EponaV2-Driving-World-Model-with-Comprehensive-Future-Reasoning.md`
  - `arXiv-2605.13591-Real2Sim-Physics-driven-Editable-Gaussian-Splatting-for-AD.md`
  - `arXiv-2605.15178-SANA-WM-Efficient-Minute-Scale-World-Modeling.md`
  - `arXiv-2605.14398-Coding-Agent-Is-Good-As-World-Simulator.md`
  - `arXiv-2605.13794-BlitzGS-City-Scale-Gaussian-Splatting-at-Lightning-Speed.md`
  - `arXiv-2605.14629-AV1-Motion-Vectors-for-Enhanced-Gaussian-Splatting.md`
  - `arXiv-2605.15010-3D-Skew-Normal-Splatting.md`
  - `arXiv-2605.15185-PDI-Bench-Geometric-Consistency-Evaluation-for-Video-World-Models.md`
- Updated wiki pages: `Gaussian Splatting.md` (added routes 5-6), `World Model.md` (expanded to 4-route taxonomy + evaluation).
- Updated `Index.md` with 10 new entries.
- Deleted processed inbox file.

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

## [2026-05-15 15:01] ingest+wiki-update | PanoVGGT
- Added paper summary: `sources/papers/arXiv-2603.17571-PanoVGGT-Feed-Forward-3D-Reconstruction-from-Panoramic-Imagery.md`.
- Added project summary: `sources/projects/PanoVGGT/PanoVGGT.md` (with `execution/` scaffold).
- Added bidirectional links between paper and project metadata (`related_project` / `related_papers`).
- Added method page: `wiki/Simulation Technologies for Autonomous Driving/Neural Rendering/PanoVGGT.md`.
- Merged PanoVGGT into: `Neural Rendering.md`, `Method Comparison.md`, `Simulation Technologies for Autonomous Driving.md`, and `Index.md`.
