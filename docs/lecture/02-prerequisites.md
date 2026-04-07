# Math And Robotics Primer

This page defines only the background needed to follow the later chapters.

!!! warning "Teaching Approximation Ahead"

    This primer intentionally compresses the background.
    It is designed to make later chapters readable, not to serve as a full robotics textbook.
    For DreamZero-specific wording and claim checkpoints, defer to
    [Source Verification](../reference/source-verification.md).

## Minimal Robotics Vocabulary

- [observation](../reference/glossary.md#observation): what the robot senses, usually images plus state
- [action](../reference/glossary.md#action): motor command sent to the robot
- [policy](../reference/glossary.md#policy): a mapping from context to action
- [closed-loop control](../reference/glossary.md#closed-loop-control): the robot acts, sees the new state, then acts again
- [embodiment](../reference/glossary.md#embodiment): the robot's physical body and actuation layout

## Minimal Modeling Vocabulary

- [VLM](../reference/glossary.md#vlm): a vision-language model, strong at semantics
- [VLA](../reference/glossary.md#vla): a vision-language-action model, predicts actions from visual and language inputs
- [world model](../reference/glossary.md#world-model): a model that predicts how the world will evolve
- [WAM](../reference/glossary.md#wam): a world action model, here meaning a model that predicts future observations and actions jointly
- [diffusion model](../reference/glossary.md#diffusion-model): a model trained to reverse a noise process

## Why Semantics Alone Is Not Enough

A robot task needs at least two kinds of competence:

$$
\text{Task Success} \approx \text{Semantic Understanding} + \text{Physical Execution Skill}
$$

This is not a formal theorem. It is a teaching approximation.

VLAs inherit strong semantics from web-scale vision-language pretraining, but DreamZero argues that they are still weak on physical execution in unseen situations.
That contrast sets up why a [world model](../reference/glossary.md#world-model) or [WAM](../reference/glossary.md#wam)-style framing matters later.

## What To Watch For

As you read later chapters, keep three distinctions active:

1. `what` the robot should do
2. `how` the robot should move
3. `why` the model can generalize outside repeated demonstrations

## Tacit Bridge

A robotics practitioner often notices failure before they can cleanly formalize it.
They see hesitation, poor alignment, unstable contact, or brittle motion.
This is a Polanyi-style point:

$$
\text{explicit metric} \neq \text{full practical judgment}
$$

The lecture will keep that gap visible instead of pretending the equations alone are enough.
