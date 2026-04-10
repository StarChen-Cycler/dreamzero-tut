# DreamZero vs LingBot-VA

This chapter compares DreamZero with LingBot-VA across problem framing, architecture, training, inference, data assumptions, evaluation, and codebase shape.

!!! warning "Main Comparison Target"

    This section is about **LingBot-VA**.
    The main paper-level source is:

    - `sources/doc2x/lingbot-va-paper/lingbot-va-paper.md`

    The main code-level source is:

    - `lingbot-va-main/`

    An older LingBot paper, `LingBot-VLA`, still matters as historical context,
    but it is not the main object of comparison for this section.

## Why This Comparison Matters

DreamZero and LingBot-VA are both trying to use video-based generative structure to improve robot control, but they emphasize different bottlenecks.

DreamZero's central pressure is:

> how to get stronger zero-shot physical generalization on unseen tasks, unseen environments, and new embodiments

LingBot-VA's central pressure is:

> how to build a causal video-action world model that can remain reactive, maintain temporal memory, and support efficient closed-loop control

These are close enough to compare directly, but not identical.

## One-Sentence Contrast

In the shortest possible form:

$$
\text{DreamZero} \approx \text{world-action modeling for zero-shot physical generalization}
$$

$$
\text{LingBot-VA} \approx \text{causal autoregressive video-action world modeling for reactive robot control}
$$

## Comparison Table

