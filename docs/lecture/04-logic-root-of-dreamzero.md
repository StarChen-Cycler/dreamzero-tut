# Logic Root Of DreamZero

This chapter rebuilds DreamZero from constraints.

## Step 1: Name The Real Need

The target is not just task imitation.
The target is:

$$
\text{zero-shot physical generalization}
$$

That means the robot should perform useful new behavior in new environments without collecting full new demonstration sets.

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

## Step 5: Ask What Blocks Real Deployment

Even if the model is logically attractive, it must still run online.

So the design must satisfy:

$$
\text{physical prior quality} + \text{closed-loop speed} + \text{alignment}
$$

DreamZero answers this with:

- joint video-action denoising
- autoregressive generation
- KV-cache reuse
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
