# PTM specification syntax

Every isoform row (in [Option 1](option-1-gene.md)) and the FASTA translation
(in [Option 2](option-2-fasta.md)) has a PTM spec text box. This page is the full grammar.

## Grammar

```
133_S_Phospho; 210_T_Phospho,215_Y_Sulfo
```

- **Semicolon (`;`)** separates independent proteoforms derived from this isoform — each group
  becomes its **own row** in the proteoform table, alongside the unmodified form (which is always
  included regardless of what you type here).
- **Comma (`,`)** combines multiple PTMs onto the *same* proteoform (one co-modified form), up to 5
  PTMs per group.
- Each single PTM is `<1-based residue position>_<amino acid code>_<Unimod name>`.

So the example above produces **two extra proteoform rows**: one with a single phospho at residue
133, and one with both a phospho at 210 *and* a sulfation at 215 together.

## Amino acid code

Single-letter codes (`S`, `T`, `Y`, ...) are the primary, documented form — they match the
single-letter sequence shown when you hover a transcript ID in the isoform catalog, so a residue you
read off there can be typed in directly with no translation step. Three-letter codes (`Ser`, `Thr`,
`Tyr`, ...) are also accepted, for anyone used to that convention.

## Supported modification names

See [Supported PTMs](../reference/supported-ptms.md) for the full table (name, Unimod ID, mass
deltas). Common ones: `Phospho`, `Acetyl`, `Methyl`, `Oxidation`, `GG` (ubiquitin/SUMO remnant).

## What happens when a spec is invalid

Invalid groups are **dropped with a human-readable warning**, shown above the isoform catalog —
they don't block the other, valid groups in the same text box, and they don't block the unmodified
form. Reasons a group gets dropped:

| Problem | Example warning |
|---|---|
| Wrong format (not exactly 3 `_`-separated parts) | `"133S_Phospho" does not match residue_AA_PTM format` |
| Residue position out of range | `residue 900 out of range (1-743)` |
| Unknown amino acid code | `unknown residue code "X"` |
| **The stated residue doesn't match the actual sequence** | `residue 211 is W in this isoform, not S` |
| Unknown PTM name | `unknown PTM "Phosphoo"` |
| More than 5 PTMs in one group | `6 PTMs exceeds the limit of 5` |

The sequence-mismatch case is the one worth double-checking carefully: **a modification site is
isoform-specific**, since different isoforms of the same gene often have shifted residue numbering
around alternatively-spliced exons. A spec copied from one isoform's numbering may point at a
completely different (or absent) residue in another. Always verify against *that specific row's*
hover-popover sequence before trusting a spec across isoforms.

## Worked example

For BCL2L1 transcript `ENST00000941693`, residue 14 is genuinely Serine. Typing:

```
14_S_Phospho
```

produces an additional proteoform row `ENST00000941693 + Phospho@14`, its mass exactly Unimod's
phospho delta (+79.966331 Da monoisotopic) above the unmodified form:

![Proteoform table showing the PTM row](../assets/screenshots/04-proteoform-table-run.png)

...and the fragment ladder marks exactly where it sits, with every b/y ion downstream of the site
correctly mass-shifted:

![PTM marker on the MS2 ladder](../assets/screenshots/06-section1-ms2-ladder.png)
