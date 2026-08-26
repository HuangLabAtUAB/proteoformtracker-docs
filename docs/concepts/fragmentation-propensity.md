# Fragmentation propensity & scoring modes

No trained intensity-prediction model (in the Prosit/MS2PIP sense) exists for intact-protein
fragmentation. ProteoformTracker instead uses a literature-grounded, rule-based **propensity
score** per backbone bond, calibrated against real matched b/y ions from published top-down
datasets. Every b/y tick on the MS2 fragment ladder is colored by tier and shaded by this score.

## What feeds into the score

Several effects combine into a per-bond propensity score:

- **Residue-pair effects**: cleavage N-terminal to proline and C-terminal to aspartate is strongly
  enhanced under collision-based fragmentation (HCD/CID).
- **Positional (terminal-vs-internal) bias**: terminal fragment ions statistically carry the
  majority of fragment-ion current for the first few backbone cleavages from either end, regardless
  of protein size — cleavages near the termini are weighted above deep-internal ones.
- **Protein length**: a real, calibrated multiplier — without it, a single very long proteoform's
  own length systematically suppresses every one of its bonds relative to shorter proteoforms,
  purely as an artifact of scale, not biology.
- **Phospho-proximity**: a calibrated multiplier for bonds near a phosphorylation site, fit
  directly from real proteoforms carrying PTMs in the validation data.

## Two scoring modes

Pick between them in [MS resolution parameters](../settings/ms-resolution.md) — they are **not on
the same numeric scale** and must never be compared directly against each other's thresholds.

### Calibrated (length-aware) — the default

The primary, auditable formula: a genuine 0-1 probability, with real fold-enrichment thresholds fit
against matched b/y ions from two independent public top-down datasets, including the length
multiplier. Tiers and their fold-enrichment over baseline:

| Tier | Score threshold | Fold enrichment |
|---|---|---|
| Elevated | > 0.10 | ~3.1× |
| High | ≥ 0.20 | ~4.7× |

The tradeoff: because length is baked in as a suppressive multiplier, a single very long
proteoform can have **every** bond fall below even the "Elevated" threshold, even though some
bonds are still meaningfully more likely to fragment than others *within that same protein*.

### RF ranking (no length) — for long proteoforms

A random-forest model trained without a length term, so it stays useful for long proteoforms whose
absolute calibrated scores are all suppressed. This is a **relative ranking within one proteoform**,
not a calibrated absolute probability, and its numbers are not comparable in magnitude to
Calibrated mode's. Its own tier thresholds:

| Tier | Score threshold | Fold enrichment |
|---|---|---|
| Elevated | > 0.08 | ~2.5× |
| High | ≥ 0.15 | ~3.5× |

Switching modes re-tiers every bond and re-colors the whole ladder; it also changes which bonds
qualify for on-demand isotope-pattern inspection (RF mode's qualifying gate is score > 0.08 vs.
Calibrated mode's score > 0.10).

## Reading the ladder

Tick **opacity** encodes the raw propensity score (within whichever filter level is active); tick
**color** encodes the unique/partial/common tier from comparing against the other checked
proteoforms or confounders — see [Tier colors](tiers.md) for that separate axis. The
[fragment filter buttons](../results/chart-interactions.md#fragment-filter) let you show only
bonds clearing a given tier, useful for finding which proteoform-distinguishing fragments actually
matter.

## What this model does not claim

This predicts cleavage **likelihood**, not observed ion **intensity** — there is no mature,
generalizable intensity-prediction model for intact-protein fragmentation to build on. Treat tier
labels as "worth checking whether this peak is informative," not as a guarantee any specific ion
will actually appear in a real spectrum at a given signal level.
