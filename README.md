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

## Zooming in: token updates as rods

Each token's update to the residual stream (the difference between consecutive states) is decomposed against the sentiment axis: a signed component along it (back/forward), an off-axis magnitude, and a polar "rod" view drawn from a single reference point. Endpoint tokens are excluded as positional artifacts. The notebook includes an animated "needle" view of the same data — a rod deflecting per token: flat for neutral words, lifting for forward, dipping for back. (Animation runs in Colab; GitHub's preview shows a static frame.)

## Finding: the pivot carries the turn

The prediction was that "terrible" — the negative content word — would show the strongest backward tilt. It didn't. The strongest backward tilt falls on **"but"**: the model's state turns negative at the discourse pivot, before any negative word has arrived. The connective does the semantic work, and "terrible" lands in a state already turned.

In imaging terms: the contrast change appears at the boundary marker, not at the structure itself.

!["but" — the pivot dips the needle](needle_but.PNG)
!["wonderful" — the peak lifts it](needle_wonderful.PNG)

Against a random control axis, rods still tilt — but the tilts bear no relationship to word meaning, confirming the pattern is semantic rather than an artifact of projection.

Caveats, stated plainly: one sentence (n=1), one small model, one layer. Whether the pivot consistently out-tilts the content word is untested. This observation has earned a follow-up experiment, not a claim.

## Chapter 2: the soak — causal evidence that relational change compounds

A matched-pair control first *killed* an earlier finding: with vocabulary held constant, the apparent depth-growth of sentiment separation vanished — it had been substantially lexical. The honest successor was a causal, position-resolved probe: two sentences identical except one word ("wonderful"/"terrible").

Result: positions before the flip show exactly zero difference (causal hygiene). The flipped word carries a large change — trivially. But the five words *after* it — identical strings in both sentences — carry substantial flip-caused change, and that downstream "soak" **grows monotonically with depth** (0.69 at layer 0 to 7.25 at layer 5). One word's change spreads forward and compounds as the network processes.

![causal soak: where the flip lives, and how it compounds](soak_compounding.PNG)

Also in this chapter: a second validated feature axis (tense), found to live at the surface layers while sentiment develops deeper; the two axes are held nearly orthogonally (cosine −0.06). Surface features decay with depth; relational change compounds.

Caveats: n=1 sentence pair; the soak measures total downstream change, not sentiment-specific change; a dip at layer 3 is unexplained; one small model throughout.

## Imaging parallel

| Imaging | This probe |
|---|---|
| Registering OCT slabs to a reference frame | Difference-of-means to find the sentiment axis |
| Contrast enhancement to reveal faint structure | Projection onto the axis |
| Distinguishing true structure from instrument artifact | Random-direction control; identifying positional artifacts |

## Future direction

This reads one model, offline. The natural extension is a live **geometric harness**: monitoring a model's proximity to interpretable directions during generation and using that geometry as a control surface — flagging or gating on approach to safety-relevant regions of activation space. That's the larger idea this artifact is the first step toward.

## Chapter 3: The universality hypothesis — scale invariance and layer compression
If the geometric manifolds found in Chapters 1 and 2 are true structural features, they should persist across architectures. This chapter tests whether two different models (Pythia-70m and Pythia-160m) use the same coordinate system to process the same discourse pivot.
Method
Using an Orthogonal Procrustes transformation, I mapped the activation trajectories of both models to determine if the high-dimensional "shape" of the "but" pivot remains constant even as the parameter count scales.
Results
The geometric shape is highly conserved across scales, but its spatial "location" inside the network shifts significantly:
1. Structural Acceleration (Layer Compression). The 70M model resolves the "but" pivot near its relative midpoint (Layer 4). The 160M model, with more computational capacity, compresses this trajectory into its early layers (Layer 2). The larger model performs the same structural work, but it does it faster and earlier.
2. Isomorphic Conservation. A peak similarity score of 0.7452 confirms the networks are navigating a near-identical mathematical coordinate system. The manifold is not an artifact of initialization; it is a stable feature of the language optimization process.
3. The Dual-Peak Recurrence Anomaly. The manifold similarity is bimodal. It peaks at Layer 2, decays sharply through the intermediate layers, and resurges at the terminal exit gate (Layer 11). This suggests the model constructs the structural geometry early for contextual routing, then re-instantiates it at the final layer to directly steer the downstream token probability distribution.
Caveats, stated plainly: n=1 comparison of two related model sizes; the compression finding may vary in deeper or more diverse architectures; the dual-peak recurrence is a strong signal, but the mechanism behind this "late recall" remains hypothetical. This is a mapping of the terrain, not a causal proof of why the architecture chooses to recur.

## Run it

Open `activation_space_demo.ipynb` in Google Colab (free tier; CPU is sufficient for Pythia-70m). No API keys required.


