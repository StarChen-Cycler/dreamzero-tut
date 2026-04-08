# Glossary

## Action

A motor command sent to the robot.

## Observation

What the robot senses from the world, typically camera views plus internal state.

## Policy

A rule or model that maps available context to actions.

## Closed-Loop Control

A control pattern where each new action depends on fresh observations from the environment.

## Diffusion Model

A generative model trained to reverse a noise process and recover structured data from noisy inputs.

## Flow Matching

A training approach where the model learns the direction that moves a noisy sample toward the clean target.

## Embodiment

The robot's body, sensors, and actuators as a physical system.

## Horizon

The forward prediction span, often written as $H$, that tells you how far into the future the model predicts.

## IDM

Inverse dynamics model. A model that predicts actions from state transitions or observed futures.

## KV Cache

A transformer inference cache that reuses past key and value tensors to avoid recomputing the full context every step.

## Proprioception

Internal body state such as joint angles, velocities, or related motor state.

## Timestep

A scalar such as $t_k$ that controls the noise level or denoising stage of a sample.

## Latent

A compressed internal representation used by a model instead of raw pixels or other raw data.

## Chunk

A local segment of a longer sequence, often indexed by $k$ in the DreamZero equations.

## VLA

Vision-Language-Action model. A model that uses visual and language context to predict robot actions.

## VLM

Vision-Language Model. A model trained on image-text style tasks, usually strong at semantics.

## World Model

A model that predicts how the world state or observations will evolve over time.

## WAM

World Action Model. In this project, a model that predicts future world state and action in an aligned way.
