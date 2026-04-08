# Notation Guide

This page explains the symbols used in DreamZero's core equations.

Use this page together with:

- [Source Verification](source-verification.md)
- [Formula Sheet](formula-sheet.md)
- [Core Equations](../lecture/05-core-equations.md)

!!! note "Purpose"

    This page is not adding new mathematics to the paper.
    It is decoding the notation so a reader can tell what each symbol stands for,
    what structural role it plays, and where it appears in the verified source.

## How To Read The Symbols

Three things happen repeatedly in the DreamZero formulas:

1. a **base symbol** names a type of object, like observations or actions
2. a **subscript** narrows time, range, or denoising step
3. a **superscript** marks chunk identity or version of a quantity

For example:

$$
\mathbf{o}_{l:l+H}
$$

Read this as:

- `\mathbf{o}`: an observation-like quantity
- `l:l+H`: from time index $l$ through the prediction horizon $l+H$

And:

$$
\mathbf{z}_{t_k}^{k}
$$

Read this as:

- `\mathbf{z}`: a video latent variable
- superscript `k`: the $k$-th chunk
- subscript `t_k`: the noise level or denoising timestep assigned to that chunk

## Symbol Table

| Symbol | Meaning | Role In The Equations | Verified Source Anchor |
| --- | --- | --- | --- |
| $\pi_0$ | the DreamZero policy/model distribution | main prediction object in Equation (1) | `sources/doc2x/DreamZero-Nvidia.md:94` |
| $\mathbf{o}$ | visual observation or observation sequence | what the robot sees and what the model predicts into the future | `:91`, `:94` |
| $\mathbf{a}$ | action or action sequence | motor commands aligned with predicted futures | `:91`, `:94` |
| $\mathbf{c}$ | language instruction | text conditioning input | `:91`, `:94`, `:118` |
| $\mathbf{q}_l$ | proprioceptive state at time $l$ | body-state conditioning input at the current anchor time | `:91`, `:94` |
| $\mathbf{q}_k$ | proprioceptive state for chunk $k$ | conditioning input inside the training objective | `:118`, `:121` |
| $l$ | sampled time index in a trajectory | current anchor point from which future prediction starts | `:91` |
| $H$ | prediction horizon | how far into the future the model predicts | `:91`, `:94` |
| $\mathbf{o}_{0:l}$ | observation history from start to time $l$ | past visual context used for prediction | `:91`, `:94` |
| $\mathbf{o}_{l:l+H}$ | future observation slice | predicted future world state over the horizon | `:91`, `:94` |
| $\mathbf{o}_{0:l+H}$ | past plus future observation span | the observation span conditioning the inverse-dynamics side | `:94` |
| $\mathbf{a}_{l:l+H}$ | future action slice | predicted motor command sequence over the horizon | `:91`, `:94` |
| $\mathbf{z}$ | video latent variable | latent representation of video used in denoising/training | `:107`, `:110`, `:118` |
| $\mathbf{z}_0^k$ | Gaussian-noise video latent for chunk $k$ | noisy endpoint in Equation (2) | `:110`, `:113`, `:121` |
| $\mathbf{z}_1^k$ | clean video latent for chunk $k$ | clean endpoint in Equation (2) | `:110`, `:113`, `:121` |
| $\mathbf{z}_{t_k}^k$ | partially noised video latent at timestep $t_k$ | actual denoising target input for chunk $k$ | `:107`, `:110`, `:118` |
| $\mathbf{a}_0^k$ | Gaussian-noise action for chunk $k$ | noisy action endpoint in Equation (2) | `:110`, `:113`, `:121` |
| $\mathbf{a}_1^k$ | clean normalized action for chunk $k$ | clean action endpoint in Equation (2) | `:110`, `:113`, `:121` |
| $\mathbf{a}_{t_k}^k$ | partially noised action at timestep $t_k$ | action-side denoising input for chunk $k$ | `:107`, `:110`, `:118` |
| $k$ | chunk index | identifies one training chunk inside a trajectory | `:107`, `:118`, `:121` |
| $K$ | number of chunks or effective chunk count in the sum | normalization and iteration bound in Equation (3) | `:103`, `:118` |
| $t_k$ | denoising timestep for chunk $k$ | controls how noisy a chunk is during flow matching | `:107`, `:110`, `:118`, `:121` |
| $\mathcal{C}_k$ | clean context from previous chunks | historical clean context used while denoising the current chunk | `:113`, `:118` |
| $\mathbf{u}_\theta$ | learned model predicting joint velocity | the trainable function optimized in Equation (3) | `:115`, `:118` |
| $\theta$ | model parameters | parameter set optimized by the loss | `:115`, `:118` |
| $\mathbf{v}^k$ | target joint velocity for chunk $k$ | ground-truth denoising direction that the model should match | `:118`, `:121` |
| $w(t_k)$ | weighting function of the timestep | reweights the loss contribution at different noise levels | `:118`, `:121` |
| $\mathcal{L}(\theta)$ | training loss | objective minimized during learning | `:118` |
| $\mathbb{E}$ | expectation | average over sampled noise, actions, and timesteps | `:118` |
| $\lVert \cdot \rVert^2$ | squared norm | measures the size of the prediction error | `:118` |
| $\mathcal{N}(\mathbf{0}, \mathbf{I})$ | standard Gaussian distribution | source of noise used to corrupt clean variables | `:113` |
| $\left[\cdot,\cdot\right]$ | concatenation or grouping notation | combines video and action quantities into a joint object | `:118`, `:121` |
| $\mid$ | conditioning bar | means \"given\" the variables on the right side | `:94` |
| $\sim$ | \"is distributed as\" | states the probability law of the noise variables | `:113` |

