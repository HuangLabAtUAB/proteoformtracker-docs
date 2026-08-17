# Glossary

**Proteoform**
: The shared internal representation every input path resolves into before any scoring runs: a
mature amino-acid sequence (after N-terminal Met excision and signal-peptide/propeptide removal)
plus a list of applied PTMs.

**Top-down proteomics (TDP)**
: Mass spectrometry performed on intact proteins, without proteolytic digestion.

**Middle-down proteomics (MDP)**
: A limited (partial) protease digestion first, producing large peptides, analyzed the same way
top-down would analyze an intact protein. See [Middle-down mode](../middle-down/peptide-picker.md).

**Resolving power `R(m/z)`**
: An Orbitrap's mass-resolving capability, quoted at a reference m/z and decreasing as m/z
increases. See [Resolving power & FWHM](../concepts/resolving-power.md).

**ΔM_FWHM**
: The mass-domain peak width (full width at half maximum) implied by resolving power at a given
m/z — the practical yardstick for "how close is too close to resolve."

**Safety margin**
: A multiplier (default 1.75×) applied over ΔM_FWHM before calling a pair confidently — not just
theoretically — separable.

**Charge-state envelope**
: The distribution of charge states (and, within each, the isotope pattern) a proteoform is
predicted to produce. See [Charge-state envelopes](../concepts/charge-envelope.md).

**Averagine**
: A closed-form statistical approximation to a protein's isotope pattern, used instead of full
combinatorial isotopologue enumeration (intractable at intact-protein scale).

**Fragmentation propensity**
: A per-bond score estimating how likely a given backbone bond is to fragment under HCD/CID, built
from residue-pair, positional, length, and PTM-proximity effects — not an intensity prediction. See
[Fragmentation propensity & scoring modes](../concepts/fragmentation-propensity.md).

**b-ion / y-ion**
: The two terminal fragment-ion series modeled (N-terminal and C-terminal fragments,
respectively) — the only ion types modeled; internal fragments and ETD/ECD c/z ions are out of
scope for now.

**Relevant proteoform**
: A proteoform you explicitly checked for comparison (an isoform, a PTM variant, a novel sequence,
or an rMATS-matched transcript).

**Confounding protein**
: A protein from the reference proteome — unrelated to your target by gene or sequence — that
shares intact mass and/or a charge-state m/z peak with your target. See
[Relevant vs. confounding proteins](../concepts/relevant-vs-confounding.md).

**`found_via`**
: How a confounder candidate was found: `mass` (intact-mass window), `mz` (charge-state peak
collision), or `both`.

**Tier (unique / partial / common / neutral)**
: How many of the other proteoforms/confounders in the current comparison share a given exon block
or fragment ion. See [Tier colors](../concepts/tiers.md).

**Calibrated (GLM) scoring mode**
: The primary fragmentation-propensity scoring formula, length-aware and calibrated against real
matched b/y ions from published top-down datasets.

**RF ranking scoring mode**
: A length-free, random-forest-based relative ranking, for long proteoforms whose Calibrated-mode
scores are all suppressed by their own length. Not comparable in magnitude to Calibrated mode.

**PTM spec**
: The text-box syntax for specifying modifications on a proteoform, e.g. `133_S_Phospho`. See
[PTM specification syntax](../input-methods/ptm-syntax.md).

**rMATS**
: A widely-used tool for detecting differential alternative-splicing events (SE, MXE, A3SS, A5SS,
RI) from RNA-seq data. See [Option 3](../input-methods/option-3-rmats.md).

**SE / MXE**
: rMATS event types supported by Option 3 — Skipped-Exon and Mutually-eXclusive-Exons,
respectively.

**IsoPepTracker**
: ProteoformTracker's bottom-up companion project, at
[github.com/HuangLabAtUAB/IsoPepTracker](https://github.com/HuangLabAtUAB/IsoPepTracker).
