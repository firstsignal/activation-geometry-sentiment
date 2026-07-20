# Three features, one clock: phase encodes feature identity in Pythia

*All code, notebooks, and figures: [github.com/firstsignal/activation-geometry-sentiment](https://github.com/firstsignal/activation-geometry-sentiment) — predictions locked before each run; kills recorded alongside survivals. If you use this work or the phaseprobe method, please cite the repository (Naomi James, 2026).*

---

## TL;DR

Flip one word in a sentence and track how the change moves through a transformer's residual stream, and you find three things the standard stage-models of transformer depth don't predict. **First**, the share of the change concentrated on a feature direction doesn't climb monotonically with depth — it *oscillates*, completing ~2.7 cycles across the network regardless of whether the network is 12, 16, or 24 layers deep (the period is fixed in fractional depth, not layers). **Second**, three different features — sentiment, plurality, tense — ride this same oscillation, each locked to its own phase of it: sentiment at 0°, plurality at ~21°, tense at ~89°, each angle held pair-by-pair across nine independent sentence pairs (resultant lengths ≥ 0.96), with the distinctness of the angles confirmed by two-sample circular tests (permutation p = 0.0052). Phase appears to encode feature *identity*. **Third**, the first cross-family test (GPT-2) shows the wave itself transfers and strengthens, but the clock rate doesn't: GPT-2 completes 1.60 cycles where Pythia completes 2.7. The oscillation's *form* looks universal; its frequency is a per-network constant. All of it was found with difference-of-means directions, a projection, and free-tier Colab.

---

## How this started

Not with a hypothesis about clocks. It started as a small artifact: a minimal harness that watches a model's internal state geometrically during generation — projections onto known feature directions, read token by token — rather than probing behaviour black-box. The first experiment was deliberately humble: a sentiment direction in Pythia-70m, difference-of-means over ten labelled sentences, project each token's activation onto it, look.

The first trace was mostly *artifact* — the largest swings were the first-token attention sink and the final period, not sentiment at all. The lesson of day one became the method of the whole project: read the trace honestly, tell signal from artifact, and never claim the bend you hoped for. Every experiment after was pre-registered — predictions and confidence stakes locked before running — and every kill kept in the notebooks next to the survivals. Eight locked predictions died this way; two instrument failures are preserved with their autopsies; one statistically significant result was discarded because it failed a quality gate set in advance.

Then came the two questions that turned an artifact into research. The first was about a single word doing live work: the test sentence was chosen because it *turns* — "The movie started out wonderful **but** became absolutely terrible" — and the earliest traces showed the state bending at "but," before "terrible" arrives: the model registering that a reversal is coming ahead of the content that delivers it. The second made it causal: **flip one word — *wonderful* to *terrible* — and where does the change actually go?**

## The soak

It doesn't stay on the word. Run two sentences through the model, identical except for the flip, and subtract the residual streams position by position. Upstream of the flip: exactly zero difference (causal hygiene, confirmed). On the flipped word: a large difference, as expected. But *downstream* — on words that are identical in both sentences — the difference is not zero, and it grows with depth: from 0.69 at layer 0 to 7.25 at layer 5 in the smallest model. The states of unchanged words *soak up* the flip. I started calling this the soak, and the rest of the project is a progressively more careful attempt to characterise it.

Decomposing the soak at each downstream position into its component along the sentiment axis versus everything orthogonal gave the first quantitative surprise: only ~40% of the flip-caused change lives on the sentiment direction, even though sentiment is the only thing that changed. Decoding the *residue* through the unembedding showed it isn't noise — the wonderful-run lifts context-appropriate positive vocabulary at downstream positions while the terrible-run lifts accusatory vocabulary. The network wasn't trained to represent sentiment; it was trained to predict, and the flip re-weights everything downstream. The 40% is the shadow that re-weighting casts on the one direction we had a filter for.

## Concentration is defended across scale

The obvious scaling prediction is dilution: as models grow and one direction becomes a vanishing fraction of the space (512 dimensions → 2048), the share of the soak on the feature axis should shrink. It doesn't. Across Pythia-70m, 160m, 410m, and 1B, the concentration ceiling stays roughly constant in absolute terms while the random-direction baseline collapses — so concentration relative to chance *rises* from ~9× to ~20×. The network defends the readability of the feature direction as the geometry thins beneath it. (Locked prediction: scale-invariant ceiling. Survived.)

## The wave

The depth profile of concentration was supposed to be a climb — concentrate more as you go deeper, then stop. It isn't. Detrend the concentration-versus-depth curve and a coherent oscillation appears: concentrate, redistribute, concentrate again. Nine unrelated sentence pairs place their crests and troughs at the same fractional depths (within-pair alignment 0.80 at 410m), and the oscillation survived an axis-split control (two independently-built axes, alignment 0.96), killed a chirp hypothesis, killed a period-2 parity hypothesis, and killed a growth-law hypothesis (log-amplitude vs phase: r² = 0.005 — the envelope is gated, not growing).

Hilbert analysis put a number on it: the mean period is ~8.4 layers in a 24-layer model — roughly **2.7 cycles across the full network**. And the decisive test: is the period fixed in absolute layers or in fractional depth? Pythia-160m (12 layers) and Pythia-1B (16 layers) both complete ~2.7 cycles too. The period is a constant of the network expressed in fractional depth. Whatever sets the rhythm scales with the network.

A second feature — tense, past versus present, probed with its own matched single-token-flip pairs — waves at the same period with even higher coherence (alignment 0.937). But the two waves were *uncorrelated*: r = 0.008. Matched frequency, unmatched phase. At the time I wrote "the network schedules." I didn't yet understand what the zero correlation was hiding.

## The slots

Chapter 6 added a third feature — plurality, singular versus plural, with past-tense noun-only flip pairs designed to carry no verb agreement — and asked where it sits on the shared clock. Three predictions were locked. The first pass produced the pattern this project keeps producing: the plurality wave survived at the wire (alignment 0.637 against a locked 0.6 threshold), the period prediction was apparently killed (3.98 cycles), and the phase comparison was *gated* — the averaged waves were too weak for their relative phase to be measured, and a retroactive check suggested even chapter 5's tense–sentiment phase offset might be a drift-average rather than a held relationship.

Two instrument failures, preserved in the notebook, diagnosed the problem. An amplitude-masked comparison collapsed because the features' loud layers don't coincide — only 1–6 of 24 layers survived a joint mask, and a resultant length computed on one layer is true by definition and means nothing. And a split-half test showed plurality's 3.98-cycle estimate scattering from 2.8 to 6.3 across random halves: not a period, but what Hilbert phase reports when fast noise rides a weak carrier. The kill was downgraded to unresolved, and both failures pointed at the same repair: stop asking one averaged wave for its phase, and ask the sentence pairs individually.

The per-pair instrument is the core method of this post. Each pair's individual concentration wave is phase-compared against the sentiment reference wave, with circular statistics across pairs and a self-control: sentiment pairs compared against their own mean must cluster tightly, or nothing else can be read. The control passed at **R = 0.99**. Then the structure appeared.

Five testifying tense pairs report offsets between 1.44 and 1.68 radians — mean **+1.55 rad = 0.49π: quadrature to two decimals** (R = 1.00, Rayleigh p < 0.001). This resolves chapter 5's puzzle exactly: two same-period waves at 90° correlate at zero. The r = 0.008 that read as "unrelated" was orthogonality's shadow all along. And the locked prediction that grammatical features would share a slot **died cleanly**: plurality's nine pairs cluster at +0.37 rad ≈ 21° (R = 0.97) — nowhere near tense's 89°, and distinct from sentiment's 0°. The distinctness was then tested rather than assumed: Watson–Williams F = 11.55, p = 0.0037; an assumption-free permutation test (10k shuffles) gives p = 0.0052. The result reproduced across four runs, two VMs, and two weights formats, with the permutation p ranging 0.0052–0.0056 — the verdict does not depend on the run.

![Per-pair phase offsets vs the sentiment clock (Pythia-410m): sentiment control at 0° (R=0.99), tense at 89° (R=1.00), plurality at 21° (R=0.97)](https://raw.githubusercontent.com/firstsignal/activation-geometry-sentiment/main/per_pair_phase_410m.PNG)

**Three features, three statistically distinct, tightly-held angles, one shared clock. Phase appears to encode feature identity, not feature class.**


## The equation

Everything above compresses into one statement. For feature *f* in model *m*, the detrended concentration wave at fractional depth *t* ∈ [0, 1] is

**w<sub>f,m</sub>(t) = A<sub>f,m</sub>(t) · cos(2πν<sub>m</sub>·t + φ<sub>f,m</sub>)**

with the content carried by three constraints: **ν belongs to the network, not the feature** (one frequency for all features, fixed in fractional depth across layer budgets); **φ<sub>f</sub> belongs to the feature, not the sentence** (a fixed phase address, held pair-by-pair, distinct across features); **A<sub>f</sub> is a gated envelope** (no growth law — the clock always ticks, the loudness comes and goes). The subscript on ν was not in the original formulation. The next section is why it had to be added.

## The stranger: first cross-family contact

Everything to this point is one model family, and every number could in principle be a fact about EleutherAI's training recipe. So the pipeline was packaged into a reusable probe (`phaseprobe`: model name in — clock rate, phase addresses, and clock-face figure out) and handed to a foreign model: GPT-2 small — different lab, different data, different tokenizer, different architectural details. Three predictions locked.

**The wave exists in GPT-2 — survived, strongly.** Sentiment within-pair alignment 0.877, *above* Pythia-410m's 0.80. Nine sentence pairs place their crests at the same fractional depths in a model Pythia never met. The wave is a property of trained transformers, not one family.

**The period transfers — killed, and the kill is the finding.** GPT-2 completes 1.60 cycles against Pythia's ~2.7. This is not noise: GPT-2's self-control clusters at R = 0.97, so the wave is coherent — it simply ticks at its own rate. A pre-named sub-outcome landed: **family-specific clock, universal clock-structure.** The equation survives with its first constraint scoped by data: ν is a per-network constant, not a constant of transformers. Both waves on one fractional-depth axis is the figure I'd keep if I could keep only one: two breathers, one ruler, different rates. The rhythm travels; the tempo doesn't. (The clock rate also reproduces exactly across float16/float32, GPU/CPU, and sessions — whatever sets a network's tempo is frozen in its weights, not in how you run it.)
![Two breathers, one ruler — the sentiment wave in Pythia-410m (ν≈2.7) and GPT-2 (ν≈1.6) on one fractional-depth axis](https://raw.githubusercontent.com/firstsignal/activation-geometry-sentiment/main/two_breathers_one_ruler.PNG)


**The slots transfer — gated, no verdict.** Tense and plurality per-pair resultant lengths collapse to 0.17 and 0.11 in GPT-2 — far below the locked R > 0.7 gate. A permutation test on those scattered angles printed p = 0.014 and was *disregarded per the gate*: a mean angle computed on near-uniform scatter is not a slot address, and a significance test on it is not evidence of one. Slot structure in GPT-2 is unresolved, not absent. Three suspects, in order: Pythia-tuned axes (the grammatical axis sets were built and validated on Pythia's representations — and the pattern of sentiment thriving while grammar vanishes entirely points here); instrument starvation (1.6 cycles over 12 layers gives per-pair Hilbert phase far less to work with than 2.7 over 24); and, only after those two are killed, GPT-2 genuinely not slotting grammatical features. Chapter 7 remains open until they're adjudicated.
![The stranger's clock (GPT-2): sentiment locks (R=0.97), tense and plurality scatter — the gate that withheld the verdict](https://raw.githubusercontent.com/firstsignal/activation-geometry-sentiment/main/phase_slots_gpt2.PNG)


## Where this sits relative to prior work

The stage-models of transformer depth — detokenisation → processing → retokenisation; mix → compress → refine; stages-of-inference framings — describe monotonic, one-directional progressions. Probing literature typically reports feature decodability rising to a plateau. I could not find prior work reporting (a) a repeating oscillation in feature concentration with a period fixed in fractional depth, or (b) feature-specific phase addresses on a shared cycle. The nearest neighbours — work on rotational and circular feature geometry, and on periodic structure in learned representations — concern geometry *within* a layer's representation space, not rhythm *across* depth. If someone knows prior art for either claim, I genuinely want to see it; the delta claimed here is the depth-rhythm and its phase-addressing, not the linear-representation machinery used to find it.

Why it might matter for safety, stated modestly: the earlier chapters show drift toward a feature is visible on readable directions before the feature-word arrives, and increasingly so relative to chance as models scale. The wave and slot findings add that the visibility is *scheduled*: one rhythm per network, one phase address per feature. A monitoring harness that learns a network's clock might tell *which* feature is concentrating from *when* it concentrates. That is a hypothesis about a control surface, not a demonstrated capability — but it is a different proposal from post-hoc explanation, and it is the direction this work is walking.

## Limits, stated plainly

Two model families; three features; nine matched sentence pairs per feature; one probe recipe (difference-of-means + projection ratio); tense's Pythia slot rests on n = 5 after three pairs failed a joint-loudness rule; all phase angles are measured against sentiment's mean curve as the reference clock; the plurality axis may carry verb-agreement flavour from its axis-building sets; GPT-2's grammatical collapse is undiagnosed as of this writing; the mechanism generating the oscillation is entirely unknown. None of this is yet a law. It is a reproducible pattern with a falsifiable equation over it, and the instruments are simple enough to hand to anyone.

## What's next

Three queued experiments close chapter 7: GPT-2-native grammatical axes (does the collapse reverse when the antenna matches the model?), GPT-2-medium at 24 layers (does the starvation suspect die?), and a cross-family ν survey. After that, the question the whole project has been backing into: **the pendulum.** Every network was trained by the same blind pressure — predict the next token — yet each settles into its own tempo for holding meaning. Something in the architecture, the data, or the optimisation sets ν. Find the pendulum, and you can predict a network's clock before ever measuring it.

A looping visualisation of the cross-family result — the wave leaving Pythia's cube, crossing to GPT-2's, and taking hold at its own tempo while the ghost of the old tempo dies to a faint trace — is [here](https://firstsignal.github.io/activation-geometry-sentiment/p1-transfer.html).

*Everything in this post was produced on free-tier Colab with subtraction, a projection, and a random direction as the control. The notebooks contain every experiment in the order it happened, including the eight kills.*