| Aspect | DreamZero | LingBot-VA paper | LingBot-VA code | Why It Matters |
| --- | --- | --- | --- | --- |
| Primary identity | [WAM](../reference/glossary.md#wam) | autoregressive video-action world model | Wan-based causal video-action stack | These two projects are closer than DreamZero and a pure VLA baseline |
| Main problem pressure | zero-shot physical generalization on unseen tasks and environments | representation entanglement, reactivity gap, limited long-term memory, causality mismatch in chunk-based methods | practical post-training and benchmark deployment | DreamZero is more explicit about open-world generalization; LingBot-VA is more explicit about causal closed-loop deployment |
| Backbone story | Wan video-diffusion backbone plus DreamZero action head | Wan2.2-5B video stream plus smaller action stream in MoT | Wan2.2 latent transformer stack | Both lean on video-generation priors, but package them differently |
| Core modeling idea | predict future video and actions jointly in one aligned framework | interleave video and action tokens in one causal autoregressive process | implement a latent video-action transformer with action and latent branches | The similarities matter; the exact control story still differs |
| Training objective | joint video and action flow matching with teacher-forced chunking | dynamic loss + inverse-dynamics loss + later forward-dynamics deployment logic | separate latent and action losses in one trainer | DreamZero presents a more unified joint objective; LingBot-VA splits the training story more explicitly |
| Data story | DROID, AgiBot, YAM, new embodiment fine-tuning | about 16K hours from aggregated public robot datasets | latent LeRobot pipeline with action segmentation metadata | DreamZero foregrounds embodiment transfer more strongly; LingBot-VA foregrounds latent pipeline practicality |
| Deployment story | distributed WebSocket inference, DiT caching, optional TensorRT | KV cache, asynchronous inference, partial denoising, forward-dynamics grounding | server-client inference, offload mode, benchmark launch scripts | DreamZero emphasizes system optimization; LingBot-VA emphasizes causal rollout and asynchronous execution |
| Evaluation story | unseen tasks, unseen environments, cross-embodiment transfer, few-shot embodiment adaptation | RoboTwin, LIBERO, real-world long-horizon / precision tasks, sample efficiency | RoboTwin and LIBERO execution scripts | DreamZero evaluates open-world transfer pressure; LingBot-VA emphasizes long-horizon and precision deployment |

## Architecture Difference

### DreamZero

The verified DreamZero story is:

$$
\text{future world prediction} + \text{future action prediction}
$$

Code evidence:

- `dreamzero-main/groot/vla/model/dreamzero/base_vla.py`
- `dreamzero-main/groot/vla/model/dreamzero/transform/dreamzero_cotrain.py`
- `dreamzero-main/groot/vla/model/dreamzero/action_head/wan_flow_matching_action_tf.py`

Educationally, DreamZero says:

- use a strong video prior
- align future action with future visual evolution
- preserve a route to zero-shot physical generalization

### LingBot-VA paper

The new LingBot-VA paper says:

- reactive [VLA](../reference/glossary.md#vla) mappings suffer from representation entanglement
- chunk/open-loop world models struggle with real-time feedback, long-range memory, and causal consistency
- a unified autoregressive video-action process can solve those problems more naturally
- asynchronous deployment and forward-dynamics grounding are part of the method, not just implementation details

Educationally, that story is:

$$
\text{autoregressive world modeling} + \text{causal memory} + \text{reactive deployment}
$$

### LingBot-VA code

The released codebase implements a Wan-based latent video-action stack.

Code evidence:

- `lingbot-va-main/wan_va/modules/model.py`
- `lingbot-va-main/wan_va/train.py`
- `lingbot-va-main/wan_va/wan_va_server.py`

Observed pattern:

- Wan-based latent transformer
- latent video and action streams
- custom FSDP trainer
- benchmark-oriented serving scripts

Educational conclusion:

> if you compare paper ideas, DreamZero and LingBot-VA are both world-modeling projects;
> if you compare released codebases, they are still close, but differ in how they package data, training, and deployment.

## Data Pipeline Difference

### DreamZero

DreamZero code keeps a fuller robotics data pipeline visible:

- dataset conversion scripts in `dreamzero-main/scripts/data/`
- schema and dataset abstractions in `dreamzero-main/groot/vla/data/`
- new-embodiment documentation in `dreamzero-main/docs/DATASET_TO_GEAR_AND_TRAIN.md`

In teaching terms, DreamZero exposes more of the path from raw robot trajectories to training-ready data.

### LingBot-VA

LingBot-VA code expects:

1. LeRobot conversion
2. action segmentation metadata in `episodes.jsonl`
3. pre-extracted Wan2.2 latents placed under `latents/`

That means the code-level pipeline is more staged:

$$
\text{raw data} \rightarrow \text{LeRobot} \rightarrow \text{latent extraction} \rightarrow \text{training}
$$

Educational implication:

- DreamZero is better when teaching raw-to-policy dataset design
- LingBot-VA is better when teaching a latent-precompute robot training workflow

## Training Infrastructure Difference

### DreamZero

DreamZero training is framework-like:

- Hydra configs in `dreamzero-main/groot/vla/configs/`
- experiment wrapper in `dreamzero-main/groot/vla/experiment/experiment.py`
- LoRA and full-fine-tune scripts in `dreamzero-main/scripts/train/`

### LingBot-VA

LingBot-VA training is more tightly packaged:

- custom `Trainer` in `lingbot-va-main/wan_va/train.py`
- FSDP sharding in `lingbot-va-main/wan_va/distributed/fsdp.py`
- task-specific configs in `lingbot-va-main/wan_va/configs/`

Educational implication:

- DreamZero looks more like a reusable research framework
- LingBot-VA looks more like a focused implementation stack around one family of models

## Inference And Deployment Difference

### DreamZero

DreamZero deployment emphasizes:

- distributed inference over multiple GPUs
- WebSocket serving
- DiT caching
- optional TensorRT acceleration

This is consistent with DreamZero's systems claim that real-time robotic control is central to the method.

### LingBot-VA

LingBot-VA deployment emphasizes:

- asynchronous prediction and execution
- KV cache for autoregressive rollout
- offload mode for VAE and text encoder
- benchmark-facing launch scripts for RoboTwin and LIBERO

Educational implication:

- DreamZero teaches how a WAM is pushed into real-time distributed deployment
- LingBot-VA teaches how causal autoregressive rollout is stabilized and operationalized in benchmark pipelines

## Evaluation Difference

### DreamZero

DreamZero's tutorial logic is built around:

- unseen tasks
- unseen environments
- cross-embodiment transfer
- few-shot embodiment adaptation

### LingBot-VA

LingBot-VA's tutorial logic is built around:

- long-horizon manipulation
- precision manipulation
- deformable-object manipulation
- simulation plus real-world deployment
- sample efficiency in post-training

So DreamZero is more valuable when teaching:

$$
\text{open-world transfer and embodiment adaptation}
$$

while LingBot-VA is more valuable when teaching:

$$
\text{causal rollout quality, temporal memory, and reactive deployment}
$$

## Practical Takeaway

Use DreamZero as the central example when the tutorial question is:

- why joint video-action prediction might improve generalization
- why embodiment transfer matters
- why real-time system constraints should shape architecture

Use LingBot-VA as the comparison target when the tutorial question is:

- how a causal world-model argument differs from a reactive VLA argument
- how asynchronous inference changes the deployment story
- how a latent-precompute pipeline changes the engineering workflow
- how benchmark design changes what a paper can legitimately claim

## Historical Note

There is also an older `LingBot-VLA` paper in `sources/doc2x/lingbot-vla/lingbot-vla.md`.

That paper is still useful as background because it shows the earlier LingBot framing:

$$
\text{large-scale VLA scaling} + \text{throughput-centric infrastructure}
$$

But this section is intentionally named `dreamzero-vs-lingbot-va` because the newer `LingBot-VA` paper is the closer conceptual comparison to DreamZero.

## Polanyi Note

This comparison is a good example of Polanyi's point that:

$$
\text{we know more than we can tell}
$$

An experienced reader notices that:

- the older LingBot paper
- the newer LingBot-VA paper
- and the released LingBot-VA code

are related but not interchangeable.

That is a tacit warning sign.
The tutorial should make it explicit, because otherwise a newcomer will compare the wrong layers and draw the wrong conclusion.
