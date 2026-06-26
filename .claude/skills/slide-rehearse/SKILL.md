---
name: slide-rehearse
description: >
  Estimate per-frame speaking time for AIG_beamer/aig_beamer.tex, flag overruns
  against BUDGET comments and the 30-minute defense target, and suggest which
  frames to compress, split, or move to backup. Read-only — does not edit
  slides. Use when user says "rehearse the talk", "time the slides", "check
  the talk length", or invokes "/slide-rehearse".
---

Rehearsing the thesis defense talk.

## Setup

1. Read `/home/blueberryduck/thesis-inconsistency-planning/AIG_beamer/aig_beamer.tex`.
2. Read `/home/blueberryduck/.claude/plans/the-current-aig-beamer-tex-is-ticklish-naur.md` — total budget (28 min main + 2 min buffer = 30 min) and Phase B outline.
3. Words-per-minute default 130. Override via `$ARGUMENTS` (e.g., `wpm=110`).

## Per-frame analysis

For each `\begin{frame}...\end{frame}` block:

1. Extract title from `\begin{frame}{...}` or `\frametitle{...}`.
2. Count prose words. Exclude LaTeX commands, math (`$...$`, `\[...\]`, `\begin{align*}...\end{align*}`), comments, and `lstlisting` bodies. Bullets count as prose.
3. Estimate `time_s = words / WPM * 60`. Round to nearest second.
4. Parse `% BUDGET: ~Ns` if present on a line immediately preceding the frame. If absent, mark `BUDGET: —` and assume 90 s only when explicitly asked.
5. Flag overrun if estimate > BUDGET + 15 s. Flag underrun if estimate < BUDGET - 20 s.
6. Frames after `\appendix` or under a section labelled with `Backup` in the title count toward the backup total, not the main total.

## Output

A markdown timetable:

| # | Title | Words | Est. (s) | Budget (s) | Status |

Plus:

- **Main total**: `Σ est.` minutes vs 28-min target. Report delta.
- **Backup total**: minutes only (informational).
- **Overrun list**: frames > budget + 15 s. Per frame, suggest exactly one of: compress, split, move to backup, drop.
- **Underrun list**: frames < budget - 20 s. Suggest adding the canonical scenario value or merging with neighbouring frame.
- **Missing budgets**: frames lacking a `% BUDGET` comment.

## Hard refusals

- Do not edit slides. Suggestions only. If the user wants the edit applied, invoke `defense-slide-build` separately.
- Do not assume a default BUDGET unless explicitly told to.
- Do not estimate by counting LaTeX source bytes; only prose words.
