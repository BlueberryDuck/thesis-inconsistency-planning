---
name: thesis-review
description: >
  Review LaTeX thesis sections for style, correctness, and consistency.
  Use when asked to review, proofread, or check thesis sections, or when
  user says "review section", "check my thesis", "proofread", "style check".
---

Reviewing Master's thesis: "Inconsistency Measurement for Classical Planning Problems."

## Setup

1. Read `/home/blueberryduck/thesis-inconsistency-planning/CLAUDE.local.md` — authoritative for conventions
2. Read section(s) in $ARGUMENTS (default: all .tex files in `masterthesis_sections/`)
3. No section given → ask which

## Style Rules (enforce strictly)

- No first-person pronouns except "we" in math proofs
- No em dashes (—) anywhere
- CAPS for computational functions (e.g., COMPUTE, SOLVE)
- Lowercase for accessors (e.g., atoms, operators)
- Title Case for structures (e.g., Planning Task, Measure Profile)
- Minimal emphasis (bold/italic only when needed)
- No needless colons, semicolons, or parenthetical clusters
- Captions BELOW floats (tables, figures, listings)
- Short captions preferred (no `\caption[short]{long}` pattern)
- `\label` AFTER `\caption`, never before
- First-mention citations include authors: "Gebser et al.~\cite{...}"
- Later citations: bare `\cite{}`

## Checks to perform

1. **Style compliance**: run an explicit per-rule scan, do not eyeball. Grep the section for each, then judge hits in context (math/citation hits are usually fine; prose hits are the target):
   - Em dashes: `grep -n '—\|---' <file>`
   - First-person: `grep -niE '\b(I|we|our|us|my)\b' <file>` (allow "we" only in math proofs)
   - Semicolons in prose: `grep -n ';' <file>` (exclude `\begin/\end/\label`)
   - Needless colons: `grep -n ':' <file>` (allow only list intros, definition headers)
   - Prose parentheticals and clusters: `grep -noE '\([^)]*\)' <file>` plus `grep -noE '\((e\.g\.|i\.e\.)[^)]*\)' <file>` (math notation OK; flag asides, acronyms never reused, stacked clusters)
   - Citations: confirm first mention names authors, later mentions bare
   - Captions/labels: `\label` after `\caption`, captions below floats, no `\caption[short]{long}`
   - CAPS/lowercase/Title Case per the function/accessor/structure convention
   A "clean" verdict requires every item above to have been run, not assumed.
2. **Cross-references**: verify `\ref{}` and `\label{}` resolve, no orphan labels
3. **Citation keys**: verify all `\cite{}` keys exist in `references.bib`
4. **Content consistency**: numbers, claims, terminology match across sections
5. **LaTeX hygiene**: orphan files, missing `\input{}`, duplicate labels
6. **Academic tone**: flag informal language, hedging, vague claims

## Output format

Group findings by severity:

- **Must fix**: errors, broken refs, style violations
- **Should fix**: inconsistencies, unclear passages
- **Consider**: improvement suggestions

Each finding: file, line (approx), problem, fix.

Never invent issues. Clean section → say so.
