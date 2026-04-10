# Core Equations

This chapter translates the paper's central equations into plain language.

!!! note "Verified Versus Pedagogical Content"

    Equations (1) through (3) are based on the verified source checkpoints in
    [Source Verification](../reference/source-verification.md).
    The explanatory prose on this page is pedagogical.
    Where the extracted source contains OCR corruption such as `DEAMZERO`, this page uses the PDF-verified reading `DreamZero`.

## Equation 1: Joint Policy Factorization

From the verified source, Equation (1) expresses DreamZero as a joint prediction problem over future observations and future actions:

$$
\pi_0(\mathbf{o}_{l:l+H}, \mathbf{a}_{l:l+H} \mid \mathbf{o}_{0:l}, \mathbf{c}, \mathbf{q}_l)
=
\pi_0(\mathbf{o}_{l:l+H} \mid \mathbf{o}_{0:l}, \mathbf{c}, \mathbf{q}_l)
\pi_0(\mathbf{a}_{l:l+H} \mid \mathbf{o}_{0:l+H}, \mathbf{q}_l)
$$

In plain language, the policy is being described as "future world prediction plus action extraction conditioned on that future."

More explicitly, the paper is saying that joint prediction of video and action can be decomposed into two conceptual subproblems:

1. **autoregressive video prediction**
2. **action prediction from an inverse-dynamics model (IDM)**

That means Equation (1) should not be read as "two separate modules were trained independently."
It should be read as:

$$
\text{joint world-action prediction}
\Rightarrow
\text{future visual rollout} + \text{action recovery from that rollout}
$$

The first factor says:

- predict how the visual world will evolve from the current observation history, language instruction, and robot state

The second factor says:

- once the future visual trajectory is available, recover the action sequence that would make that future happen

This is exactly why the paper mentions an [IDM](../reference/glossary.md#idm) here.
An inverse-dynamics model is the part that infers actions from state transitions or desired future observations.
So the decomposition is conceptually:

$$
\underbrace{\text{what future should happen}}_{\text{video prediction}}
\quad + \quad
\underbrace{\text{what action would realize it}}_{\text{inverse dynamics}}
$$

The paper's next move is important:

- it uses this decomposition to explain the training objective
- but it does **not** implement two fully separate models
- instead, DreamZero trains one end-to-end system so video and action remain tightly aligned

Symbol legend:

- $\pi_0$: the DreamZero policy or model distribution
- $\mathbf{o}$: observation sequence
- $\mathbf{a}$: action sequence
- $\mathbf{c}$: language instruction
- $\mathbf{q}_l$: proprioceptive state at anchor time $l$
- $l:l+H$: the future range starting at $l$ and extending across horizon $H$

If you need the full symbol table, use [Notation Guide](../reference/notation-guide.md#symbol-table).

Interpretation:

- the model predicts future observations
- the model predicts future actions
- the action side is tied to what the future is supposed to look like

Pedagogically, this means DreamZero treats future video as part of the action reasoning process instead of a separate decoration.

!!! warning "Equation 1 Caveat"

    The extracted Markdown preserves the equation body well, but the label attached to this equation is OCR-corrupted in the raw source.
    Use the PDF-verified interpretation from [Source Verification](../reference/source-verification.md#equation-checklist), not the raw `DEAMZERO` string.

## Equation 2: Noisy Interpolation

The paper defines noisy video latents and noisy actions by linear interpolation:

$$
\mathbf{z}_{t_k}^{k} = t_k \mathbf{z}_{1}^{k} + (1 - t_k)\mathbf{z}_{0}^{k}
$$

This says the noisy video latent is made by blending a clean latent with Gaussian noise according to the timestep $t_k$.

$$
\mathbf{a}_{t_k}^{k} = t_k \mathbf{a}_{1}^{k} + (1 - t_k)\mathbf{a}_{0}^{k}
$$

This says the noisy action is constructed in the same way, so video and action are corrupted under a parallel schedule.

Symbol legend:

- $\mathbf{z}$: video latent representation
- $\mathbf{a}$: action representation
- subscript `0`: Gaussian-noise endpoint
- subscript `1`: clean endpoint
- superscript `$k$`: the $k$-th chunk
- $t_k$: denoising timestep for chunk $k$

The chunk superscript does not mean exponentiation.
See [Notation Guide](../reference/notation-guide.md#notation-patterns).

Read this as:

- start with clean targets
- mix them with Gaussian noise
- train the model to recover the direction back toward the clean signal

## Equation 3: Flow-Matching Objective

The paper then trains a model to predict a joint velocity field:

$$
\mathcal{L}(\theta) =
\mathbb{E}\left[
\frac{1}{K}\sum_{k=1}^{K}
w(t_k)
\left\lVert
\mathbf{u}_{\theta}(\cdot) - \mathbf{v}^{k}
\right\rVert^2
\right]
$$

In plain language, this loss penalizes the model when its predicted denoising direction differs from the target direction that points from noise toward the clean video-action pair.

Symbol legend:

- $\mathcal{L}(\theta)$: training loss as a function of parameters $\theta$
- $\mathbb{E}$: expectation, meaning an average over sampled noisy states and timesteps
- $\frac{1}{K}\sum_{k=1}^{K}$: average over chunks indexed by $k$
- $w(t_k)$: weighting function applied at timestep $t_k$
- $\mathbf{u}_\theta$: learned model prediction
- $\mathbf{v}^{k}$: target denoising direction for chunk $k$
- $\lVert \cdot \rVert^2$: squared error magnitude

If any of those symbols feel dense, use [Notation Guide](../reference/notation-guide.md#equation-reading-walkthroughs).

Do not let the notation obscure the point.
The teaching meaning is:

$$
\text{learn the denoising direction for video and action together}
$$

## Why These Equations Matter

Equation 1 says the policy is grounded in predicted visual futures.
Equations 2 and 3 say the training process learns video and action denoising as a shared problem.

That is why DreamZero's story is stronger than:

> "We added another head to predict actions."

Its claim is deeper:

> action quality improves when the model's internal future world picture improves.

## Range-Notation Reminder

The ranges in Equation (1) are the most important notation habit on this page:

- $\mathbf{o}_{0:l}$ means visual history up to the present anchor
- $\mathbf{o}_{l:l+H}$ means the future slice to be predicted
- $\mathbf{a}_{l:l+H}$ means the corresponding future action slice

This is why the equation reads like a mapping from past context to future world-action structure.

## Reader Warning

The raw math was preserved from the Doc2x extraction, but the verification authority is still `DreamZero-Nvidia.pdf`.
Use [Source Verification](../reference/source-verification.md#equation-checklist) as the starting checkpoint for any later equation cleanup.

For the end-to-end training sequence that operationalizes these equations, read [Industrial Training Guide](10-industrial-training-guide.md).
