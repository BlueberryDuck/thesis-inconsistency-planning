---
name: audit-bib-entry
description: >
  Audit a single BibTeX entry in references.bib for completeness, correct entry
  type, and acronym protection. Verifies fields against DBLP, DOI resolver, or
  publisher pages. Use when user says "audit bib entry", "check this citation",
  "verify reference", "fix bibtex", or pastes a citekey.
argument-hint: "<citekey>"
model: sonnet
---

Auditing one BibTeX entry in `/home/blueberryduck/thesis-inconsistency-planning/AIG_paper/references.bib`.

## No-hallucination rule (overrides everything below)

**Every field value in the proposed corrected entry must trace to a primary source: the existing entry, the DOI resolver, DBLP, the publisher landing page, or a sibling LaTeX file in the thesis.** If a field cannot be sourced, omit it. Do not infer page ranges, editors, series volumes, conference ordinals, addresses, months, or publishers. In the report, every changed/added field gets a `Source:` line citing the URL or local file path.

## Phase 1: Locate

- No citekey in `$ARGUMENTS` → ask which entry
- Open `references.bib`, find `@<type>{<citekey>,` block; record current type and all fields
- Citekey not found → list near matches and stop

## Phase 2: Static checks

### Required / optional fields per entry type

Source: Patashnik, _BibTeXing_ (`btxdoc.pdf`, §3). Required = entry is malformed without the field. Optional = printed if present.

| Type                             | Required                                                               | Optional                                                                                   |
| -------------------------------- | ---------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| `@article`                       | author, title, journal, year                                           | volume, number, pages, month, note                                                         |
| `@book`                          | author **or** editor, title, publisher, year                           | volume **or** number, series, address, edition, month, note                                |
| `@booklet`                       | title                                                                  | author, howpublished, address, month, year, note                                           |
| `@inbook`                        | author **or** editor, title, chapter **and/or** pages, publisher, year | volume **or** number, series, type, address, edition, month, note                          |
| `@incollection`                  | author, title, booktitle, publisher, year                              | editor, volume **or** number, series, type, chapter, pages, address, edition, month, note  |
| `@inproceedings` / `@conference` | author, title, booktitle, year                                         | editor, volume **or** number, series, pages, address, month, organization, publisher, note |
| `@manual`                        | title                                                                  | author, organization, address, edition, month, year, note                                  |
| `@mastersthesis` / `@phdthesis`  | author, title, school, year                                            | type, address, month, note                                                                 |
| `@misc`                          | (none)                                                                 | author, title, howpublished, month, year, note                                             |
| `@proceedings`                   | title, year                                                            | editor, volume **or** number, series, address, month, organization, publisher, note        |
| `@techreport`                    | author, title, institution, year                                       | type, number, address, month, note                                                         |
| `@unpublished`                   | author, title, note                                                    | month, year                                                                                |

Note: bibstyle is `plain`; fields outside each row's optional set don't render. Flag empty-looking `@misc` entries.

### Entry-type heuristics (flag, do not silently rewrite)

These are pattern-matches over the existing entry, not inferences:

- `@article` with `journal` containing "Technical Report" → propose `@techreport`, ask for `institution`
- `@article` with `journal` starting "Proceedings of" or named after a venue (KR, IJCAI, ECAI, AAAI, NeurIPS, ICAPS) → propose `@inproceedings`
- `@article` with `journal` matching the title of an edited book → propose `@incollection`

Report the triggering substring and the external source confirming the corrected type.

### Acronym protection in `title`

Plain bibstyle lowercases title words after the first word and after `:` unless the token is brace-protected. Source: `plain.bst`, `format.title` function.

Protection rule, applied mechanically:

1. Tokenize the `title` field on whitespace and punctuation
2. For every token that contains ≥2 consecutive uppercase ASCII letters and is not already inside `{...}`, flag it
3. Cross-check against `\acrodef{X}{...}` definitions in `AIG_masterthesis.tex` — confirmed acronyms get a strong "protect with `{X}`" recommendation; unrecognized ALL-CAPS tokens get a softer "is this an acronym?" prompt

Also flag (mechanical patterns only):

- Trailing `.` inside the `title` braces
- DOI casing: any letters between `10.xxxx/` and the next `/` that are uppercase when the canonical record (resolved DOI) uses lowercase

### Booktitle redundancy in `@inproceedings` / `@incollection`

`plain.bst` renders `address` and `month year` separately, so DBLP's self-contained booktitle double-prints them. Strip tokens from `booktitle` that duplicate sibling fields in the same entry:

- City/country → covered by `address`
- Dates like `September 11-16, 2022` → covered by `month` + `year`

