# Polanyi Lens

Michael Polanyi is useful here because DreamZero sits at the border between explicit mathematical description and tacit engineering judgment.

!!! note "Interpretive Lens, Not Source Claim"

    Michael Polanyi is not part of the DreamZero paper's own framing.
    This chapter is an external teaching lens used by this project to explain why a technically correct summary can still miss practical understanding.
    Keep source claims anchored in [Source Verification](../reference/source-verification.md), and use Polanyi only to interpret what the paper leaves tacit.

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

That second line is the important discipline:
Polanyi should reveal hidden judgment, not license extra claims that the source never made.

## Why This Matters For Non-Roboticists

Readers new to robotics often want a purely verbal or purely mathematical explanation.
That is too clean.

In practice, understanding this paper requires a third layer:

$$
\text{formal description} + \text{practical judgment}
$$

The lecture should teach that third layer explicitly instead of leaving it hidden.

## Evidence Discipline

When you use the Polanyi lens, keep two safeguards active:

1. separate source-literal statement from interpretive statement
2. state what evidence would weaken the interpretation

## Editorial Rule

Whenever a chapter introduces a formula or architecture claim, add:

- what an expert silently checks
- what could still go wrong
- what evidence would reduce doubt
