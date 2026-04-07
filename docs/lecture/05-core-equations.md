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

## Reader Warning

The raw math was preserved from the Doc2x extraction, but the verification authority is still `DreamZero-Nvidia.pdf`.
Use [Source Verification](../reference/source-verification.md#equation-checklist) as the starting checkpoint for any later equation cleanup.
