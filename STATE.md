# State — 13 July 2026

Ch5 complete & committed: wave survived axis-split (0.96); period
fractional (0.37 at 12/16/24 layers); chirp/parity/growth-law killed;
tense wave real (0.937), same period, r = 0.008 vs sentiment →
multiplexing: features share the clock, not the beat.

Next: ch6 = plurality phase-slot test (axes from ch2 singular/plural
sets; needs 9 matched single-token-flip test pairs; same probe as ch5;
run on 410m). Three outcomes all informative: third phase slot /
phase-groups / doesn't wave.

Then: writeup.md → embed figures, voice-pass findings → Alignment
Forum post ("Two features, one clock").

Parked: BOS-norm/gating test (Mix-Compress-Refine link), layer 13
autopsy (3 instruments point at it), 50-seed null band, cross-scale
phase offsets.

**State — 13 July 2026 (evening)**

Ch6 complete & committed: plurality wave real but weak (0.637, at
threshold); P2 killed-then-downgraded (3.98 cycles unstable under
split-half — Hilbert artifact on weak carrier); P3 killed clean.
The finding: per-pair phase offsets (control R=0.99) show three
features at three distinct locked angles on one clock — sentiment 0°,
plurality ~21° (R=0.97, n=9), tense ~89° = quadrature (R=1.00, n=5,
Rayleigh p<.001). Resolves ch5's r=0.008 as orthogonality, not
non-relation. Phase encodes feature identity, not class.
Two instrument failures preserved in-notebook (joint-mask starvation
1-6/24 layers; split-half scatter) — they motivate the per-pair method.

Figures: plurality_phase_slot_410m, per_pair_phase_410m (check .PNG
vs .png case in links).

Next: writeup.md — Finding 5 rewrite (schedules → phase-slots),
figures + voice-pass → Alignment Forum post. README Map-of-experiments
needs ch6 row + tagline check.

Parked: Watson-Williams test (plurality 21° vs control 0° — distinct
slot or noisy sentiment slot?); cross-scale phase check (quadrature at
160m/1B?); which 3 tense pairs abstained + why; subspace probe for
plurality amplitude; BOS-norm/gating test (Mix-Compress-Refine link);
layer 13 autopsy; 50-seed null band.

**State — 14 July 2026 (evening)**

Ch6 CLOSED & pushed: distinctness gate passed — Watson-Williams
p=0.0037, permutation p=0.0052; three distinct slots confirmed
(0°/21°/89°). Verdict reproduced 3× across VMs and two weights
formats. README updated. One curated fossil kept in-notebook
(stalled 7% download bar + caption) documenting the HF CDN incident;
weights fetched via curl → local disk that day.

Ch7 OPEN (ch7-one-clock-many-models.ipynb), session 1 done:
- Equation stated & rescoped by data:
  w_{f,m}(t) = A_{f,m}(t)·cos(2πν_m·t + φ_{f,m})
  Form universal; constants per-model ("local bubble").
- GPT-2 small (12L, CPU fp32, curl/local-disk load):
  P1 SURVIVED — sentiment wave alignment 0.877 (> Pythia's 0.80);
  the wave is cross-family.
  P2 KILLED (the finding) — 1.60 cycles vs ~2.7; self-control
  R=0.97 so coherent, just slower. ν is per-network:
  family-specific clock, universal clock-structure.
  P3 GATED — tense/plurality per-pair R = 0.17/0.11, below locked
  0.7 gate; printed p=0.014 disregarded per gate. Unresolved, not
  absent. Suspects in order: (1) instrument starvation (1.6 cycles
  / 12 layers), (2) Pythia-tuned axes (grammar waves absent:
  alignments -0.03/0.16, while sentiment thrives → suspect 2
  gaining), (3) GPT-2 doesn't slot grammar (only after 1&2 die).
- phaseprobe package (PhaseProbe class) built in ch7 cell 1.
- Figure: phase_slots_gpt2.PNG (check saved + pushed).
- README: ch7 section + row 7 added, "Chapter open"; kill tally → 8.

Next session, in order:
1. Overlay figure — Pythia vs GPT-2 sentiment waves, one fractional
   axis ("two breathers, one ruler"); hard-code ch6's w_s values
   with citation comment rather than reloading 410m.
2. GPT-2-native tense/plurality axis sets — PRE-REGISTER before
   running (tests suspect 2).
3. GPT-2-medium (24L) — tests suspect 1. Pre-register fresh.
4. THE WRITEUP (writeup.md): title → "Three features, one clock:
   phase encodes feature identity in Pythia"; Finding 5 rewritten
   (done); TL;DR + Related-Work delta + byline fix pending; ch7
   cross-family result = new closing section. Ships before ch8.

Parked: cross-family ν survey; ν-as-fingerprint hypothesis (same
ν + slots ⇒ shared lineage? — hypothesis only); cross-scale phase
check (160m/1B); abstaining tense pairs; subspace probe; BOS-norm/
gating (Mix-Compress-Refine); layer 13 autopsy; 50-seed null band.
Noted-not-developed: "Microsoft clock crash" association — no
mechanistic bridge; keep out of writing.

Infra note: HF XET/CDN large-file stalls all day 14 Jul; curl of
safetensors or .bin direct usually works; verify by byte count,
never the progress bar. One runtime at a time on free tier;
GPU quota now metered — GPT-2-small runs fine on CPU fp32 (~30min).

