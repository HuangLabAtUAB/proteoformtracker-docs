# Charge-state envelopes

Every MS1 chart in ProteoformTracker (the "MS1 charge-envelope overlay" in both Section 1 and
Section 2) plots a *predicted* charge-state envelope for each proteoform — not a real acquired
spectrum, since ProteoformTracker never touches raw instrument data. This page covers what's being
predicted and how to read the chart.

## Charge-state ceiling

Conditioned on the **ionization mode** you pick in
[MS resolution parameters](../settings/ms-resolution.md):

- **Denatured**, and roughly ≤ 40 kDa: the maximum charge state tracks the protein's own
  basic-residue count (Arg/Lys/His), since denaturing conditions let the charge ladder climb close
  to that ceiling.
- **Above ~40 kDa, or native mode generally**: a Rayleigh-limit relationship tied to
  surface-area/mass takes over instead — native conditions in particular suppress charging well
  below the basic-residue ceiling.

## Isotope pattern: resolved vs. unresolved

For each charge state, ProteoformTracker predicts the real isotope envelope shape (an
[Averagine](https://doi.org/10.1177/1469066794006) closed-form approximation, not full
combinatorial isotopologue enumeration — intractable at intact-protein scale) and then checks it
against the instrument's own resolving power at that charge state's m/z
(see [Resolving power & FWHM](resolving-power.md)):

- **Resolved** (isotope spacing wider than the instrument's FWHM): drawn as a real discrete comb —
  one stick per actual isotope peak.
- **Unresolved** (isotope spacing narrower than the FWHM — the common case above ~25–30 kDa, where
  the whole isotope envelope blurs into one hump): drawn as the smooth envelope curve those
  unresolved peaks would actually blur into.

Zoom into a single charge state's own narrow m/z window (scroll/pinch, or the +/− buttons) to see
which regime you're in — the default zoomed-all-the-way-out view can't show individual isotope
ticks even for well-resolved small proteins, since the full envelope spans hundreds to thousands of
m/z.

## Relative intensity, not absolute signal

The y-axis is **normalized relative intensity**, not a real absolute-intensity prediction — total
ion signal divides across more charge states and a wider isotope envelope as mass increases, and
ProteoformTracker models that redistribution, but absolute signal ultimately depends on sample
loading and instrument sensitivity the tool has no way to know.

## Hovering a peak

Once zoomed in enough (the hint text tells you when you haven't), hovering a resolved peak or an
unresolved curve sample shows:

- Its own m/z, charge state, and relative intensity
- The nearest *meaningful* peak (≥1% relative intensity) belonging to a **different** proteoform in
  the same chart, and how many multiples of the instrument's FWHM apart they are — the same
  resolved/partially-separated/same-peak verdict language used everywhere else in the app

## Where charge envelopes feed into scoring

- **MS1 stats tiles** (clean/overlapping/total peaks): counts based on this same peak-collision
  check, run pairwise across every proteoform in the chart.
- **Confounder search**: a candidate protein's own predicted envelope is what gets checked against
  your target's for the m/z-domain half of the search — see
  [Relevant vs. confounding proteins](relevant-vs-confounding.md).
