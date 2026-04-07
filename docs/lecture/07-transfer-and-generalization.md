# Transfer And Generalization

This chapter explains what DreamZero claims to generalize and why that matters.

## Three Kinds Of Generalization

The extracted paper emphasizes:

1. new tasks
2. new environments
3. new embodiments

That last one is the strongest claim because a new embodiment means the body itself changes.

## Evidence Framing

The paper reports:

- over $2 \times$ improvement on task and environment generalization relative to strong VLAs
- about $38 \times$ inference speedup after optimization
- over $42\%$ relative improvement from small amounts of cross-embodiment video data

## What The Reader Should Infer Carefully

These numbers do not automatically prove universal superiority.
They support a narrower argument:

$$
\text{joint world-action modeling} + \text{video prior}
\Rightarrow
\text{better transfer under the tested conditions}
$$

## Why Few-Shot Embodiment Adaptation Is Important

If a model can adapt to a new robot with only a small amount of play data, then the representation may be learning structure that is not tied too tightly to one exact body.

That is a major systems-level payoff:

$$
\text{shared world prior} + \text{small embodiment-specific update}
$$

## Claim Discipline

This guide should keep three questions active:

1. What exact benchmark or task family is each claim about?
2. What comparison baseline is being used?
3. Does the paper show causation, or only correlation, between architectural choices and downstream gains?
