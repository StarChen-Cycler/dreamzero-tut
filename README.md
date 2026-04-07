# DreamZero Guide

This project is a Markdown-first lecture and report system for deconstructing the DreamZero paper into a reference that a mathematically literate reader can understand without prior robotics knowledge.

## Purpose

- Preserve the paper's causal logic instead of only summarizing results.
- Move from `logic root` to `root cause`:
  - `logic root` = the minimal chain of premises that makes DreamZero a rational design.
  - `root cause` = the failure sources in prior approaches that DreamZero is trying to fix.
- Use Michael Polanyi's tacit knowledge idea to teach not only what the paper states, but also what practitioners implicitly rely on when they judge whether the model is plausible.

## Structure

- `sources/doc2x/` contains the extracted paper Markdown and figures from Doc2x.
- `docs/lecture/` contains the teaching sequence.
- `docs/report/` contains the synthesized standard reference.
- `docs/reference/` contains glossary, formulas, and source mapping.
- `.memo/memodocs/` contains the user and technical specs.
- `.claude/rules/` contains authoring and review rules for future work.

## Build

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
mkdocs serve
```

## Source Policy

- Keep the extracted paper in `sources/doc2x/` unchanged.
- Put interpretation, cleanup, pedagogy, and simplification only in `docs/`.
- When OCR text is suspicious, verify against `DreamZero-Nvidia.pdf`.
