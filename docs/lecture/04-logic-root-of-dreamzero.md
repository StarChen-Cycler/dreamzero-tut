# Logic Root Of DreamZero

This chapter rebuilds DreamZero from constraints.

!!! note "Reconstruction Layer"

    `Logic root` is our explanatory term, not the paper's own section title.
    It is a disciplined reconstruction of the paper's argument from verified claims in
    [Source Verification](../reference/source-verification.md).
    Treat the causal chain below as a teaching map for the robot [policy](../reference/glossary.md#policy),
    not as a direct quotation from the paper.

## Step 1: Name The Real Need

The target is not just task imitation.
The target is:

$$
\text{zero-shot physical generalization}
$$

That means the robot should perform useful new behavior in new environments without collecting full new demonstration sets.
This is where [embodiment](../reference/glossary.md#embodiment) and [closed-loop control](../reference/glossary.md#closed-loop-control) start to matter, because generalization is about real execution, not only instruction matching.

## Step 2: Ask What Prior Could Support That

If new motion must be handled, the model needs a prior over how the world changes through time.

So the next step is:

$$
\text{need temporal physical prior}
$$

## Step 3: Ask Where That Prior Could Come From

Web-scale video contains much richer temporal variation than static image-text corpora.

So:

$$
\text{video pretraining} \Rightarrow \text{better world dynamics prior}
$$

## Step 4: Ask How Actions Should Connect To That Prior

If future video and future action are learned jointly, actions are not predicted in isolation.

Instead:

$$
\text{action prediction} \leftrightarrow \text{predicted visual future}
$$

This is the move from VLA-style action prediction toward a World Action Model.
In glossary terms, the design is moving from a [VLA](../reference/glossary.md#vla) framing toward a [WAM](../reference/glossary.md#wam) framing.

## Step 5: Ask What Blocks Real Deployment

Even if the model is logically attractive, it must still run online.

So the design must satisfy:

$$
\text{physical prior quality} + \text{closed-loop speed} + \text{alignment}
$$

DreamZero answers this with:

- joint video-action denoising
- autoregressive generation
- [KV-cache](../reference/glossary.md#kv-cache) reuse
- system and kernel optimization

## Logic Root Summary

The shortest causal chain is:

$$
\text{unseen motion generalization}
\Rightarrow
\text{need temporal physical prior}
\Rightarrow
\text{use pretrained video backbone}
\Rightarrow
\text{jointly predict video and action}
\Rightarrow
\text{preserve alignment and closed-loop usability}
$$

That is the logic root this project will keep returning to.
