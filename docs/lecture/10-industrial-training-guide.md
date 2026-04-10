# Industrial Training Guide

This section explains how DreamZero is trained as a real system, not only as a paper idea.

The goal is to connect three layers:

1. the **paper-level formulation**
2. the **code-level training path**
3. the **operational training plan** you would actually execute

!!! warning "High-Confidence Scope"

    This page is restricted to training steps that can be grounded in:

    - the DreamZero paper in `sources/doc2x/dreamzero-vla/DreamZero-Nvidia.md`
    - the verified checkpoints in [Source Verification](../reference/source-verification.md)
    - the DreamZero training code under `dreamzero-main/`

    When the public scripts use small smoke-test defaults like `max_steps=100`, this page will say so explicitly instead of pretending those defaults reproduce the paper's full training runs.

## Why This Section Exists

The earlier equation pages explain what DreamZero's objective means.
This page answers a different question:

> What is the exact training sequence that turns those equations into an executable pipeline?

In industrial terms, this is the gap between:

$$
\text{mathematical objective}
\quad \text{and} \quad
\text{reproducible training system}
$$

## Source Backbone

This guide is based primarily on:

- `sources/doc2x/dreamzero-vla/DreamZero-Nvidia.md`
- `dreamzero-main/groot/vla/model/dreamzero/action_head/wan_flow_matching_action_tf.py`
- `dreamzero-main/groot/vla/model/dreamzero/transform/dreamzero_cotrain.py`
- `dreamzero-main/groot/vla/experiment/base.py`
- `dreamzero-main/groot/vla/experiment/experiment.py`
- `dreamzero-main/groot/vla/configs/conf.yaml`
- `dreamzero-main/groot/vla/configs/model/dreamzero/action_head/wan_flow_matching_action_tf.yaml`
- `dreamzero-main/scripts/train/droid_training_lora.sh`
- `dreamzero-main/scripts/train/droid_training_full_finetune.sh`

## The Three Core Equations, Repositioned For Training

The three essential equations already introduced earlier are:

### Equation (1): Joint World-Action Formulation

$$
\pi_0(\mathbf{o}_{l:l+H}, \mathbf{a}_{l:l+H} \mid \mathbf{o}_{0:l}, \mathbf{c}, \mathbf{q}_l)
=
\pi_0(\mathbf{o}_{l:l+H} \mid \mathbf{o}_{0:l}, \mathbf{c}, \mathbf{q}_l)
\pi_0(\mathbf{a}_{l:l+H} \mid \mathbf{o}_{0:l+H}, \mathbf{q}_l)
$$

Training meaning:

- the system is conceptually solving future video prediction and future action prediction together
- but the paper explicitly says it does **not** train two separate models
- the code realizes this with one unified model call that predicts both video and action noise fields in one forward pass

Code anchor:
`dreamzero-main/groot/vla/model/dreamzero/action_head/wan_flow_matching_action_tf.py:763-775`

### Equation (2): Noise Construction

$$
\mathbf{z}_{t_k}^{k} = t_k \mathbf{z}_{1}^{k} + (1 - t_k)\mathbf{z}_{0}^{k},
\qquad
\mathbf{a}_{t_k}^{k} = t_k \mathbf{a}_{1}^{k} + (1 - t_k)\mathbf{a}_{0}^{k}
$$

Training meaning:

- clean video latents and clean actions are pushed onto a noisy interpolation path
- each chunk gets its own denoising timestep
- the model learns from corrupted current chunks while still seeing clean previous chunks

Code anchors:

- timestep and noise construction:
  `dreamzero-main/groot/vla/model/dreamzero/action_head/wan_flow_matching_action_tf.py:706-758`
- scheduler application:
  `:743-754`

### Equation (3): Weighted Flow-Matching Objective

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

Training meaning:

- the model predicts the denoising direction for the current noisy chunk
- the loss is a timestep-weighted mean-squared error
- in the actual implementation there is a video-side loss and, when actions are present, an action-side loss

Code anchors:

- video loss:
  `dreamzero-main/groot/vla/model/dreamzero/action_head/wan_flow_matching_action_tf.py:777-789`
- action loss:
  `:791-800`

## System View

At a high level, DreamZero training looks like this:

$$
\text{dataset} \rightarrow \text{transform} \rightarrow \text{video/action corruption}
\rightarrow \text{joint model forward} \rightarrow \text{weighted losses}
\rightarrow \text{optimizer step}
$$