## Notation Patterns

### 1. Conditioning Bars

In Equation (1), the vertical bar means:

$$
p(x \mid y) = \text{distribution of } x \text{ given } y
$$

In DreamZero, the right-hand side of the bar tells you what information the model is allowed to use.

### 2. Index Ranges

DreamZero often writes time ranges like:

$$
0:l \qquad \text{or} \qquad l:l+H
$$

Read these as slices:

- `0:l` means history from the start up to the current anchor
- `l:l+H` means future content over the prediction horizon

### 3. Superscripts For Chunks

In expressions like:

$$
\mathbf{z}_{t_k}^{k}
$$

the superscript `$k$` does **not** mean exponentiation.
It identifies which chunk the variable belongs to.

### 4. Expectations

In Equation (3),

$$
\mathbb{E}_{\mathbf{z},\mathbf{a},\{t_k\}}[\cdot]
$$

means the loss is averaged over sampled noisy variables and timesteps rather than computed on only one fixed noise draw.

### 5. Squared Norms

The quantity

$$
\left\lVert \mathbf{u}_\theta(\cdot) - \mathbf{v}^k \right\rVert^2
$$

is a squared error magnitude.
It says the model is penalized more when its predicted denoising direction is farther from the target direction.

## Equation Reading Walkthroughs

### Equation (1)

$$
\pi_0(\mathbf{o}_{l:l+H}, \mathbf{a}_{l:l+H} \mid \mathbf{o}_{0:l}, \mathbf{c}, \mathbf{q}_l)
$$

Read this as:

\"Predict the future observations and future actions, given the past observations, the language instruction, and the current proprioceptive state.\"

### Equation (2)

$$
\mathbf{z}_{t_k}^{k} = t_k \mathbf{z}_{1}^{k} + (1 - t_k)\mathbf{z}_{0}^{k}
$$

Read this as:

\"The noisy video latent for chunk $k$ is made by blending the clean latent and Gaussian noise according to timestep $t_k$.\"

### Equation (3)

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

Read this as:

\"Average the weighted squared prediction error over chunks and over sampled noisy states.\"

## Reader Advice

If a formula still feels dense, decode it in this order:

1. identify the base symbols
2. identify the conditioning variables
3. identify the time or chunk indices
4. only then interpret the operator structure

That order is usually enough to turn an unfamiliar formula into a readable sentence.
