# Tier colors: unique / partial / common

The same three-color scheme (plus a fourth, "neutral") appears in three different places in the
app: exon blocks, MS2 fragment-ion ticks, and confounder-search fragment tiers. It always means the
same thing: **how many of the OTHER proteoforms currently being compared share this same element.**

| Color | Tier | Meaning |
|---|---|---|
| 🟦 Blue `#2a78d6` | **Unique** | No other checked proteoform/confounder has this element — informative on its own. |
| 🟪 Purple `#8952e0` | **Partial** | Shared with *some* but not *all* of the others. |
| 🟨 Gold `#eda100` | **Common** | Shared with *every* other checked proteoform/confounder — not distinguishing. |
| ⬜ Gray `#8a8a86` | **Neutral** | Nothing to compare against (only one proteoform checked). |

## Exon blocks

In the MS2 fragment-ladder track, each proteoform's exon structure is drawn as a row of colored
blocks. An exon block is:

- **Unique** if no other checked proteoform has that exact exon
- **Common** if every other checked proteoform has it too
- **Partial** otherwise

This is a real genomic-coordinate comparison (exact exon boundary match), not an axis-position
heuristic.

## MS2 fragment-ion ticks (b/y)

Each backbone bond's b-ion and y-ion tick is tiered by comparing its fragment **mass** against
every other checked proteoform's (Section 1) or every currently-selected confounder's (Section 2)
own fragment ladder, using the same resolving-power-derived mass tolerance used everywhere else in
the app (see [Resolving power & FWHM](resolving-power.md)) — not a fixed Da cutoff.

- **Unique**: this fragment mass matches nothing in any other ladder — if observed, it alone would
  distinguish this proteoform.
- **Common**: every other ladder in the comparison also produces a fragment at this mass — this
  bond confirms nothing about which proteoform you're looking at.
- **Partial**: matched by some, not all.

The per-proteoform **unique/partial/common counts** shown next to each row's title in the ladder
are a live tally over only the bonds currently passing the
[fragment filter](../results/chart-interactions.md#fragment-filter) — they update immediately when
you change the filter, so "how many informative fragments survive at the High-propensity
threshold" is always a real, current number, not a static one computed once at analysis time.

## Confounder-search tiers (Section 2)

Section 2 tiers the **target's own** ladder against whichever confounders you leave checked after
[selecting which to compare](../results/section-2-confounders.md) — so deselecting a confounder can
promote some of the target's fragments from "common"/"partial" back up to "unique," since the tier
is always relative to the *current* comparison set, not a fixed property of the fragment itself.