The important engineering detail is that every block in that chain corresponds to an actual code path.

## Step-by-Step Training Sequence

| Step | What Happens | Mathematical Change | Main Code Anchor |
| --- | --- | --- | --- |
| 1 | Launch the training job with Hydra config and `torchrun` | select optimizer, batch, horizon, frames, and architecture mode | `dreamzero-main/scripts/train/droid_training_lora.sh`, `dreamzero-main/scripts/train/droid_training_full_finetune.sh` |
| 2 | Instantiate the DreamZero model and action head | bind the paper objective to one `VLA` model with a Wan-based action head | `dreamzero-main/groot/vla/configs/conf.yaml`, `dreamzero-main/groot/vla/configs/model/dreamzero/vla.yaml`, `dreamzero-main/groot/vla/model/dreamzero/base_vla.py` |
| 3 | Load pretrained components | initialize the trainable robot stack from Wan, T5, CLIP, and VAE weights | `wan_flow_matching_action_tf.py:240-312` |
| 4 | Freeze or unfreeze modules depending on training mode | choose which parameter subspace $\theta$ is actually optimized | `wan_flow_matching_action_tf.py:322-413` |
| 5 | Build the dataset and modality transform | convert raw multi-view trajectories, states, actions, and language into model-ready tensors | `dreamzero_cotrain.py:504-622`, `droid_relative.yaml` |
| 6 | Sample noise timesteps and Gaussian noise | move clean variables onto the interpolation path of Equation (2) | `wan_flow_matching_action_tf.py:706-758` |
| 7 | Run a joint forward pass for video and action | estimate $\mathbf{u}_\theta$ for both modalities at once | `wan_flow_matching_action_tf.py:760-775` |
| 8 | Compute weighted dynamics and action losses | instantiate Equation (3) as weighted MSE terms and sum them | `wan_flow_matching_action_tf.py:777-810` |
| 9 | Backpropagate, optimize, and log | update trainable parameters, track loss averages, save metadata/checkpoints | `base.py:408-433`, `base.py:435-470`, `base.py:781-812` |

## Step 1: Launch Configuration

The public DreamZero scripts expose two main DROID-style modes:

### LoRA adaptation mode

Script:
`dreamzero-main/scripts/train/droid_training_lora.sh`

Observed defaults:

- `train_architecture=lora`
- `num_frames=33`
- `action_horizon=24`
- `num_views=3`
- `learning_rate=1e-4`
- `bf16=true`
- `frame_seqlen=880`

### Full fine-tuning mode

Script:
`dreamzero-main/scripts/train/droid_training_full_finetune.sh`

Observed differences:

- `train_architecture=full`
- `learning_rate=1e-5`
- DeepSpeed config switches to `zero2_offload.json`
- `save_lora_only=false`

!!! note "Paper Scale Versus Public Script Defaults"

    The paper reports pretraining at 100K steps with global batch size 128.
    The public scripts shown in this repo use very small defaults such as `max_steps=100` for smoke-test or practical launch convenience.
    They are useful for understanding the pipeline, but they should not be confused with the full paper-scale training regime.

## Step 2: Model Assembly

The model is assembled through Hydra config composition:

- top-level training config:
  `dreamzero-main/groot/vla/configs/conf.yaml`
- model wrapper:
  `dreamzero-main/groot/vla/configs/model/dreamzero/vla.yaml`
- action-head config:
  `dreamzero-main/groot/vla/configs/model/dreamzero/action_head/wan_flow_matching_action_tf.yaml`

Operationally:

1. `VLA` is instantiated as the top-level container
2. the DreamZero action head is bound into that container
3. the action head loads:
   - Wan diffusion backbone
   - text encoder
   - image encoder
   - VAE

Mathematically, this fixes the parameterized map:

$$
\theta \mapsto \mathbf{u}_{\theta}
$$

that will later be optimized by Equation (3).

## Step 3: Parameter Freezing And Trainable Subspace

The code supports at least two major training modes:

### LoRA mode

- all parameters are first frozen
- LoRA adapters are injected into the diffusion model
- state encoder, action encoder, and action decoder remain trainable
- text encoder, image encoder, and VAE stay frozen

### Full mode

- the diffusion model is trainable
- text encoder, image encoder, and VAE still remain frozen

This means the optimized parameter set is not always the full model.
In industrial terms, DreamZero changes the effective training problem from:

$$
\min_{\theta} \mathcal{L}(\theta)
$$

