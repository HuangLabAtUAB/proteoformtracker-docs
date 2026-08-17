# Supported PTMs (Unimod)

The [PTM spec text box](../input-methods/ptm-syntax.md) accepts any modification name from this
table (case-insensitive). This is a **curated subset** of Unimod, not the full database — pick
whichever of these covers your case; for anything else, the underlying `ptm()` R function accepts a
manual mass delta directly (not exposed in the UI spec syntax).

| Name | Unimod ID | Mono Δmass (Da) | Avg Δmass (Da) | Typical site |
|---|---|---:|---:|---|
| `Phospho` | UNIMOD:21 | 79.966331 | 79.9799 | Ser / Thr / Tyr |
| `Acetyl` | UNIMOD:1 | 42.010565 | 42.0367 | Protein N-term or Lys |
| `Methyl` | UNIMOD:34 | 14.015650 | 14.0266 | Lys / Arg |
| `Dimethyl` | UNIMOD:36 | 28.031300 | 28.0532 | Lys / Arg |
| `Trimethyl` | UNIMOD:37 | 42.046950 | 42.0797 | Lys |
| `Oxidation` | UNIMOD:35 | 15.994915 | 15.9994 | Met / Trp / Pro / Cys |
| `GG` | UNIMOD:121 | 114.042927 | 114.1026 | Lys (ubiquitin/SUMO remnant) |
| `Deamidated` | UNIMOD:7 | 0.984016 | 0.9848 | Asn / Gln |
| `Gln->pyro-Glu` | UNIMOD:28 | −17.026549 | −17.0305 | N-terminal Gln |
| `Glu->pyro-Glu` | UNIMOD:27 | −18.010565 | −18.0153 | N-terminal Glu |
| `Palmitoyl` | UNIMOD:47 | 238.229666 | 238.4136 | Cys |
| `Sulfo` | UNIMOD:40 | 79.956815 | 80.0642 | Tyr |
| `Carbamidomethyl` | UNIMOD:4 | 57.021464 | 57.0513 | Cys (common alkylation artifact) |
| `Formyl` | UNIMOD:122 | 27.994915 | 28.0101 | Protein N-term or Lys |
| `Nitro` | UNIMOD:354 | 44.985078 | 44.9976 | Tyr |
| `Amidated` | UNIMOD:2 | −0.984016 | −0.9848 | C-terminal amidation |

!!! note "Site is not validated against biology, only against sequence identity"
    ProteoformTracker checks that the residue you specify actually matches the amino acid at that
    position in that isoform's sequence (see [PTM syntax](../input-methods/ptm-syntax.md#what-happens-when-a-spec-is-invalid)) —
    it does **not** check whether that site is a biologically plausible target for the modification
    (e.g. nothing stops you from specifying `Palmitoyl` on a residue that isn't Cys, as long as you
    also correctly state that residue's real identity). You're responsible for specifying
    biologically sensible sites; the tool only guards against sequence mismatches.
