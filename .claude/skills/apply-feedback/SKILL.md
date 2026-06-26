---
name: apply-feedback
description: >
  Apply advisor feedback to thesis LaTeX files. Use when user pastes feedback
  from thesis advisor (often in German), says "apply feedback", "advisor said",
  "Betreuerin feedback", or "Isabelle said".
argument-hint: "<paste feedback here>"
---

Applying advisor feedback to Master's thesis on "Inconsistency Measurement for Classical Planning Problems."

## Setup

1. Read `/home/blueberryduck/thesis-inconsistency-planning/CLAUDE.local.md` — authoritative for conventions + section status
2. Parse feedback in $ARGUMENTS (German OK — understand, respond English)
3. Identify affected section(s)

## Process

### Phase 1: Understand

- Read feedback carefully
- Read affected .tex sections fully
- Feedback references theorem/prop/section → locate exactly
- Ambiguous wording → ASK, no assume

### Phase 2: Evaluate validity (mandatory, do NOT skip)

Before planning any change, judge each feedback point on its merits. The advisor is not always right; blind application can introduce errors, regressions, or terminology drift.

Per point, state:

- **Claim check** — is the technical/factual claim correct? Verify against UBIQUITOUS_LANGUAGE.md, prior thesis text, primary literature.
- **Fix check** — does the suggested fix actually achieve the advisor's apparent intent? Sometimes the diagnosis is right but the proposed wording isn't.
- **Conflict check** — does it conflict with established thesis conventions, prior advisor decisions, style rules, or earlier feedback already applied?
- **Ripple check** — would applying this force changes elsewhere (other sections, abstract, intro, definitions, cross-refs)?
- **Verdict** — one of: AGREE / AGREE WITH MODIFICATION / DISAGREE / NEEDS USER ADJUDICATION. Give a one-line reason.

Surface disagreements and modifications to the user **before** writing the plan. Do not silently "fix" feedback you think is wrong by applying a different change — flag it and ask.

### Phase 3: Plan

For points cleared in Phase 2 (AGREE / AGREE WITH MODIFICATION):

- Minimal change that fixes properly
- New content, restructure, or reword?
- New bib entries? Search primary lit if yes.
- Present plan BEFORE changes. Wait approval.

### Phase 4: Apply

- Edits respect ALL style rules from CLAUDE.local.md
- No first-person, no em dashes, CAPS/lowercase/TitleCase conventions
- Minimal edits — no rewrite untouched sections
- Preserve existing `\label{}` names unless broken

### Phase 5: Update tracking

- Update `CLAUDE.local.md` with changes (section status, page count if significant)
- Check if `~/Obsidian/300 - Academic/Masters Thesis Plan.md` needs update
- Report changed + unchanged (with reasoning, including Phase 2 verdicts for any DISAGREE points)

## Important

- Respect thesis voice + structure
- No filler prose
- Theoretical content needs lit → search primary sources (Papadimitriou, Arora & Barak, etc.)
- Flag if feedback ripples to OTHER sections
