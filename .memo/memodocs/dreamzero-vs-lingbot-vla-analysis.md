# DreamZero vs LingBot-VLA Analysis

## Investigation Scope

This memo compares:

- `dreamzero-main/`
- `lingbot-va-main/`
- `sources/doc2x/lingbot-vla/lingbot-vla.md`

The purpose is to support an educational comparison section inside the tutorial docs.

## Critical Interpretation Warning

The LingBot materials split into two layers:

1. `sources/doc2x/lingbot-vla/lingbot-vla.md`
   - describes **LingBot-VLA**
   - positions the system as a large-scale **VLA foundation model**
   - states a **Qwen2.5-VL** plus action-expert **Mixture-of-Transformers** architecture
2. `lingbot-va-main/`
   - ships code for **LingBot-VA**
   - uses a **Wan-based** latent video-action stack
   - requires `wan22_pretrained_model_name_or_path`
   - operates on **pre-extracted video latents** rather than raw pixel training inputs

This means the comparison page must explicitly distinguish:

- `LingBot-VLA (paper layer)`
- `LingBot-VA (code layer)`

and must not pretend they are identical.

## High-Level Contrast

### DreamZero

- Paper framing: **World Action Model**
- Core claim: joint video and action prediction improves zero-shot physical generalization
- Codebase shape:
  - generic VLA shell in `dreamzero-main/groot/vla/model/dreamzero/base_vla.py`
  - DreamZero-specific transform and action head in:
    - `dreamzero-main/groot/vla/model/dreamzero/transform/dreamzero_cotrain.py`
    - `dreamzero-main/groot/vla/model/dreamzero/action_head/wan_flow_matching_action_tf.py`
- Training stack:
  - Hydra + Transformers-style experiment wrapper
  - DeepSpeed / ZeRO oriented scripts
  - LoRA and full fine-tuning paths
- Deployment stack:
  - distributed WebSocket inference server
  - DiT caching
  - optional TensorRT path

### LingBot-VLA (paper)

- Paper framing: **pragmatic VLA foundation model**
- Core claim: VLA performance scales with massive real-world robot data and efficient infrastructure
- Architecture claim:
  - pre-trained **Qwen2.5-VL** backbone
  - action expert
  - **Mixture-of-Transformers**
- Data claim:
  - about **20,000 hours**
  - **9 dual-arm robot platforms**
- Evaluation claim:
  - real-world GM-100 benchmark
  - RoboTwin simulation
  - codebase throughput benchmark

### LingBot-VA (code)

- Code framing: **causal world modeling for robot control**
- Backbone/config shape:
  - Wan-based transformer stack in `lingbot-va-main/wan_va/modules/model.py`
  - config path points to `wan22_pretrained_model_name_or_path`
- Training stack:
  - custom trainer in `lingbot-va-main/wan_va/train.py`
  - FSDP sharding in `lingbot-va-main/wan_va/distributed/fsdp.py`
- Data path:
  - LeRobot dataset plus `action_config` metadata
  - explicit **latent extraction** step before training
  - dataset loader in `lingbot-va-main/wan_va/dataset/lerobot_latent_dataset.py`
- Deployment stack:
  - `lingbot-va-main/wan_va/wan_va_server.py`
  - offload VAE and text encoder to CPU
  - RoboTwin and LIBERO evaluation scripts

## Comparison Dimensions

| Dimension | DreamZero | LingBot-VLA paper | LingBot-VA code |
| --- | --- | --- | --- |
| Primary identity | WAM | VLA | causal video-action stack |
| Backbone story | Wan video model + robot action head | Qwen2.5-VL + action expert | Wan2.2 latent video stack |
| Main training object | joint video and action prediction | conditional action generation with strong VLM semantics | latent video and action denoising |
| Data story | DROID / AgiBot / new embodiment workflows | 20k-hour multi-platform scaling story | post-training on latent LeRobot datasets |
| Adaptation emphasis | new embodiment fine-tuning, YAM / AgiBot | large-scale multi-platform VLA adaptation | RoboTwin / LIBERO / custom latent dataset fine-tuning |
| Training framework | Hydra + experiment wrapper + DeepSpeed scripts | paper emphasizes throughput benchmark | custom Trainer + FSDP |
| Inference shape | distributed WebSocket server, DiT cache, TensorRT option | paper-level deployment framing | server-client inference, offload mode, Wan latent generation |
| Educational meaning | why world modeling may beat pure semantic imitation | why scale and infrastructure matter for VLA training | why released code may drift from paper framing |

