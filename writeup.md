# Two features, one clock: evidence of scheduled concentration in Pythia

*[Repo link] — all results from locked pre-registered predictions;
kills recorded alongside survivals.*

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

## Setup (one screen, everything reproducible in free Colab)
- Difference-of-means axes, per layer; random-direction nulls
- The causal probe: matched pairs, one token flipped; on/off ratio on
  positions *after* the flip
- The ritual: predictions locked with confidence before each run; three
  of six died. The kills are preserved in the notebooks with their
  original confidence stakes, so the survivals can be read at face
  value: nothing here was selected after seeing the data.

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

## Finding 1: the change soaks forward and concentrates
Two sentences identical except one word (wonderful/terrible). Positions
before the flip show exactly zero difference; the identical words
*after* it carry substantial flip-caused change that grows with depth.
Decomposing that change against the sentiment axis: its on-axis share
climbs from ~13% at layer 0 to ~40% by mid-depth (n=9 held-out pairs,
~10× a random-direction null), then saturates. The residue decodes
coherently through the unembedding — structure, not noise — and its
coherence is assembled over roughly two layers, not present at the
embedding.

## Finding 2: concentration holds its share as scale thins the geometry
The same measurement across Pythia-70m, 160m, 410m, 1B (d_model 512 →
2048). The absolute concentration ceiling is roughly scale-stable at
~0.3–0.4 while the random null falls with 1/√d — so concentration rises
from ~9× to ~20× above chance. The network defends the same share of
the change on the feature direction while the geometry thins beneath it.

## Finding 3: the depth profile is a wave, and it's the network's
The concentration curve does not climb smoothly — it undulates, and
nine unrelated sentence pairs place their peaks and troughs at the same
fractional depths (mean pairwise r up to 0.80). The rival explanation —
axis-estimation wobble shared across pairs — was tested by building
axes from two disjoint sentence sets (per-layer cosine between them
only 0.51–0.82): the two sets produce the same wave, cross-set
correlation 0.96. The wave belongs to the network, not the probe.

## Finding 4: the period is fractional — three breaths at any depth
Hilbert phase analysis: the wave is a slow, stable oscillation —
period 8.43 layers in the 24-layer model. Run across scales: 2.65
cycles at 12 layers, 2.71 at 16, ~2.7 at 24 — fractional period 0.38,
0.37, 0.37. Three model depths, one dimensionless number. The
absolute-period alternative (predicting 1.4 and 1.9 cycles) is dead by
a factor of two. The network divides its depth into the same phases
whatever its layer budget.

## Finding 5: a second feature rides the same clock, out of phase
A tense axis (past/present), same causal probe, eight held-out pairs.
The tense wave is real — within-pair alignment 0.937, exceeding
sentiment's 0.80 — and its period matches (2.73 vs ~2.7 cycles). But
the two waves are uncorrelated (r = 0.008): matched frequency,
unmatched phase. Where sentiment redistributes, tense concentrates.
Two features time-share one clock. This is the result the title claims,
and the one I could not find in prior work.

## What died along the way
The chirp (killed): the wavelength does not stretch — which forced the
Hilbert analysis that found the true slow period. Layer-parity
(killed): the fast alternation is not an even/odd architectural
artifact (r = −0.17). The growth law (killed): no
