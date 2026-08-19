# Rationale and technical details of ProteoformTracker's design

In TDP or MDP, the question "Given two (or more) specific proteoforms, can this mass spectrometer actually tell them apart?" might sound straightforward. However, it is more complex than relying on a single delta mass cutoff. Three things a naive
fixed-threshold check misses, and that this tool handles explicitly instead:

## 1. The mass difference needed for resolvability isn't fixed

It scales with the pair's own mass and charge state, via the instrument's resolving power at
whatever m/z the ions actually land at:

$$
R(m/z) = R_{ref} \times \sqrt{\frac{m/z_{ref}}{m/z}}
\qquad\qquad
\Delta M_{FWHM} \approx \frac{M}{R(m/z_{best})}
$$

A rule like "Δmass ≥ 3 Da is enough" produces **false positives** on large pairs (a Δmass that
clears the fixed threshold can still be small relative to the *actual* peak width at high m/z,
where resolving power is worse — the rule calls them separable when they aren't) and
**false negatives** on small, marginal pairs (a Δmass under the threshold can already be cleanly
resolved at low m/z, where resolving power is far better — the rule calls them unresolvable when
they're actually fine). See [Resolving power & FWHM](resolving-power.md) for the full model and a
worked example.

## 2. A resolvable pair can still be confounded by a third, unrelated protein

Even if your two proteoforms of interest are cleanly separable from *each other*, some completely
unrelated protein — from a different gene entirely — might share one of their intact masses, or
land a charge-state peak on top of one of theirs. ProteoformTracker searches the full reference
proteome for this explicitly (see [Relevant vs. confounding proteins](relevant-vs-confounding.md)),
rather than treating the pair as isolated from the rest of the proteome.

This is also the reasoning behind treating the **MS2 fragment ladder** as a first-class,
independent axis of discrimination rather than an optional extra: two proteins from different genes
essentially never share extended sequence identity, so their fragment ions diverge quickly even
when their intact masses happen to coincide. See
[Fragmentation propensity & scoring modes](fragmentation-propensity.md).

## 3. Getting there requires real structural and physical modeling, not just arithmetic

None of the above is a lookup or a plain mass subtraction — each part rests on its own nontrivial
model:

- For a proteoform derived from a **novel transcript** (an rMATS event, or a pasted/uploaded FASTA
  sequence that isn't yet a named, annotated isoform), even knowing *where it structurally differs*
  from the canonical or other known isoforms has to be inferred — differential exons are found by
  matching against annotated transcript structure (see
  [Option 2: FASTA sequence](../input-methods/option-2-fasta.md) and
  [Option 3: rMATS splicing events](../input-methods/option-3-rmats.md)), not assumed from the input
  alone. Only once that's resolved can ProteoformTracker say which MS2 fragment ions are actually
  **proteoform-unique** rather than shared with the canonical sequence (see
  [Tier colors](tiers.md)).
- Predicting whether two proteoforms would actually show up as separate MS1 peaks means modeling
  the **charge-state envelope** each one would realistically produce under the chosen ionization
  mode, and the **isotope distribution** within each charge state — resolved into discrete peaks or
  blurred into one smooth envelope, depending on the instrument (see
  [Charge-state envelopes](charge-envelope.md)).
- Distinguishing two proteoforms at MS2 means scoring each backbone bond's real
  **fragmentation propensity** — not just enumerating every theoretical fragment mass, since
  different bonds fragment at genuinely different rates depending on sequence context (see
  [Fragmentation propensity & scoring modes](fragmentation-propensity.md)).

## How the pieces fit together

```mermaid
flowchart LR
    A[Input: gene/isoform, FASTA sequence, or rMATS event] --> B["Proteoform\n(sequence + PTMs)"]
    B --> C[MS1: charge-envelope\n+ resolving-power scoring]
    B --> D[MS2: fragment ladder\n+ propensity scoring]
    C --> E[Section 1:\nrelevant-proteoform comparison]
    D --> E
    B --> F[Confounder search\nmass + m/z domain]
    F --> G[Section 2:\nconfounding-protein search]
```

Every input path (gene/isoform selection, pasted FASTA, or an rMATS splicing event) resolves into
the same internal `Proteoform` representation — a mature amino-acid sequence plus a list of applied
PTMs — before any scoring runs. Everything downstream is identical regardless of which path
produced it.

## Known limitations

Stated here plainly, not hidden in a footnote — ProteoformTracker's own design spec is explicit
about these:

- The envelope-crowding check is a closed-form heuristic, not a guarantee of real
  deconvolution-algorithm (e.g. UniDec) behavior.
- The fragmentation propensity model predicts cleavage *likelihood*, not observed ion intensity —
  no mature, generalizable intensity-prediction model exists yet for intact-protein fragmentation.
- PTMs are propagated onto the proteoforms you explicitly specify, never onto background
  confounding proteins — PTM occupancy is sample/condition-specific biology, not a fixed proteome
  property, so there's no canonical "modified state" of an unrelated protein to precompute.
- Only terminal (b/y) fragment ions are modeled; internal fragments (two simultaneous cleavages)
  are out of scope — they're combinatorially far more numerous and individually much less probable
  than terminal ions.
- The Orbitrap `R(m/z)` scaling law is instrument-family specific; FT-ICR or other platforms would
  need a different resolving-power relationship.
