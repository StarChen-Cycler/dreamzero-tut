# Formula Sheet

This page keeps the most important formulas in one place.

## Teaching Formulas

These are pedagogical simplifications used by the guide:

$$
\text{Task Success} \approx \text{Semantic Understanding} + \text{Physical Execution Skill}
$$

$$
\text{Generalization Gap} \approx \text{Needed Physical Prior} - \text{Learned Physical Prior}
$$

$$
\text{formal description} + \text{practical judgment}
$$

## Paper-Derived Formulas

### Joint Prediction View

$$
\pi_0(\mathbf{o}_{l:l+H}, \mathbf{a}_{l:l+H} \mid \mathbf{o}_{0:l}, \mathbf{c}, \mathbf{q}_l)
=
\pi_0(\mathbf{o}_{l:l+H} \mid \mathbf{o}_{0:l}, \mathbf{c}, \mathbf{q}_l)
\pi_0(\mathbf{a}_{l:l+H} \mid \mathbf{o}_{0:l+H}, \mathbf{q}_l)
$$

### Noisy Interpolation

$$
\mathbf{z}_{t_k}^{k} = t_k \mathbf{z}_{1}^{k} + (1 - t_k)\mathbf{z}_{0}^{k}
$$

$$
\mathbf{a}_{t_k}^{k} = t_k \mathbf{a}_{1}^{k} + (1 - t_k)\mathbf{a}_{0}^{k}
$$

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

## Interpretation Rule

When using a formula in the lecture, always say:

1. whether it is literal from the paper or a teaching simplification
2. what practical question it answers
3. what hidden assumption it depends on
