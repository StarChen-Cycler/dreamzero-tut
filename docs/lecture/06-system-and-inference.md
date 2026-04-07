# System And Inference

DreamZero is not only a modeling claim.
It is also a latency claim.

## Why Speed Is Structural

A robot in closed-loop control must repeatedly do:

$$
\text{observe} \rightarrow \text{infer} \rightarrow \text{act} \rightarrow \text{observe again}
$$

If inference is too slow, the policy becomes physically irrelevant.

## Why Autoregressive Generation Helps

The paper argues that autoregressive generation offers three benefits:

1. KV-cache reuse improves speed.
2. History can be reused without rebuilding everything.
3. Native frame-rate alignment is easier to preserve.

The engineering point is that alignment is not only a modeling concern.
It is also a systems concern.

## Closed-Loop Update Intuition

DreamZero uses real observations to replace predicted frames in the cache during execution.

Conceptually:

$$
\text{predicted future} \xrightarrow{\text{execute}} \text{real observation update}
$$

This reduces compounding error because the system does not keep hallucinating from its own past mistakes.

## Why 7 Hz Matters

The paper reports real-time closed-loop control at about:

$$
7 \text{ Hz}
$$

That is not just a benchmark number.
It is evidence that the model has crossed from "interesting offline architecture" into "plausible online controller."

## Tacit Bridge

Robotics readers tacitly ask:

- Is the latency low enough for contact-rich tasks?
- Does prediction stay aligned with action timing?
- Is the speed claim coming from algorithmic design, systems engineering, or both?

DreamZero's answer is: both.
