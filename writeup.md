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
prediction. Each kill narrowed the survivor's description — [one line
on what each subtraction taught].

## Limitations, stated plainly
One model family. Two features. n=9/n=8 pairs. Hilbert phase on 12–24
samples. The schedule is *described, not explained* — no mechanism yet.
The 410m layer-13 anomaly remains open.

## Why this might matter for safety
If concentration onto readable directions follows a global schedule,
a monitoring harness needs one clock, not one per feature — and drift
toward a feature is visible at predictable depths before the
feature-word arrives. [The geometric-harness paragraph, tightened.]

## What's next
A third feature (plurality): does it claim a third phase slot?
Cross-scale phase offsets. The gating mechanism. Layer 13.
