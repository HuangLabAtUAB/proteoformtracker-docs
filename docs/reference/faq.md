# FAQ & troubleshooting

## "Run analysis" / "Load isoforms" is greyed out

Both the [MS strategy](../settings/ms-strategy.md) and
[MS resolution parameters](../settings/ms-resolution.md) panels need their own **Set** button
clicked at least once — every entry-point button (Load isoforms, Run sequence analysis, Find
matching transcripts, Run analysis) stays disabled until both are set. This isn't a bug: it's a
deliberate confirmation step for settings everything downstream depends on. Changing a value in
either panel after clicking Set re-locks it, requiring a fresh click.

For "Run analysis" specifically, you also need: at least one proteoform checked/selected, and a
confounder-search target picked in the dropdown.

## The confounder search only found a few results — is that right?

It depends entirely on `found_via`. A target with few real mass-domain neighbors and few m/z
collisions genuinely can have a short candidate list — that's a real result, not a bug. Check the
status line's "N by mass window, N by m/z collision, N by both" breakdown; see
[Relevant vs. confounding proteins](../concepts/relevant-vs-confounding.md).

## My confounder list is capped at 30 — how do I see more?

By design, for performance — see
[why the list is capped](../concepts/relevant-vs-confounding.md#why-the-list-is-capped). The status
line reports the true total found. There's currently no way to raise the cap from the UI.

## The MS2 ladder shows nothing for a bond I clicked

The fragment-isotope inspector only computes for bonds whose propensity score clears the current
scoring mode's isotope gate (Calibrated: score > 0.10; RF: score > 0.08). If you click a bond below
that gate, the label explains this rather than showing an empty chart — try the **Elevated** filter
level to only click bonds that will actually qualify.

## GLM ("Calibrated") mode shows almost nothing above the "Elevated" filter for my protein

This is the expected behavior the RF mode exists to fix — a single very long proteoform's own
length systematically suppresses its calibrated score. Switch **Fragmentation scoring mode** to
**RF ranking** in [MS resolution parameters](../settings/ms-resolution.md); see
[Fragmentation propensity & scoring modes](../concepts/fragmentation-propensity.md) for why the two
modes aren't on the same numeric scale.

## Reset doesn't seem to clear everything

If you hit this on an older build: newer versions explicitly tear down chart interaction listeners
and re-collapse both result sections on Reset, so nothing from a previous analysis can reappear
(e.g. via a stray scroll over a chart area). If you still see stale content after Reset on a current
build, that's worth reporting as a real bug.

## FASTA / rMATS input tabs say alignment isn't available

These need a local GRCh38 genome FASTA + minimap2 index, which isn't required for
[Option 1](../input-methods/option-1-gene.md) and is gitignored (not shipped with the repo). See
[Installation & setup](../getting-started/installation.md#6-optional-fasta-rmats-input-support).

## Where do I report a real bug?

Open an issue on the [app repository](https://github.com/HuangLabAtUAB/ProteoformTracker).
