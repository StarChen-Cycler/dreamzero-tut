# User Spec: Dream Zero Guide

## Executive Summary

Create a Markdown-first lecture and reference system that deconstructs the DreamZero project from `logic root` to `root cause`, using LaTeX formulas where useful, and making the material understandable to readers who know general math but do not know robotics.

## Routed Intake

- Input shape: Problem
- Inquiry logic: How
- Discrepancy type: Ideal-Seeking
- Structure class: Semi-Structured
- Cognitive depth: Higher-Order
- Deconstruction pressure: High
- Clarification stakes: High
- Recommended clarification level: 4

## Audience

Primary audience:

- mathematically literate readers
- engineers or students with little or no robotics background
- readers who need a standard reference, not only a one-off explanation

Secondary audience:

- future contributors who will extend the guide into a fuller report or site

## Goals

1. Explain what DreamZero is trying to solve before explaining how it is built.
2. Separate symptoms from root causes in prior VLA limitations.
3. Reconstruct DreamZero's architecture from first principles.
4. Use formulas without assuming robotics fluency.
5. Build a reusable document set that can later grow into a polished public reference.

## Core Deliverables

- a lecture sequence in Markdown
- a standard reference report in Markdown
- glossary and formula sheet
- explicit source map tying interpretations back to the paper
- authoring rules that preserve accessibility and rigor

## Pedagogical Method

Use Michael Polanyi's tacit knowledge concept as a standing lecture rule.

Each chapter should include:

1. what the paper explicitly states
2. the causal mechanism behind it
3. the tacit cue an expert notices
4. the transfer question that tests whether the idea generalizes beyond the paper

## Constraints

- source content must remain Markdown-first
- formulas must be written in LaTeX-compatible syntax
- explanations must not require robotics pre-knowledge
- claims must stay traceable to source material
- simplifications must be marked as pedagogical rather than source-literal

## Success Criteria

The project is successful when:

1. a new reader can explain why DreamZero differs from a standard VLA
2. a new reader can follow the main equations at a conceptual level
3. the report clearly distinguishes root cause from design response
4. a future contributor can extend the guide without re-deriving the project logic from scratch

## Failure Criteria

- the guide becomes a shallow summary with no causal structure
- formulas appear without explanation
- robotics jargon is used before it is grounded
- tacit expert judgment stays hidden
- claims become detached from the paper source

## Edge Cases

- OCR errors in extracted Markdown
- ambiguous equations or notation in Doc2x output
- temptation to over-explain robotics history and lose focus
- drift from DreamZero-specific explanation into generic world-model evangelism