Keep venue identity: full name, ordinal, acronym (brace-protected), trailing `Proceedings`.

## Phase 3: External verification

Priority order — stop at first authoritative hit. Record every URL consulted.

1. **DOI resolver** (if `doi` present): WebFetch `https://doi.org/<doi>`, follow redirects.
2. **DBLP**: WebSearch `dblp <first author surname> <title keywords>`, then WebFetch the record. DBLP is canonical for conference name + ordinal, pages, editors, series + volume, year, month.
3. **Publisher landing page** (Springer Link, ScienceDirect, ACM DL, AAAI OJS, DROPS, JAIR, Zenodo).
4. **arXiv / author page** only for fields the publisher record omits.

Forbidden sources: Google Scholar, ResearchGate, Wikipedia, blog posts. They paraphrase metadata and have been wrong on this thesis's bib before.

For conference papers, confirm against the canonical record: ordinal, editors, series + volume, address, month, pages. Drop any field the source does not confirm.

## Phase 4: Output

Present the report, then immediately apply the corrected entry. Do not ask for confirmation — the user can revert via git if needed.

### Report

Sections, each omitted if empty:

- **Entry**: `<citekey>` (`@<type>`)
- **Missing required fields** (Patashnik): list
- **Missing optional but informative**: field + source URL
- **Type mismatch**: current → proposed, triggering substring, confirming URL (or "flag only" if unconfirmed)
- **Acronym protection**: per-token, with `\acrodef` line number or "please confirm"
- **Discrepancies vs external source**: `<field>: entry says X, <source> says Y → <url>`
- **Proposed corrected entry**: full block; every changed/added field gets a `% Source: <url or "existing entry">` comment
- **Sources consulted**: list of URLs

### Apply

After presenting the report, write the corrected entry to `references.bib` via Edit in the same turn.

- Preserve the indentation and alignment style of surrounding entries (`  field    = {value},`)
- Do not touch other entries
- Do not add fields without a `Source:` line in the report
- **Exception — stop and ask first**: if the audit suggests the citekey points to a *different work* than the bib entry describes (different paper, different authors, year off by more than one), do not apply — surface the conflict and wait for direction. Typo-level discrepancies (page range off by one, casing, dash style) apply without asking.

## Field order (citation order)

Reorder fields to match `plain.bst` (this thesis's bibstyle) render order. Source: `plain.bst` v0.99d, entry-type `FUNCTION` blocks (lines 538–837), with composite formatters `format.vol.num.pages`, `format.date`, `format.bvolume`, `format.in.ed.booktitle`, `format.chapter.pages`, `format.tr.number` decomposed to atomic fields.

Templates (in render order, address-present / editor-present branch where the function branches). Omit any field the entry does not have:

- `@article`: author, title, journal, volume, number, pages, month, year, note
- `@book`: author OR editor, title, volume, series, number, publisher, address, edition, month, year, note
- `@booklet`: author, title, howpublished, address, month, year, note
- `@inbook`: author OR editor, title, volume, series, chapter, pages, number, publisher, address, edition, month, year, note
- `@incollection`: author, title, editor, booktitle, volume, series, number, chapter, pages, publisher, address, edition, month, year, note
- `@inproceedings` / `@conference`: author, title, editor, booktitle, volume, series, number, pages, address, month, year, organization, publisher, note
- `@manual`: author, title, organization, address, edition, month, year, note
- `@mastersthesis` / `@phdthesis`: author, title, type, school, address, month, year, note
- `@misc`: author, title, howpublished, month, year, note
- `@proceedings`: editor, title, volume, series, number, address, month, year, organization, publisher, note
- `@techreport`: author, title, type, number, institution, address, month, year, note
- `@unpublished`: author, title, note, month, year

Fields not rendered by `plain.bst` (`doi`, `isbn`, `issn`, `url`, `urldate`, `copyright`, `keywords`, `language`, `version`, `subtitle`, `pagetotal`, `ppn_gvk`, etc.) are kept for archival completeness. Place them after the last rendered field in this order: `doi`, `isbn`, `issn`, `url`, `urldate`, `copyright`, `language`, `keywords`, then any other extras preserving their existing relative order.

## Conventions

- Preserve `\textit{}`, math mode, existing `{...}` brace protection
- Keep optional fields the user already supplied (copyright, keywords, language, issn) — do not strip even if unused by plain bibstyle
- Lowercase DOI prefixes when the resolved record does
- UTF-8 author names with diacritics — never escape
- `--` for page ranges; `and` as BibTeX author separator
- If `.tex` usage of the citekey implies a different work than the bib entry describes, flag it — do not silently fix
