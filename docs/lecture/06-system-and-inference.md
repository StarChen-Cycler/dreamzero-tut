# System And Inference

DreamZero is not only a modeling claim.
It is also a latency claim.

!!! note "System Story Versus Paper Story"

    The numbers on this page are taken from the verified paper checkpoints in
    [Source Verification](../reference/source-verification.md).
    The workflow equations below are explanatory compressions, not literal equations from the paper.

## Why Speed Is Structural

A robot in closed-loop control must repeatedly do:

$$
\text{observe} \rightarrow \text{infer} \rightarrow \text{act} \rightarrow \text{observe again}
$$

In plain language, the robot only stays relevant if its next decision arrives before the world has already moved on.

If inference is too slow, the policy becomes physically irrelevant.

Notation cue:

- the arrows are workflow arrows, not stochastic operators
- they are shorthand for temporal order in [closed-loop control](../reference/glossary.md#closed-loop-control)

## Why Autoregressive Generation Helps

The paper argues that autoregressive generation offers three benefits:

1. KV-cache reuse improves speed.
2. History can be reused without rebuilding everything.
3. Native frame-rate alignment is easier to preserve.

The engineering point is that alignment is not only a modeling concern.
It is also a systems concern.

The verified paper argument is that KV-cache reuse and native frame-rate preservation are part of why autoregressive generation is operationally attractive here.

## Closed-Loop Update Intuition

DreamZero uses real observations to replace predicted frames in the cache during execution.

Conceptually:

$$
\text{predicted future} \xrightarrow{\text{execute}} \text{real observation update}
$$

In plain language, DreamZero does not keep trusting its own imagined future once the real camera stream provides fresh evidence.

This reduces compounding error because the system does not keep hallucinating from its own past mistakes.

## Why 7 Hz Matters

The paper reports real-time closed-loop control at about:

$$
7 \text{ Hz}
$$

In plain language, that is the reported control loop frequency after the model and systems optimizations have been applied.

That is not just a benchmark number.
It is evidence that the model has crossed from "interesting offline architecture" into "plausible online controller."

## Verified Latency Anchors

The paper gives several concrete points that make the latency story legible:

$$
5.7 \text{ s per action chunk} \rightarrow 150 \text{ ms per action chunk}
$$

In plain language, the paper claims DreamZero moved from unusably slow single-GPU execution to a latency regime compatible with asynchronous closed-loop control.

Notation cue:

- `ms` means milliseconds
- `s` means seconds
- the arrow means a before/after latency comparison, not a learned function

$$
48 \text{ action steps at } 30 \text{ Hz} \approx 1.6 \text{ s per chunk}
$$

In plain language, the controller can overlap inference with execution because each action chunk lasts much longer than the final optimized inference latency.

Range-notation reminder:

This page uses timing notation instead of sequence ranges like $l:l+H$.
If you need to reconnect the timing story to the sequence notation in Equations (1)-(3), use [Notation Guide](../reference/notation-guide.md#notation-patterns).

$$
38\times \text{ cumulative speedup on GB200}
$$

In plain language, the paper's deployment story depends on both algorithmic and systems optimization, not on architecture alone.

Notation cue:

- $\times$ means multiplicative speedup
- $38\times$ means the optimized system is reported to be 38 times faster than the specified baseline setup

## Tacit Bridge

Robotics readers tacitly ask:

- Is the latency low enough for contact-rich tasks?
- Does prediction stay aligned with action timing?
- Is the speed claim coming from algorithmic design, systems engineering, or both?

DreamZero's answer is: both.
