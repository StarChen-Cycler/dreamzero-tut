# Why VLAs Hit A Wall

DreamZero starts from a diagnosis, not from architecture worship.

## Core Diagnosis

The paper's claim is that standard VLAs inherit strong semantic priors but weak physical priors.

In plain language:

- a VLA may know what an object is
- it may understand an instruction
- but it may still not know how a new motion should unfold in space and time

## Root Cause Map

### Symptom

The robot fails on unseen tasks, unseen motions, or unseen environments.

### Root Cause

The representation is rich in semantics and poor in physical dynamics.

### Mechanism

Static image-text pretraining does not directly teach:

- temporal evolution
- contact dynamics
- motion geometry
- control-sensitive visual change

## Teaching Equation

One compact way to state the paper's argument is:

$$
\text{Generalization Gap} \approx \text{Needed Physical Prior} - \text{Learned Physical Prior}
$$

Again, this is a didactic compression, not the paper's literal equation.

## Why Repeated Demonstrations Are A Weak Fix

Collecting many demonstrations can patch the gap, but it does not solve the structural cause.

The problem is:

$$
\text{coverage cost} \uparrow \quad \text{as} \quad \text{task diversity} \uparrow
$$

If the learning system needs repeated task-specific action data for every new motion, the scaling law is bad.

## What DreamZero Is Actually Trying To Buy

DreamZero is buying a different kind of prior:

$$
\text{video pretraining} \Rightarrow \text{spatiotemporal prior} \Rightarrow \text{better physical anticipation}
$$

That is the beginning of the logic root.

## Tacit Bridge

Practitioners often trust a policy more when its predicted future "looks physically sensible" even before success rates are fully measured.

That tacit judgment matters here:

- coherent predicted futures suggest aligned internal structure
- incoherent futures suggest the policy is guessing actions without a robust world picture
