# DreamZero vs LingBot-VLA

This chapter compares DreamZero with LingBot-VLA across problem framing, architecture, training, inference, data assumptions, evaluation, and codebase shape.

!!! warning "Do Not Collapse Paper And Code Into One Thing"

    This comparison uses three distinct objects:

    - `DreamZero`: the DreamZero paper and released code
    - `LingBot-VLA (paper)`: the extracted paper in `sources/doc2x/lingbot-vla/lingbot-vla.md`
    - `LingBot-VA (code)`: the released repository in `lingbot-va-main/`

    The LingBot paper and the LingBot code release do **not** describe exactly the same stack.
    The paper presents a Qwen2.5-VL based VLA foundation-model story, while the released code is a Wan-based latent video-action system.
    This chapter keeps that distinction explicit because it is educationally important.

## Why This Comparison Matters

DreamZero and LingBot are both trying to make robot control more general, but they put the center of gravity in different places.

DreamZero's question is:

> Can a robot policy generalize better if action prediction is anchored to predicted future world evolution?

LingBot-VLA's paper question is:

> How far can VLA performance scale when we use much larger and more diverse real-world robot data with an efficient training stack?

Those are not the same question.

## One-Sentence Contrast

In the shortest possible form:

$$
\text{DreamZero} \approx \text{world-action modeling for zero-shot physical generalization}
$$

$$
\text{LingBot-VLA paper} \approx \text{large-scale VLA scaling and benchmarking}
$$

$$
\text{LingBot-VA code} \approx \text{Wan-based latent video-action training and deployment stack}
$$

## Comparison Table

| Aspect | DreamZero | LingBot-VLA paper | LingBot-VA code | Why It Matters |
| --- | --- | --- | --- | --- |
| Primary identity | [WAM](../reference/glossary.md#wam) | [VLA](../reference/glossary.md#vla) | Wan-based causal video-action stack | The learner should not assume they are the same model family |
| Main problem pressure | zero-shot physical generalization on unseen tasks and environments | scaling VLA performance with more real-world data and efficient infrastructure | practical post-training and benchmark deployment | The projects optimize for different bottlenecks |
| Backbone story | Wan video-diffusion backbone plus DreamZero action head | Qwen2.5-VL plus action expert in MoT architecture | Wan2.2 latent transformer stack | The paper-level LingBot story and code-level LingBot story diverge |
| Input representation | multi-view observations, language, proprioception, action chunks | three-view images, language, robot state, action chunks | pre-extracted video latents, text embeddings, actions | The code-level data assumptions are different |
| Training objective | joint video and action flow matching with teacher-forced chunking | conditional flow matching for action generation | separate latent loss and action loss in one training loop | The comparison is not only semantic; it changes what the model actually learns |
| Adaptation story | DROID, AgiBot, YAM, new embodiments | 20k-hour multi-platform VLA generalization story | RoboTwin, LIBERO, custom latent LeRobot datasets | The downstream use cases differ |
| Infra focus | distributed WebSocket inference, DiT caching, TensorRT path | paper emphasizes throughput and scaling evidence | FSDP, offload, benchmark launch scripts | One project emphasizes real-time policy deployment more strongly |
| Evaluation story | unseen task and environment generalization, cross-embodiment transfer, real robot claims | GM-100, RoboTwin, throughput benchmarking | RoboTwin and LIBERO execution scripts | The benchmark culture differs |

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

- use a video-model prior
- predict future observations and actions together
- treat action generation as aligned with a future world trajectory

### LingBot-VLA paper

The extracted paper says:

- start from a strong [VLM](../reference/glossary.md#vlm), specifically Qwen2.5-VL
- add an action expert
- organize the system through Mixture-of-Transformers
- scale data and benchmarking aggressively

Educationally, that story is much closer to:

$$
\text{semantic backbone} + \text{action expert} + \text{scale}
$$

### LingBot-VA code

The released codebase does not present the same architecture story as the paper.

Code evidence:

- `lingbot-va-main/wan_va/modules/model.py`
- `lingbot-va-main/wan_va/train.py`
- `lingbot-va-main/wan_va/wan_va_server.py`

Observed pattern:

- Wan-based latent video-action stack
- direct handling of latent tensors and action streams
- custom FSDP training loop

Educational conclusion:

> if you compare papers, DreamZero vs LingBot-VLA is partly a WAM-vs-VLA comparison;
> if you compare released codebases, DreamZero vs LingBot-VA is closer to a world-model stack vs another latent video-action stack comparison.

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

## Training Infrastructure Difference

### DreamZero

DreamZero training is framework-like:

- Hydra configs in `dreamzero-main/groot/vla/configs/`
- experiment wrapper in `dreamzero-main/groot/vla/experiment/experiment.py`
- LoRA and full-fine-tune scripts in `dreamzero-main/scripts/train/`

That structure is useful for learners who want to understand how one research codebase supports multiple embodiment paths and backbone variants.

### LingBot-VA

LingBot-VA training is more tightly packaged:

- custom `Trainer` in `lingbot-va-main/wan_va/train.py`
- FSDP sharding in `lingbot-va-main/wan_va/distributed/fsdp.py`
- task-specific configs in `lingbot-va-main/wan_va/configs/`

That structure is useful for learners who want a more direct path from config to training loop.

## Inference And Deployment Difference

### DreamZero

DreamZero deployment emphasizes:

- distributed inference over multiple GPUs
- WebSocket serving
- DiT caching
- optional TensorRT acceleration

This is consistent with DreamZero's systems claim that real-time robotic control is a central part of the method, not an afterthought.

### LingBot-VA

LingBot-VA deployment emphasizes:

- server-client separation
- benchmark integration for RoboTwin and LIBERO
- offload mode for VAE and text encoder
- launch scripts for concrete evaluation scenarios

This makes the release more benchmark- and deployment-script oriented.

## Evaluation Difference

### DreamZero

DreamZero's tutorial logic is built around:

- unseen tasks
- unseen environments
- cross-embodiment transfer
- few-shot embodiment adaptation

### LingBot-VLA paper

LingBot-VLA's paper logic is built around:

- large-scale real-world benchmark coverage
- three-platform comparison
- simulation benchmark performance
- training throughput as a first-class result

So DreamZero is more valuable when teaching:

$$
\text{why world modeling may improve control generalization}
$$

while LingBot-VLA is more valuable when teaching:

$$
\text{how VLA performance and infrastructure scale under large real-world data}
$$

## Practical Takeaway

Use DreamZero as the central example when the tutorial question is:

- why a [WAM](../reference/glossary.md#wam) differs from a [VLA](../reference/glossary.md#vla)
- why joint video-action prediction matters
- why real-time closed-loop execution constrains architecture
- why cross-embodiment transfer matters

Use LingBot-VLA or LingBot-VA as the comparison target when the tutorial question is:

- what a large-scale VLA scaling program looks like
- how benchmark design changes the story a paper tells
- how a released codebase may diverge from a paper-level architecture claim
- how latent-data pipelines change training assumptions

## Polanyi Note

This comparison is also a good example of Polanyi's point that:

$$
\text{we know more than we can tell}
$$

An experienced reader notices that the LingBot paper and code release are not describing the exact same system family.
That is a tacit warning sign.
The tutorial should make it explicit, because otherwise a newcomer will compare the wrong things and draw the wrong conclusion.
