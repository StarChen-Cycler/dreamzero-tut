# DreamZero Guide

This project turns the DreamZero paper into a teaching path and a standard reference.

The design follows two constraints:

1. `system-development-principles-canonical.md`
2. `question-problem-routing-system-v3.md`

That means the guide does not begin with a full summary. It begins by making the problem explicit, containing scope, and identifying the smallest clarification loop that matters.

## Who This Is For

This guide assumes:

- comfort with algebra and basic probability
- no prior robotics background
- no prior knowledge of diffusion models or robot control

## What This Guide Tries To Explain

The central question is:

> Why would predicting video together with action help a robot generalize better than a standard Vision-Language-Action pipeline?

The guide answers that question in two passes:

- `lecture/`: a teachable path with explicit bridges for readers who are new to robotics
- `report/`: a compact standard reference for reuse

It now also includes a direct comparison chapter against LingBot-VA so readers can see what changes when a project is framed as:

- a world-action model
- a causal video-action world model
- or a latent video-action code release

## Design Promise

Every major chapter should answer four things:

1. What problem pressure exists?
2. What is the root cause behind that pressure?
3. What design move does DreamZero make?
4. What tacit judgment does a practitioner rely on that the paper does not fully spell out?

The comparison chapter adds a fifth question:

5. Which differences come from the model idea itself, and which differences come from the released codebase or evaluation regime?

## Source Boundary

The raw extracted paper lives in `sources/doc2x/`.
Interpretation happens in this `docs/` tree.
