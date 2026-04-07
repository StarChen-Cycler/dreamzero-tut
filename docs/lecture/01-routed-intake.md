# Routed Intake

This page applies `question-problem-routing-system-v3.md` to the project request itself.

## Routed Intake

- Input shape: Problem
- Inquiry logic: How
- Discrepancy type: Ideal-Seeking
- Structure class: Semi-Structured
- Cognitive depth: Higher-Order
- Deconstruction pressure: High
- Clarification stakes: High
- Recommended clarification level: 4
- Reason for route: the task is not merely to explain a paper; it is to build a reusable teaching and reference system for non-specialists without distorting the paper's logic

## Cleaned Statement

Create a Markdown-first lecture and reference report that deconstructs DreamZero from first principles, explains the root causes of the problem it solves, and remains intelligible to readers with general mathematical literacy but no robotics background.

## Scope Boundaries

Included:

- DreamZero's problem framing
- why prior VLAs are limited
- why joint video-action modeling matters
- the model's core equations
- the real-time control story
- cross-embodiment transfer claims
- a Polanyi-based teaching method for tacit knowledge

Excluded for now:

- full implementation reproduction
- code walkthrough of the public repository
- benchmark-by-benchmark replication
- literature review outside what is needed to explain DreamZero

## Clarification Fields

The guide must keep these fields explicit:

- audience baseline
- source of truth for claims
- which equations are exact from the paper
- which explanations are pedagogical simplifications
- where tacit judgment enters interpretation

## Logic Root And Root Cause

To preserve the user's wording, this project uses:

- `logic root`: the smallest premise chain that leads to DreamZero's design
- `root cause`: the failure mechanism that makes older designs insufficient

## Smallest Loop That Matters

Per the canonical principles, the smallest useful loop is:

$$
\text{paper source} \rightarrow \text{deconstruction notes} \rightarrow \text{teaching chapter} \rightarrow \text{reference synthesis}
$$

This scaffold is the first completion of that loop.
