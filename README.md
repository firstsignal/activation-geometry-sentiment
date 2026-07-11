# activation-geometry-sentiment

Finding and validating a sentiment direction in a language model's activations — a mechanistic interpretability probe framed through medical imaging.

## What this is

A weekend probe of a small language model (Pythia-70m) that locates a single linear direction in the model's residual stream corresponding to sentiment, validates it against a control, and traces it token-by-token as the model reads text.

The framing is deliberate. I spent several years as an ophthalmic imaging specialist building computational pipelines (AOSLO, OCT) that make faint biological structure legible and quantifiable. Interpretability is the same problem on a different instrument: find the reference frame in which hidden structure becomes visible, then build the readout. This is the first cell of that idea.

## Method

1. **Acquire.** Run Pythia-70m over small labelled sets of positive and negative sentences, capturing the residual stream at a middle layer (mean-pooled over tokens).
2. **Register.** Take the difference of the positive and negative means — this is the sentiment axis (a difference-of-means probe).
3. **Validate.** Project the training sentences onto the axis. Positives and negatives separate into two non-overlapping clusters. A **random control direction** does not separate them — confirming the signal is real structure, not an artifact of arbitrary projection.
4. **Trace.** Project each token of a fresh, sentiment-turning sentence onto the axis to read the signal as the model processes text.

## Results

The sentiment axis cleanly separates positive from negative sentences; the random control does not. In the token trace, the axis correctly elevates the positive word ("wonderful").

Honest limitations, kept in view rather than hidden:
- The signal on content tokens is **faint**, as expected for a 70m-parameter model.
- The largest deflections fall on the **first token** (an attention-sink / positional artifact) and the **final punctuation token**, not on the semantically negative word — a positional effect, not sentiment. Identifying and excluding these is part of the point.

![sentiment trace, first token excluded](trace.PNG)

Before excluding the first token, positional artifacts dominate the scale:

![raw trace including first-token artifact](trace_raw.PNG)

## Imaging parallel

| Imaging | This probe |
|---|---|
| Registering OCT slabs to a reference frame | Difference-of-means to find the sentiment axis |
| Contrast enhancement to reveal faint structure | Projection onto the axis |
| Distinguishing true structure from instrument artifact | Random-direction control; identifying positional artifacts |

## Future direction

This is a static readout. The natural extension is a live **geometric harness**: monitoring a model's proximity to interpretable directions during generation and using that geometry as a control surface — flagging or gating on approach to safety-relevant regions of activation space. That's the larger idea this artifact is the first step toward.

## Run it

Open `activation_space_demo.ipynb` in Google Colab (free tier; CPU is sufficient for Pythia-70m). No API keys required.
