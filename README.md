# DreamZero Guide

This project is a Markdown-first lecture and report system for deconstructing the DreamZero paper into a reference that a mathematically literate reader can understand without prior robotics knowledge.

## Purpose

- Preserve the paper's causal logic instead of only summarizing results.
- Move from `logic root` to `root cause`:
- `logic root` = the minimal chain of premises that makes DreamZero a rational design.
- `root cause` = the failure sources in prior approaches that DreamZero is trying to fix.
- Use Michael Polanyi's tacit knowledge idea to teach not only what the paper states, but also what practitioners implicitly rely on when they judge whether the model is plausible.

## What Is Implemented

This repository already contains:

- a complete lecture path in `docs/lecture/`
- a compact standard reference in `docs/report/`
- a glossary, formula sheet, source verification note, and source map in `docs/reference/`
- a local MkDocs website with KaTeX math rendering
- a verified source workflow that distinguishes PDF truth from extracted OCR text

## How To Use It

You can use this project in two ways.

### Option 1: Read It Through The Website

This is the preferred way because it gives you:

- chapter navigation
- rendered LaTeX formulas
- working links between lecture, report, glossary, and source verification pages

In PowerShell:

```powershell
.\.venv\Scripts\python -m mkdocs serve
```

Then open the local URL printed by MkDocs.
Usually it is:

```text
http://127.0.0.1:8000/
```

### Option 2: Read The Markdown Directly

If you do not want to run the local site, open the Markdown files directly:

- lecture: `docs/lecture/`
- standard reference: `docs/report/dreamzero-standard-reference.md`
- reference pages: `docs/reference/`

This works, but formulas and navigation are better in the website.

## Recommended Reading Paths

### If You Are New To DreamZero

Read in this order:

1. `docs/index.md`
2. `docs/lecture/00-course-map.md`
3. `docs/lecture/01-routed-intake.md`
4. `docs/lecture/02-prerequisites.md`
5. `docs/lecture/03-why-vlas-hit-a-wall.md`
6. `docs/lecture/04-logic-root-of-dreamzero.md`
7. `docs/lecture/05-core-equations.md`
8. `docs/lecture/06-system-and-inference.md`
9. `docs/lecture/07-transfer-and-generalization.md`
10. `docs/lecture/08-polanyi-lens.md`

### If You Want The Short Version

Start with:

1. `docs/report/dreamzero-standard-reference.md`
2. `docs/reference/formula-sheet.md`
3. `docs/reference/glossary.md`

### If You Want To Verify Claims Against The Source

Start with:

1. `docs/reference/source-verification.md`
2. `docs/reference/source-map.md`
3. `DreamZero-Nvidia.pdf`
4. `sources/doc2x/DreamZero-Nvidia.md`

## Project Structure

- `sources/doc2x/` contains the extracted paper Markdown and figures from Doc2x.
- `docs/lecture/` contains the teaching sequence.
- `docs/report/` contains the synthesized standard reference.
- `docs/reference/` contains glossary, formulas, and source mapping.
- `.memo/memodocs/` contains the user and technical specs.
- `.claude/rules/` contains authoring and review rules for future work.

## Key Files

- `docs/index.md`: homepage of the local docs site
- `docs/lecture/00-course-map.md`: lecture entrypoint
- `docs/report/dreamzero-standard-reference.md`: stable summary/reference layer
- `docs/reference/glossary.md`: robotics and modeling vocabulary
- `docs/reference/formula-sheet.md`: main formulas in one place
- `docs/reference/source-verification.md`: verified OCR and equation checkpoints
- `docs/reference/source-map.md`: source-of-truth and handoff map

## Local Commands

### Run The Site

```powershell
.\.venv\Scripts\python -m mkdocs serve
```

### Build The Static Site

```powershell
.\.venv\Scripts\python -m mkdocs build
```

### Reinstall Dependencies If Needed

```powershell
python -m venv .venv
.\.venv\Scripts\activate
pip install -r requirements.txt
```

## Source Policy And Editing Rules

- Keep the extracted paper in `sources/doc2x/` unchanged.
- Put interpretation, cleanup, pedagogy, and simplification only in `docs/`.
- When OCR text is suspicious, verify against `DreamZero-Nvidia.pdf`.
- Use `docs/reference/source-verification.md` before changing equations or numeric claims.

## Context Entrypoints

If a future session needs to recover project intent quickly, read these first:

1. `.memo/memodocs/user_spec_dream-zero-guide.md`
2. `.memo/memodocs/tech_spec_dream-zero-guide.md`
3. `CLAUDE.md`
4. `docs/reference/source-verification.md`
5. `docs/reference/source-map.md`

## Who This Is For

This project is designed for readers who:

- are comfortable with general math
- do not already know robotics
- want a standard reference rather than only a paper summary

## Current Status

The lecture and report are already implemented and usable.
They are not just plans.

What has been done:

- source verification workflow
- foundation lecture chapters
- equations and system chapters
- transfer and Polanyi synthesis
- standard reference layer
- final handoff/context recovery instructions

What is still true:

- the PDF remains the final authority for exact wording and equation typography
- the docs are designed to be extendable if you want a more polished public release later