## Codebase Structure Contrast

### DreamZero Code Organization

- `dreamzero-main/groot/vla/`
  - reusable framework-style structure
  - model, data, transforms, configs, experiment utilities
- `dreamzero-main/scripts/`
  - explicit train/data/inference scripts
- `dreamzero-main/docs/`
  - dataset conversion and new-embodiment guides
- `dreamzero-main/socket_test_optimized_AR.py`
  - direct distributed server entrypoint

Interpretation:
DreamZero exposes a more framework-like training and embodiment-adaptation pipeline.

### LingBot-VA Code Organization

- `lingbot-va-main/wan_va/`
  - tighter package centered on one stack
  - modules, train loop, dataset, distributed utils, configs
- `lingbot-va-main/evaluation/`
  - benchmark-facing launch scripts
- `lingbot-va-main/script/`
  - shell launchers
- `lingbot-va-main/example/`
  - example observation bundles

Interpretation:
LingBot-VA exposes a more benchmark-and-post-training oriented package around one model family.

## Data Pipeline Contrast

### DreamZero

Evidence:

- `dreamzero-main/README.md`
- `dreamzero-main/docs/DATASET_TO_GEAR_AND_TRAIN.md`
- `dreamzero-main/scripts/data/*.py`
- `dreamzero-main/groot/vla/data/`

Observed pattern:

- supports raw dataset conversion into LeRobot or DreamZero-specific training flows
- keeps modality schemas explicit
- supports embodiment-specific mappings
- training flow still operates on richer modality processing inside the model/data stack

### LingBot-VA

Evidence:

- `lingbot-va-main/README.md` around latent extraction
- `lingbot-va-main/wan_va/dataset/lerobot_latent_dataset.py`

Observed pattern:

- requires a prior latent-extraction step
- stores `.pth` latent files alongside video metadata
- dataset loader consumes latent tensors and text embeddings directly

Educational implication:

- DreamZero teaches more about how a robot foundation model consumes raw or minimally processed multi-view trajectories
- LingBot-VA teaches more about a latent-precompute training pipeline

## Training Infrastructure Contrast

### DreamZero

Evidence:

- `dreamzero-main/groot/vla/experiment/experiment.py`
- `dreamzero-main/groot/vla/configs/conf.yaml`
- `dreamzero-main/README.md`

Observed pattern:

- Hydra-configured experiments
- Transformers-like training arguments
- DeepSpeed-focused training scripts
- LoRA and full fine-tuning options

### LingBot-VA

Evidence:

- `lingbot-va-main/wan_va/train.py`
- `lingbot-va-main/wan_va/distributed/fsdp.py`

Observed pattern:

- custom training loop
- explicit FSDP sharding and activation checkpointing
- manual checkpoint save/load flow

Educational implication:

- DreamZero is closer to a configurable research framework
- LingBot-VA is closer to a purpose-built training system around one implementation stack

## Deployment Contrast

### DreamZero

Evidence:

- `dreamzero-main/socket_test_optimized_AR.py`
- `dreamzero-main/README.md`

Observed pattern:

- distributed inference across GPUs
- WebSocket server
- DiT caching
- TensorRT acceleration path
- real-time robot-control orientation

### LingBot-VA

Evidence:

- `lingbot-va-main/wan_va/wan_va_server.py`
- `lingbot-va-main/README.md`

Observed pattern:

- server-client deployment split
- offload VAE and text encoder to CPU
- explicit benchmark launch scripts
- Wan latent/action generation loops

Educational implication:

- DreamZero comparison should emphasize real-time control systems and WAM deployment
- LingBot-VA comparison should emphasize packaging for benchmark execution and post-training workflows

## Recommended Tutorial Framing

The docs comparison page should answer:

1. Are these projects solving the same problem?
2. Where do the paper-level model identities differ?
3. Where do the released codebases differ?
4. Why does that difference matter for a learner?

The comparison page should explicitly use three labels:

- `DreamZero`
- `LingBot-VLA (paper)`
- `LingBot-VA (code)`

That naming discipline is essential to avoid false equivalence.
