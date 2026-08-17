# ProteoformTracker

![ProteoformTracker](assets/screenshots/00-logo-header.png)

**ProteoformTracker** is an R Shiny planning tool for isoform/proteoform-level detectability in
**top-down** and **middle-down** proteomics (TDP/MDP). It answers one specific, pairwise question:

> Given two specific proteoforms, can this instrument actually tell them apart?

It is deliberately **not** an absolute mass-cutoff calculator. A naive "is this protein big enough
to detect" check misses two things this tool handles explicitly:

1. **The Δmass needed for resolvability isn't a fixed number.** It scales with a pair's own mass
   and charge state, via the instrument's own resolving-power model. A fixed rule like "Δmass ≥ 3
   Da is enough" produces false negatives on large, easily-separable pairs and false positives on
   small, marginal pairs.
2. **A pairwise-resolvable pair can still be confused by an unrelated third protein** — from a
   completely different gene — whose intact mass or charge-state peaks happen to land in the same
   window. This is why ProteoformTracker treats the MS2 fragment ladder as a first-class,
   independent axis of discrimination, not an optional extra.

ProteoformTracker is a companion project to
[IsoPepTracker](https://github.com/HuangLabAtUAB/IsoPepTracker) (its bottom-up counterpart).

## What it does

- Takes a proteoform from one of three input paths — a gene's annotated isoforms, a pasted/uploaded
  FASTA sequence, or an rMATS alternative-splicing event — and resolves it into a shared internal
  representation (sequence + PTMs).
- Predicts each proteoform's **MS1 charge-state envelope** (with real isotope patterns where the
  instrument would resolve them) and scores whether it's cleanly separable from every other checked
  proteoform, using an Orbitrap resolving-power model.
- Generates each proteoform's **MS2 b/y fragment ladder**, scores each bond's fragmentation
  propensity, and classifies every fragment as unique / partial / common relative to the other
  checked proteoforms — so you can see exactly which peaks would actually distinguish two isoforms.
- Searches the full reference human proteome for **confounding proteins** — unrelated proteins that
  could share your target's intact mass or a charge-state m/z peak — and lets you compare against
  a user-curated subset of them.
- Supports **middle-down** workflows (simulated partial protease digestion) as well as intact
  top-down analysis.

## Where to start

- New here? Start with [Installation & setup](getting-started/installation.md), then the
  [5-minute quick start](getting-started/quick-start.md).
- Want the concepts first? See [What ProteoformTracker answers](concepts/overview.md).
- Already running the app? Jump straight to whichever [input method](input-methods/option-1-gene.md)
  or [results page](results/section-1-relevant.md) you need.

## Source

- App repository: [github.com/HuangLabAtUAB/ProteoformTracker](https://github.com/HuangLabAtUAB/ProteoformTracker)
- Design spec (`ptracker_vibe.md` in the app repo) has the full scoring-model rationale and known
  limitations, if you want more depth than this guide covers.
