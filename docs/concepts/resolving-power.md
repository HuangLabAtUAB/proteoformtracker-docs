# Resolving power & FWHM

Every resolvability verdict in ProteoformTracker — whether two charge-state peaks are "clean" or
"overlapping" in the MS1 stats panel, whether a fragment ion is distinguishable, whether a
confounding protein's peak collides with your target's — comes back to the same instrument model.

## The Orbitrap resolving-power law

Resolving power is quoted at one reference m/z (conventionally 200) and degrades as m/z increases:

$$
R(m/z) = R_{ref} \times \sqrt{\frac{m/z_{ref}}{m/z}}
$$

This converts to a mass-domain peak width (full width at half maximum):

$$
\Delta M_{FWHM} \text{ (Da)} \approx \frac{M}{R(m/z_{\text{best}})}
$$

where `M` is the ion's mass and `m/z_best` is whichever charge state in its envelope gives the
smallest (best) ΔM_FWHM.

### Worked example

A 16 kDa proteoform at charge state z=20 has m/z ≈ (16,000 + 20 × 1.007) / 20 ≈ 801. At
`R_ref = 120,000` and `m/z_ref = 200`:

```
R(801) = 120,000 × sqrt(200 / 801) ≈ 60,000
ΔM_FWHM ≈ 16,000 / 60,000 ≈ 0.27 Da
```

So at that charge state, two proteoforms of this protein would need to differ by more than roughly
0.27 Da (times the safety margin, below) to show up as two separate peaks rather than one blurred
one.

## Safety margin

Being *theoretically* resolvable and being *confidently, practically* separable in a real spectrum
aren't quite the same thing. ProteoformTracker applies a safety margin (default **1.75×** ΔM_FWHM,
adjustable in [MS resolution parameters](../settings/ms-resolution.md)) before calling a pair
"clean" rather than merely "borderline" — the design spec's own recommendation is a 1.5–2× margin.

## Where this shows up in the UI

- **MS1 charge-envelope peaks** stats tile (Section 1 and Section 2): counts how many of the
  checked/target proteoform's charge-state peaks are "clean" (no other proteoform/confounder within
  the safety margin) vs. "overlapping."
- **Hovering an MS1 peak**: the tooltip reports the nearest other peak's distance in units of the
  instrument's own FWHM at that m/z — under 1× reads as "effectively the same detected peak," a few
  × as "partially separated," many × as "clearly resolved."
- **Confounder search**: a charge-state peak from a candidate protein counts as *colliding* with
  your target's peak using this exact same tolerance.

## What this model does *not* capture

This is an Orbitrap-specific scaling law — FT-ICR and other platforms have a different
resolving-power relationship and would need a different formula. It's also a peak-*width* model,
not a full simulation of isotope fine structure; see
[Charge-state envelopes](charge-envelope.md) for how the actual isotope pattern (or its smooth
envelope, when unresolved) is predicted.
