# Tech Spec: Dream Zero Guide

## Project Type

Documentation-first project with Markdown source, LaTeX math, static-site preview, and memo-backed planning artifacts.

## Stack Decision

Chosen stack:

- source format: Markdown
- formula syntax: KaTeX-compatible LaTeX
- preview and navigation: MkDocs Material
- source archive: Doc2x extracted Markdown plus figures

## Why This Stack

The project needs:

- pure Markdown authoring
- explicit navigation for lecture and reference sections
- search
- reliable math rendering
- low overhead for future contributors

Context7 verification supported the choice of MkDocs plus Material and `pymdownx.arithmatex`, with explicit `nav` configuration and KaTeX-compatible setup.

## Source Layers

### Layer 1: Raw Source

- `DreamZero-Nvidia.pdf`
- `sources/doc2x/DreamZero-Nvidia.md`
- `sources/doc2x/images/`

### Layer 2: Interpretation Layer

- `docs/lecture/`
- `docs/reference/`

### Layer 3: Stable Synthesis

- `docs/report/dreamzero-standard-reference.md`

## Folder Layout

```text
dream-zero-guide/
  .claude/rules/
  .memo/memodocs/
  docs/
    lecture/
    report/
    reference/
    javascripts/
    stylesheets/
  sources/
    doc2x/
  mkdocs.yml
  requirements.txt
  CLAUDE.md
```

## Authoring Rules

1. Do not edit extracted source in place.
2. Keep explicit source claims separate from teaching simplifications.
3. Introduce notation before using it repeatedly.
4. Pair each formula with a plain-language explanation.
5. Pair each architecture claim with a system-level or causal explanation.

## Build Commands

```bash
pip install -r requirements.txt
mkdocs serve
mkdocs build
```

## Validation Loop

The smallest validation loop for this project is:

$$
\text{one source claim} \rightarrow \text{one teaching explanation} \rightarrow \text{one rendered page check}
$$

This follows the canonical rule to validate the smallest thing that matters.

## Review Checklist

- navigation renders correctly
- math renders correctly
- each chapter states its purpose
- each major claim points back to source material
- audience assumptions stay visible
- Polanyi layer is present where interpretation depends on tacit judgment

## Risks

- OCR corruption can leak into the published explanation
- equations may render differently across Markdown engines
- the report may drift into summary instead of disciplined deconstruction
- the site may remain structurally clean but conceptually too advanced for the target audience

## Extension Path

Next logical phases:

1. refine each lecture chapter with line-accurate source references
2. add figure callouts tied to source images
3. add notation consistency checks
4. create Octie task graph for chapter-level buildout
