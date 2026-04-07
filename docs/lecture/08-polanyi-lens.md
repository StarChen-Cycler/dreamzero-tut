# Polanyi Lens

Michael Polanyi is useful here because DreamZero sits at the border between explicit mathematical description and tacit engineering judgment.

## Core Principle

Polanyi's key statement is:

$$
\text{We know more than we can tell.}
$$

For this lecture, that means a reader must learn both:

- the explicit formalism
- the tacit cues that tell an expert whether the formalism is likely to work

## How To Use This In The Lecture

Each chapter should contain four layers:

1. `Statement`: what the paper explicitly says
2. `Mechanism`: the causal story behind the statement
3. `Tacit cue`: what an expert would notice before full proof
4. `Transfer question`: how to ask whether the idea will survive outside the paper

## Example

Explicit statement:

$$
\text{larger pretrained video models} \Rightarrow \text{better downstream action execution}
$$

Tacit interpretation:

- if predicted futures become more coherent, the action head has a better scaffold
- if futures look visually plausible but physically irrelevant, the policy may still fail

## Why This Matters For Non-Roboticists

Readers new to robotics often want a purely verbal or purely mathematical explanation.
That is too clean.

In practice, understanding this paper requires a third layer:

$$
\text{formal description} + \text{practical judgment}
$$

The lecture should teach that third layer explicitly instead of leaving it hidden.

## Editorial Rule

Whenever a chapter introduces a formula or architecture claim, add:

- what an expert silently checks
- what could still go wrong
- what evidence would reduce doubt