to:

$$
\min_{\theta_{\text{trainable}}} \mathcal{L}(\theta_{\text{frozen}}, \theta_{\text{trainable}})
$$

where only a subset of modules may actually move.

## Step 4: Data And Transform Pipeline

The transform `DreamTransform` is the bridge between dataset records and model-ready tensors.

What it does:

1. stack and arrange multi-view video
2. clean and tokenize language
3. pad state and action channels to configured maxima
4. build masks for valid state and action dimensions
5. attach embodiment identifiers

Key code:
`dreamzero-main/groot/vla/model/dreamzero/transform/dreamzero_cotrain.py:504-622`

Industrial reading:

- before the model sees a batch, the raw robotics record has already been normalized into a common shape
- this is where cross-embodiment training becomes mechanically possible

## Step 5: Noise Injection

This is where Equation (2) becomes an actual tensor operation.

The code samples:

- Gaussian noise for video latents
- Gaussian noise for action tensors
- timesteps for video chunks
- timesteps for action chunks

Then it applies:

$$
\mathbf{z}_{t_k}^{k} \leftarrow \text{add\_noise}(\mathbf{z}_1^k, \mathbf{z}_0^k, t_k)
$$

$$
\mathbf{a}_{t_k}^{k} \leftarrow \text{add\_noise}(\mathbf{a}_1^k, \mathbf{a}_0^k, t_k)
$$

with scheduler-driven targets:

$$
\mathbf{v}^{k}_{\text{video}} = \text{training\_target}(\mathbf{z}_1^k, \mathbf{z}_0^k, t_k)
$$

$$
\mathbf{v}^{k}_{\text{action}} = \text{training\_target}(\mathbf{a}_1^k, \mathbf{a}_0^k, t_k)
$$

Key code:
`wan_flow_matching_action_tf.py:743-754`

Important implementation nuance:

- the code supports coupled and decoupled video-action noise schedules
- the base training path can keep action timestep tied to video timestep
- the config also exposes later variants for higher-noise video emphasis and decoupled schedules

## Step 6: Teacher-Forced Context

The paper says DreamZero uses teacher forcing over chunk-wise video denoising.
In the code, the current noisy chunk is not predicted from nothing.
The model also receives:

- clean context from earlier chunks
- state features
- text embeddings
- embodiment id

The forward call includes:

- `noisy_latents`
- `noisy_actions`
- `clean_x=latents.transpose(1, 2)`

That last argument is the practical clue that previous clean visual context is explicitly present during training.

So the industrial reading is:

$$
\text{current noisy chunk} + \text{clean history} \rightarrow \text{predict denoising direction}
$$

## Step 7: Joint Forward Pass

The model predicts both:

- `video_noise_pred`
- `action_noise_pred`

inside one forward call.

This is the executable counterpart of the paper's refusal to split video prediction and inverse dynamics into two independent models.

The paper-level idea from Equation (1) is therefore implemented as:

1. one shared backbone call
2. one synchronized noisy input state
3. two output branches for video-side and action-side denoising predictions

Key code:
`wan_flow_matching_action_tf.py:760-775`

## Step 8: Loss Construction

The loss in code is not a symbolic single-line formula anymore.
It becomes two weighted terms:

### Dynamics loss

$$
\mathcal{L}_{\text{dyn}} =
\operatorname{mean}
\left(
w(t_k)
\cdot
\operatorname{MSE}(
\widehat{\mathbf{v}}_{\text{video}}^{k},
\mathbf{v}_{\text{video}}^{k})
\right)
$$

### Action loss

$$
\mathcal{L}_{\text{act}} =
\operatorname{mean}
\left(
w(t_k^{a})
\cdot
\operatorname{MSE}(
\widehat{\mathbf{v}}_{\text{action}}^{k},
\mathbf{v}_{\text{action}}^{k})
\cdot
\text{action\_mask}
\cdot
\text{has\_real\_action}
\right)
$$

### Total loss

$$
\mathcal{L}_{\text{total}} = \mathcal{L}_{\text{dyn}} + \mathcal{L}_{\text{act}}
$$

Key code:
`wan_flow_matching_action_tf.py:777-810`

This is one of the most important industrial clarifications:

- Equation (3) gives the abstract weighted flow-matching objective
- the code realizes it as a dynamics term plus an action term
- the action term is masked by action validity and by whether the example truly contains a real action label

## Step 9: Trainer-Level Optimization

