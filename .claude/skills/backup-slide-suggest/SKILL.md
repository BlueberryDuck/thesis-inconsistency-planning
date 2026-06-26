---
name: backup-slide-suggest
description: >
  Predict defense Q&A and propose backup slide candidates with thesis-section
  citations, cross-referencing the current main outline of
  AIG_beamer/aig_beamer.tex against thesis content not on a main slide.
  Read-only — does not edit slides. Use when user says "predict Q&A",
  "suggest backup slides", "harden the deck", or invokes
  "/backup-slide-suggest".
---

Suggesting backup slides for the thesis defense.

## Setup

1. Read `/home/blueberryduck/thesis-inconsistency-planning/AIG_beamer/aig_beamer.tex` — current outline. Identify main frames vs backup frames (backup = after `\appendix` or under a section labelled with `Backup`).
2. Read `/home/blueberryduck/.claude/plans/the-current-aig-beamer-tex-is-ticklish-naur.md` — backup pack list (Phase B) and the predictable Q&A list (Phase E).
3. Skim `AIG_paper/masterthesis_sections/` for content the main deck does not cover.

## Predictable Q&A categories

Always check whether the deck has backup coverage for:

1. **Methodology choice**: brave vs cautious reasoning; ASP vs SAT/MaxSAT.
2. **Scope limits**: pairwise-only / 3unsat blind spot; $k$-tuple extension.
3. **Parameter sensitivity**: horizon $h$ default; soundness-coverage tradeoff; cave-diving false positive at $h = 10$.
4. **Related work**: dead-state certificates [Eriksson 2018], excuses [Göbelbecker 2010], explanations [Sreedharan 2019].
5. **Complexity**: PSPACE-completeness proof sketch; why uniform.
6. **Postulates**: full table of Planning Consistency / Operator Monotonicity / Goal Monotonicity satisfaction.
7. **Implementation**: ASP brave-reasoning listings (`lst_bridge.tex`, `lst_witnesses.tex`); pipeline phases.
8. **Empirical**: per-domain table (7 domains); grounding bottleneck (57% timeout); scope vs structure granularity on `diagnosis` prob05/prob10/prob16.

## Output

A markdown table, one row per suggested backup slide:

| # | Predicted question | Backup slide title | Supporting section / figure |

After the table, list:

- **Gaps**: predictable questions with no backup yet. Sort by likelihood (advisor-area questions first).
- **Surplus**: existing backup frames with no obvious Q&A trigger; consider dropping.
- **Hyperlink check**: existing backup frames not yet `\hyperlink`-targeted from a main frame. Suggest the main-frame anchor point for each.

## Hard refusals

- Do not edit slides. Suggestions only. Pair with `defense-slide-build` to draft a backup frame.
- Do not invent thesis content. Cite only material present in `AIG_paper/`.
- Do not propose backup slides that duplicate existing main-frame content.
