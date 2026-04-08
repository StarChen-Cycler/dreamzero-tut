# Formula Sheet

This page keeps the most important formulas in one place.

Use [Source Verification](source-verification.md) to determine whether a formula below is fully source-literal or a teaching simplification.
Use [Notation Guide](notation-guide.md) when you need to decode what the symbols, subscripts, superscripts, or operators mean.

## Teaching Formulas

These are pedagogical simplifications used by the guide:

$$
\text{Task Success} \approx \text{Semantic Understanding} + \text{Physical Execution Skill}
$$

Interpretation:
task success needs both semantic understanding and physically competent execution.

$$
\text{Generalization Gap} \approx \text{Needed Physical Prior} - \text{Learned Physical Prior}
$$

Interpretation:
the more physical understanding the model lacks, the worse zero-shot performance should be.

$$
\text{formal description} + \text{practical judgment}
$$

Interpretation:
the lecture uses Polanyi's idea that explicit equations do not exhaust real engineering understanding.

## Paper-Derived Formulas

### Joint Prediction View

$$
\pi_0(\mathbf{o}_{l:l+H}, \mathbf{a}_{l:l+H} \mid \mathbf{o}_{0:l}, \mathbf{c}, \mathbf{q}_l)
=
\pi_0(\mathbf{o}_{l:l+H} \mid \mathbf{o}_{0:l}, \mathbf{c}, \mathbf{q}_l)
\pi_0(\mathbf{a}_{l:l+H} \mid \mathbf{o}_{0:l+H}, \mathbf{q}_l)
$$

Verification status:
checked semantically against PDF pages 6-7.
The extracted source label attached to this equation is OCR-corrupted, but the equation body and numbering are usable.

Interpretation:
DreamZero treats action prediction as tied to a predicted future world trajectory rather than as an isolated action-only mapping.

Notation help:
see [Notation Guide](notation-guide.md#symbol-table) for $\pi_0$, $\mathbf{o}$, $\mathbf{a}$, $\mathbf{c}$, $\mathbf{q}_l$, the conditioning bar, and the range notation `0:l` / `l:l+H`.

### Noisy Interpolation

$$
\mathbf{z}_{t_k}^{k} = t_k \mathbf{z}_{1}^{k} + (1 - t_k)\mathbf{z}_{0}^{k}
$$

Interpretation:
the noisy video latent is an interpolation between clean signal and Gaussian noise.

$$
\mathbf{a}_{t_k}^{k} = t_k \mathbf{a}_{1}^{k} + (1 - t_k)\mathbf{a}_{0}^{k}
$$

Interpretation:
the noisy action follows the same interpolation pattern, which keeps the two modalities aligned during denoising.

Notation help:
see [Notation Guide](notation-guide.md#symbol-table) for $\mathbf{z}$, $\mathbf{a}$, $t_k$, the chunk superscript $k$, and the Gaussian endpoints with subscripts `0` and `1`.

### Flow-Matching Objective

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

Verification status:
checked structurally against PDF page 7.
Exact typography and spacing should still be spot-checked in the PDF during any later publication pass.

Interpretation:
the model is trained to predict the correct denoising direction for the joint video-action state.

Notation help:
see [Notation Guide](notation-guide.md#notation-patterns) for $\mathbb{E}$, $\lVert \cdot \rVert^2$, $w(t_k)$, $\mathbf{u}_\theta$, $\mathbf{v}^k$, and the summation over $k$.

## Interpretation Rule

When using a formula in the lecture, always say:

1. whether it is literal from the paper or a teaching simplification
2. what practical question it answers
3. what hidden assumption it depends on