After the model returns:

- `loss`
- `dynamics_loss`
- `action_loss`

the trainer:

1. extracts `loss`
2. logs moving averages of the auxiliary loss terms
3. groups parameters for weight decay
4. applies optimizer updates
5. saves metadata and checkpoints

Key code:

- `base.py:408-433` for loss extraction and logging
- `base.py:435-470` for optimizer grouping
- `base.py:781-812` for trainer creation and callbacks

The code therefore upgrades the paper's objective into a real training operation:

$$
\text{forward} \rightarrow \text{loss decomposition} \rightarrow \text{optimizer step} \rightarrow \text{checkpoint/log}
$$

## Algorithm 1: Industrial DreamZero Training Job

```text
Algorithm 1 Industrial DreamZero Training Job

Input:
  dataset root D
  pretrained Wan / T5 / CLIP / VAE weights W
  training config C

1: Compose Hydra config C
2: Instantiate DreamZero VLA model M from config
3: Load pretrained weights W into action head submodules
4: Freeze or unfreeze modules according to training mode
5: Instantiate train dataset and data collator
6: Dump merged metadata for normalization and deployment use
7: Create trainer with optimizer, callbacks, and checkpoint policy
8: for each training step do
9:     batch <- next(train_dataloader)
10:    outputs <- M(batch)
11:    loss <- outputs["loss"]
12:    backpropagate(loss)
13:    optimizer.step()
14:    log moving averages of dynamics_loss and action_loss
15: end for
```

## Algorithm 2: Per-Batch DreamZero Training Step

```text
Algorithm 2 Per-Batch DreamZero Training Step

Input:
  transformed batch B = {video, language, state, action, masks}

1: Encode language to prompt embeddings
2: Encode or prepare video latents z_1
3: Prepare normalized action targets a_1
4: Sample Gaussian noise z_0, a_0
5: Sample video timesteps t_k and action timesteps t_k^a
6: Form noisy latents z_t^k and noisy actions a_t^k
7: Compute training targets v_video^k and v_action^k
8: Run joint model forward pass:
       (v̂_video^k, v̂_action^k) <- model(z_t^k, a_t^k, clean context, language, state)
9: Compute weighted dynamics loss
10: Compute masked weighted action loss
11: Sum both losses to obtain total loss
12: Return {loss, dynamics_loss, action_loss}
```

## What Changes Mathematically During Training

At each batch, DreamZero changes the state of learning in three distinct ways:

### 1. Clean data is moved onto a noise path

Before the model predicts anything, clean variables are transformed into noisy variables:

$$
(\mathbf{z}_1^k, \mathbf{a}_1^k)
\rightarrow
(\mathbf{z}_{t_k}^k, \mathbf{a}_{t_k}^k)
$$

### 2. The model predicts denoising directions

The trainable network does not directly output final actions during training.
It outputs velocity-like denoising predictions:

$$
\widehat{\mathbf{v}}^{k}_{\text{video}}, \widehat{\mathbf{v}}^{k}_{\text{action}}
$$

### 3. Parameters are updated to reduce weighted flow error

After the weighted losses are computed, the optimizer moves the trainable parameters so that:

$$
\widehat{\mathbf{v}}^{k} \rightarrow \mathbf{v}^{k}
$$

more accurately over future batches.

This is the operational meaning of training:

$$
\theta_{t+1} \leftarrow \theta_t - \eta \nabla_{\theta}\mathcal{L}_{\text{total}}
$$

## Industrial Checklist

If you were auditing whether a DreamZero training run matches the intended method, check:

1. Are the correct pretrained Wan, text encoder, image encoder, and VAE weights loaded?
2. Is the dataset converted and transformed into the expected multi-view, state, action, and language format?
3. Are the correct trainable modules enabled for LoRA or full mode?
4. Are video and action noise timesteps being sampled as intended?
5. Is the model receiving clean context plus noisy current chunks?
6. Are both `dynamics_loss` and `action_loss` being produced?
7. Are the weighting terms from the scheduler actually applied?
8. Are metadata, checkpoints, and loss logs being written?

## Relationship To Earlier Sections

Use the earlier sections like this:

- [Core Equations](05-core-equations.md): what the three paper equations mean
- [Notation Guide](../reference/notation-guide.md): what the symbols in those equations mean
- [System and Inference](06-system-and-inference.md): how the trained model is later turned into a real-time control system

This section is the bridge between those layers.
