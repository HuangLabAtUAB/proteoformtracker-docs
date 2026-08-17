# Relevant vs. confounding proteins

ProteoformTracker runs two genuinely different searches, matching a distinction from its design
spec:

- **Relevant proteoforms** (Section 1): the isoforms/proteoforms you explicitly checked — proteins
  that share exon structure or sequence with your target, by construction (you picked them from the
  same gene, or PTM-derived variants of the same sequence).
- **Confounding proteins** (Section 2): proteins that share **mass** and/or **m/z** with your
  target *regardless of any sequence or gene relationship* — found by searching the full reference
  human proteome, not by anything you selected.

Cross-gene proteins essentially never share extended sequence identity, so their fragment ions
diverge quickly at MS2 even when their intact mass happens to coincide at MS1 — which is exactly
why the confounder search is a real risk worth checking explicitly, not something you'd catch just
by comparing your relevant proteoforms against each other.

## Two ways a protein can confound your target

The confounder search unions two independent searches, since they catch genuinely different risks:

- **Mass-domain**: the candidate's own intact mass falls within your target's resolving-power
  window (see [Resolving power & FWHM](../concepts/resolving-power.md)) — a candidate found this way
  could still turn out resolvable once real charge-state peaks are checked.
- **m/z-domain**: the candidate's charge-state peaks land on top of your target's, **even when its
  intact mass is far away** — e.g. a 12 kDa and a 16 kDa protein can produce colliding peaks at a
  4:3 charge-state ratio. A mass-window-only search would never surface this.

Each candidate in the results table is tagged `found_via`: **mass**, **mz**, or **both**.

## Why the list is capped

An m/z-domain search alone can surface hundreds of real hits for some targets — every reference
protein whose charge envelope happens to touch the target's anywhere across its whole envelope.
ProteoformTracker caps the shown list (default: the 30 highest-priority), prioritized `both` >
`mass`-only > `mz`-only-sorted-by-collision-severity — never an arbitrary or first-found subset.
The status text reports how many were found in total vs. how many are shown.

## The MS2 shared-ions column

Before you decide which confounders are even worth the (isotope-computation-heavy) full
comparison, the candidate table shows a cheap **pairwise** MS2 overlap count per candidate: how
many of the target's own b/y ions collide with *that one candidate's* fragment ladder, considered
alone. This is deliberately **not** the same thing as the unique/partial/common tiers shown after
comparing — those are inherently set-dependent (a fragment only counts as "common" if *every*
confounder you leave checked shares it), so they can't be known until you've actually picked your
final comparison set. The pairwise number is there purely to help you prioritize which candidates
are worth including.

## Two-step workflow, by design

Section 2 is deliberately split into a cheap **search** step (candidate list, no isotope
computation) and an expensive **compare** step (only for whichever candidates you leave checked) —
see [Section 2: Confounding-protein search](../results/section-2-confounders.md) for the full
workflow, including why this split exists and how to use outside evidence (e.g. RNA-seq expression
calls) to narrow the list before running the heavy computation.
