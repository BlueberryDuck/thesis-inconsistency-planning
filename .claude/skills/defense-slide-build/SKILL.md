---
name: defense-slide-build
description: >
  Draft beamer frames for the thesis defense talk in AIG_beamer/aig_beamer.tex
  by reading thesis sections under AIG_paper/. Enforces the example-first slide
  rules from the defense slide build plan. Use when user says "build slide
  for §X", "draft a slide on Y", "add a defense frame for <topic>", or
  invokes "/defense-slide-build".
---

Building defense slides for: "Inconsistency Measurement for Classical Planning Problems."

## Setup

1. Read `/home/blueberryduck/thesis-inconsistency-planning/CLAUDE.local.md` — paths and frozen-source rule.
2. Read `/home/blueberryduck/thesis-inconsistency-planning/UBIQUITOUS_LANGUAGE.md` — canonical terminology.
3. Read `/home/blueberryduck/.claude/plans/the-current-aig-beamer-tex-is-ticklish-naur.md` — outline (Phase B table), canonical scenarios, time budget.
4. Identify target thesis section from `$ARGUMENTS`. If unclear, ask.
5. Read the referenced section under `AIG_paper/masterthesis_sections/`.

## Edit target

`/home/blueberryduck/thesis-inconsistency-planning/AIG_beamer/aig_beamer.tex` only. Never edit anything under `AIG_paper/`. Read-only sources include all `.tex`, `references.bib`, and `figures/` under `AIG_paper/`.

## Slide construction rules

Every drafted frame must satisfy:

1. **Title as take-home**, not topic. "Naive encoding flattens conflicts", not "Naive encoding".
2. **Value before definition.** Open with a scenario value, e.g. `Light Switch: $\mathcal{I}^{\text{scope}}_{\text{MX}} = 2$, $\mathcal{I}^{\text{struct}}_{\text{MX}} = 1$`, then the definition.
3. **One displayed equation max** per frame; prose carries the rest.
4. **At most seven bulleted lines.** Split into two frames otherwise.
5. **Footer cite** to thesis section: `\hfill {\scriptsize \textcolor{gray}{§4.2}}` for Q&A navigation.
6. **Budget comment** above each frame: `% BUDGET: ~90s`.
7. **Canonical scenarios only**: Locked Door, Bank Vault, Light Switch, Traffic Light, Trust and Travel, Rival Alliances, 3unsat. Refuse new examples unless they cover a structural pattern the seven cannot.
8. **Ubiquitous Language**: $\mathcal{I}$ for measures, never bare $I$. STRIPS tuple order $P, O, I, G$. Trace state vars use σ, not s. Operator/Action: use Operator in formal text; Action reserved for PDDL surface.
9. **No first-person pronouns**, no em dashes (—), no `\caption[short]{long}`. Captions below floats. `\label` after `\caption`.

## Workflow

1. Locate insertion point in `aig_beamer.tex` (which section block, which frame number per Phase B).
2. Draft the frame using the rules above.
3. Compile and check: `pdflatex -interaction=nonstopmode -output-directory=AIG_beamer AIG_beamer/aig_beamer.tex` (run twice for refs). Resolve any errors and any new overfull hbox >5 pt before reporting done.
4. Report: section, frame title, BUDGET seconds, scenario used, and the line range of the new frame.

## Hard refusals

- Do not edit `AIG_paper/`. Flag instead.
- Do not invent scenarios beyond the canonical seven.
- Do not exceed seven bullets or one displayed equation per frame.
- Do not use em dashes, first person, or hedging in slide prose.
