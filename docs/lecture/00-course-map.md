# Course Map

This lecture sequence is designed for readers who can follow equations but do not already speak robotics.

!!! note "Source Boundary"

    The lecture sequence is not identical to the paper's section order.
    It is a teaching order built from the verified source material in
    [Source Verification](../reference/source-verification.md).
    When a chapter uses a compact equation or a causal chain for teaching,
    treat that as a pedagogical device unless the chapter says it is literal from the paper.

## Sequence

| Module | Purpose | Main Output |
| --- | --- | --- |
| 1. Routed Intake | Bound the question correctly | Problem frame |
| 2. Primer | Supply the minimum robotics and modeling background | Reader contract |
| 3. Why VLAs Hit a Wall | Identify failure sources in prior approaches | Root-cause map |
| 4. Logic Root of DreamZero | Rebuild the design from first principles | Causal chain |
| 5. Core Equations | Translate the paper's mathematical core into plain language | Notation fluency |
| 6. System and Inference | Explain why the architecture can run online | Engineering intuition |
| 7. Transfer and Generalization | Interpret the evidence and its limits | Claim discipline |
| 8. Polanyi Lens | Surface tacit knowledge and hidden judgment | Teaching method |
| 9. DreamZero vs LingBot-VA | Compare problem framing, architecture, codebase shape, and deployment assumptions | Comparative judgment |
| 10. Industrial Training Guide | Turn the DreamZero paper and code into an executable training sequence | Implementation literacy |

## Reading Modes

- If you want the full teaching path, read this section in order.
- If you want a compact reference, jump to [Standard Reference](../report/dreamzero-standard-reference.md).
- If notation becomes heavy, use [Formula Sheet](../reference/formula-sheet.md) and [Glossary](../reference/glossary.md).

## Baseline Terms

These lecture chapters assume you can quickly refer back to the glossary for:

- [action](../reference/glossary.md#action)
- [policy](../reference/glossary.md#policy)
- [observation](../reference/glossary.md#observation)
- [embodiment](../reference/glossary.md#embodiment)
- [World Action Model](../reference/glossary.md#wam)

## Polanyi-Based Teaching Rule

Michael Polanyi's core claim is:

$$
\text{We know more than we can tell.}
$$

So each lecture chapter includes both:

- explicit content: definitions, equations, claims
- tacit content: what a competent reader notices, expects, or distrusts before the paper fully explains it

## What The First Four Chapters Are Doing

Chapters 1 through 4 are the orientation layer.
They are deliberately front-loaded with problem framing so a new reader does not meet [closed-loop control](../reference/glossary.md#closed-loop-control),
[proprioception](../reference/glossary.md#proprioception), or [world models](../reference/glossary.md#world-model) as unexplained jargon.

The later comparison chapter uses that vocabulary to show why two seemingly similar robot-foundation-model projects can still differ at the paper layer, the code layer, or both.
