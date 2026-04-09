# DreamZero Standard Reference

This report is the compact synthesis layer of the project.

!!! warning "Stable Reference, Not Final Authority"

    This page is the stable synthesis layer for the project.
    It is not the canonical source.
    For checked wording, equations, and claim anchors, use [Source Verification](../reference/source-verification.md) and `DreamZero-Nvidia.pdf`.

## One-Sentence Thesis

DreamZero argues that robot policies generalize better when action prediction is coupled to a pretrained video-based model of future world evolution rather than learned primarily as direct action imitation on top of static semantic priors.

## Problem Statement

Classical VLAs are strong at semantics but relatively weak at unseen physical motion.

The core gap is:

$$
\text{semantic prior} \not\Rightarrow \text{physical dynamics prior}
$$

So the paper asks a more structural question:

> What if the policy learns action through aligned future-world prediction instead of action-only imitation?

## Root Cause

The root cause used by this project is consistent across the lecture and report:

$$
\text{strong semantic prior} \neq \text{sufficient physical dynamics prior}
$$

In plain language, the earlier generation of models can know what a task means without knowing how a new motion should unfold in space and time.

## Root Cause Diagnosis

The report's causal reading of the paper is:

1. static pretraining gives object and language understanding
2. unseen motion requires temporal physical priors
3. repeated demonstrations are expensive and narrow
4. video pretraining offers richer dynamics structure
5. joint video-action modeling may transfer those priors into control

## Logic Root

The shortest design chain is:

$$
\text{need unseen-motion generalization}
\Rightarrow
\text{need temporal world prior}
\Rightarrow
\text{use pretrained video diffusion backbone}
\Rightarrow
\text{align future video with future action}
\Rightarrow
\text{make it fast enough for closed-loop execution}
$$

This report uses `logic root` as the minimal premise chain that makes DreamZero's design rational.

## Architecture In Plain Language

DreamZero conditions on:

- past and current observations
- language instruction
- proprioceptive state

It then predicts:

- future video chunks
- corresponding future action chunks

The paper's design intent is not just multi-task prediction.
It is modality alignment:

$$
\text{future action should make sense relative to the model's own future world prediction}
$$

## Core Mathematical Story

The paper gives a joint formulation:

$$
\pi_0(\mathbf{o}_{l:l+H}, \mathbf{a}_{l:l+H} \mid \mathbf{o}_{0:l}, \mathbf{c}, \mathbf{q}_l)
$$

Symbol legend:

- $\pi_0$: DreamZero policy/model distribution
- $\mathbf{o}$: observation sequence
- $\mathbf{a}$: action sequence
- $\mathbf{c}$: language instruction
- $\mathbf{q}_l$: proprioceptive state at anchor time $l$
- $l:l+H$: future slice from anchor time $l$ across horizon $H$
- $\mid$: \"given\"

Full notation reference:
[Notation Guide](../reference/notation-guide.md#symbol-table).

and trains the model with a flow-matching style denoising objective over both modalities.

The simplest interpretation is:

$$
\text{learn to recover coherent future video and action together from noise}
$$

Range-notation reminder:
the report uses compressed wording, but the source equations distinguish history slices like $\mathbf{o}_{0:l}$ from future slices like $\mathbf{o}_{l:l+H}$.
That distinction is one of the main structural ideas in the paper.

## Why Real-Time Matters

Without real-time inference, the model is not operational as a policy.

DreamZero therefore treats systems work as first-class:

- algorithmic scheduling
- caching
- quantization
- CUDA-level optimization

The reported result is about $7$ Hz closed-loop control after a reported $38 \times$ speedup.

## Why Cross-Embodiment Transfer Matters

The strongest strategic claim is that a world-action representation can transfer across bodies more efficiently than a narrow action-imitator.

This appears in two ways:

- video-only data from other humans or robots helps
- a new robot can be adapted with little additional play data

!!! note "Evidence-Limit Caution"

    These results are best read as evidence of a promising transfer mechanism, not as proof that DreamZero is generally embodiment-agnostic.
    The reported gains still depend on the paper's specific robots, tasks, and data mixtures.

## Limits And Open Questions

- How much of the gain is from video pretraining scale versus architecture?
- How robust is the result outside the reported task families?
- What breaks when visual prediction looks plausible but contact dynamics are wrong?
- How much embodiment-specific information remains hidden in the action decoder?
- How much of the reported transfer gain depends on embodiment similarity rather than a fully general world-action representation?
- What would happen if the sensing stack or action space changed more radically than in the paper's tested setups?

## Comparison Anchor

For a structured comparison against another major open robot-model release, read:

- [DreamZero vs LingBot-VLA](../lecture/09-dreamzero-vs-lingbot-vla.md)

That chapter is useful because it separates:

- DreamZero as a world-action-model story
- LingBot-VLA as a paper-level VLA scaling story
- LingBot-VA as a code-level latent video-action stack

## How To Use This Report

- Use the lecture chapters to teach the system.
- Use this report as the stable reference layer.
- Use the source map to trace claims back to the extracted paper.

## Context Entrypoints

If a future session needs to recover project intent quickly, start with:

1. `.memo/memodocs/user_spec_dream-zero-guide.md`
2. `.memo/memodocs/tech_spec_dream-zero-guide.md`
3. `CLAUDE.md`
4. `docs/reference/source-verification.md`
5. `docs/reference/source-map.md`
