# Core Equations

This chapter translates the paper's central equations into plain language.

## Equation 1: Joint Policy Factorization

From the extracted paper:

$$
\pi_0(\mathbf{o}_{l:l+H}, \mathbf{a}_{l:l+H} \mid \mathbf{o}_{0:l}, \mathbf{c}, \mathbf{q}_l)
=
\pi_0(\mathbf{o}_{l:l+H} \mid \mathbf{o}_{0:l}, \mathbf{c}, \mathbf{q}_l)
\pi_0(\mathbf{a}_{l:l+H} \mid \mathbf{o}_{0:l+H}, \mathbf{q}_l)
$$

Interpretation:

- the model predicts future observations
- the model predicts future actions
- the action side is tied to what the future is supposed to look like

Pedagogically, this means DreamZero treats future video as part of the action reasoning process instead of a separate decoration.

## Equation 2: Noisy Interpolation

The paper defines noisy video latents and noisy actions by linear interpolation:

$$
\mathbf{z}_{t_k}^{k} = t_k \mathbf{z}_{1}^{k} + (1 - t_k)\mathbf{z}_{0}^{k}
$$

$$
\mathbf{a}_{t_k}^{k} = t_k \mathbf{a}_{1}^{k} + (1 - t_k)\mathbf{a}_{0}^{k}
$$

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

The source Markdown came from OCR-style extraction.
Before publishing a final report, check these equations against `DreamZero-Nvidia.pdf`.
