# Two features, one clock: evidence of scheduled concentration in Pythia

*[Repo link] — all results from locked pre-registered predictions;
kills recorded alongside survivals.*

## Related work, and where this sits

Depth-phase structure in transformers has precedent. Lad et al. (2024)
identify four sequential stages of inference (detokenization, feature
engineering, prediction ensembling, residual sharpening). The
Mix-Compress-Refine framework (Queipo-de-Llano et al., 2025) organizes
depth into three phases — broad mixing (~0–20%), compressed computation
(~20–85%), selective refinement (~85–100%) — notably defined in
*fractional* depth, and traces the phase boundaries to massive
activations in the residual stream. The logit-lens tradition
(nostalgebraist 2020; Belrose et al. 2023) treats depth as iterative
inference, with prediction trajectories converging smoothly.

The delta here: prior work finds *monotonic* phases — one arc, one
direction. This work finds a *repeating cycle*: concentration onto a
feature direction oscillates ~2.7 times across depth, at a period that
is constant in fractional depth across a 4× range of layer counts, and
— the part I have not found anywhere — two features share the period
while occupying uncorrelated phases. The stages literature describes
acts; this looks like a beat within them. Whether the wave's quiet
stretches align with the Mix-Compress-Refine compression valley is an
open, testable question (see What's next).


## TL;DR
Flip one word in a sentence and trace how the change concentrates onto a
feature direction, layer by layer. The concentration doesn't climb — it
oscillates. The oscillation (1) survives an axis-split control (r = 0.96
across disjoint probe sets), (2) holds a **fractional** period: ~2.7
cycles across network depth at 12, 16, and 24 layers (0.37 ± 0.01), and
(3) is shared by a second feature — tense waves with alignment 0.94 at
the same period but **zero phase correlation** with sentiment (r = 0.01).
Same clock, different beats. This looks less like "the network breathes"
and more like "the network schedules."

## Setup - The ritual: predictions locked with confidence before each run; 
three of six died. The kills are preserved in the notebooks with their
  original confidence stakes, so the survivals can be read at face
  value: nothing here was selected after seeing the data.

- Difference-of-means axes, per layer; random-direction nulls
- The causal probe: matched pairs, one token flipped; on/off ratio on
  positions *after* the flip
- The ritual: predictions locked with confidence before each run;
  three of six died. [1–2 sentences on why this matters for trusting
  what follows]

## Finding 1: the change soaks forward and concentrates  [ch2–3, compressed]
## Finding 2: concentration holds its share as scale thins the geometry  [ch4]
## Finding 3: the depth profile is a wave, and it's the network's  [ch4→5 axis-split]
## Finding 4: the period is fractional — three breaths at any depth  [the 0.37 table]
## Finding 5: a second feature rides the same clock, out of phase  [the multiplexing plot]

## What died along the way
The chirp. Layer-parity. The growth law. The instant-consequence
prediction. Each kill narrowed the survivor's description — The chirp (killed): the wavelength does not stretch — which forced the
Hilbert analysis that found the true slow period. Layer-parity (killed):
the fast alternation is not an even/odd architectural artifact
(r = −0.17). The growth law (killed): no geometric envelope
(r² = 0.005) — the oscillation runs at constant loudness, gated rather
than growing. The instant-consequence prediction (killed, ch3):
consequence is assembled over ~2 layers, not present at the embedding.
Each kill subtracted a wrong description and left the survivor sharper:
a fixed-amplitude, gated, fractional-period oscillation.


## Limitations, stated plainly
One model family. Two features. n=9/n=8 pairs. Hilbert phase on 12–24
samples. The schedule is *described, not explained* — no mechanism yet.
The 410m layer-13 anomaly remains open.

## Why this might matter for safety
If concentration onto readable directions follows a global schedule,
a monitoring harness needs one clock, not one per feature — and drift
toward a feature is visible at predictable depths before the
feature-word arrives. 
The longer arc this work serves is a live geometric harness: monitoring
a model's proximity to interpretable directions during generation and
using that geometry as a control surface. The schedule finding sharpens
the proposal twice over. If drift toward a feature is visible at
predictable fractional depths regardless of the sentence, monitoring
can be depth-targeted rather than exhaustive. And if features share one
clock at fixed phase offsets, a harness needs to learn one rhythm per
network, not one per feature — the schedule, once mapped, is the index.

## What's next
A third feature (plurality): does it claim a third phase slot?
Cross-scale phase offsets. The gating mechanism. Layer 13.
