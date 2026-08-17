# Installation & setup

ProteoformTracker is an R Shiny app. It needs R, a project-local Python virtualenv (wrapped via
`reticulate` for mass calculation), and several precomputed reference-data files that are too large
to ship in the git repo.

## Requirements

- **R** ≥ 4.0
- **Python**, for the wrapped [`pyteomics`](https://pyteomics.readthedocs.io/) mass-calculation
  engine (a project-local virtualenv is created automatically — see below)
- **Homebrew CLI tools** (only needed for FASTA/rMATS input, not the core gene → isoform path):
  `samtools`, `minimap2`, `TransDecoder.LongOrfs` / `TransDecoder.Predict`
- **Bioconductor's `rtracklayer`** (only needed to *build* the exon index once; the running app
  doesn't import it directly)

## 1. Restore R packages

```r
renv::restore()
```

## 2. Create the Python environment

```r
Rscript scripts/setup_python_env.R
```

This creates a `proteoformtracker-py` virtualenv and installs `python/requirements.txt` (pinned
`pyteomics<5.0` — see that file for why).

## 3. Build the reference-proteome mass index

Confounding-protein search needs a precomputed table of theoretical intact masses for the full
human reviewed-canonical proteome (UniProt `UP000005640`). This also parses each entry's gene
symbol for display in the confounder table.

```r
Rscript scripts/build_reference_proteome.R
```

Downloads the proteome FASTA on first run (~15 MB), then computes ~20,000 masses — a couple of
minutes total. Produces `data/UP000005640_9606_reviewed_canonical.fasta` and
`data/reference_mass_index.rds`.

## 4. Build the m/z-domain collision index

```r
Rscript scripts/build_mz_collision_index.R
```

Pure R, no Python — roughly 10 seconds. This is what lets the confounder search catch proteins
whose charge-state *peaks* collide with your target even when their intact mass is far away (see
[Relevant vs. confounding proteins](../concepts/relevant-vs-confounding.md)).

## 5. Build the exon structure index

Needed for the gene → isoform pathway (Option 1) and the rMATS pathway (Option 3).

```r
BiocManager::install("rtracklayer")  # one-time, not in renv.lock
mkdir -p data/annotation
curl -o data/annotation/Homo_sapiens.GRCh38.116.gtf.gz \
  http://ftp.ensembl.org/pub/release-116/gtf/homo_sapiens/Homo_sapiens.GRCh38.116.gtf.gz
Rscript scripts/build_exon_index.R
```

The GTF is ~140 MB; this step takes a few minutes.

## 6. (Optional) FASTA / rMATS input support

Options 2 (pasted FASTA) and 3 (rMATS) additionally need a local genome FASTA + minimap2 index, and
the Homebrew CLI tools listed above:

```sh
brew install samtools minimap2
# TransDecoder: see https://github.com/TransDecoder/TransDecoder

mkdir -p reference/genome
curl -o reference/genome/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz \
  http://ftp.ensembl.org/pub/release-116/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz
gunzip -k reference/genome/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz
mv reference/genome/Homo_sapiens.GRCh38.dna.primary_assembly.fa reference/genome/GRCh38.fa
samtools faidx reference/genome/GRCh38.fa
minimap2 -d reference/genome/GRCh38.mmi reference/genome/GRCh38.fa  # large, ~12 GB, slow
```

If this index isn't built yet, the FASTA input tab still loads — it just reports that the
minimap2 alignment step isn't available yet rather than erroring.

## Run the tests

```sh
Rscript tests/testthat.R
```

## Run the app

```r
shiny::runApp(port = 7412, host = "127.0.0.1")
```

or simply `shiny::runApp()` for the default port. All reference-data paths above are relative to
the project root, so the whole folder can be moved or cloned anywhere without code changes.

!!! note "What's gitignored"
    `data/*` and `reference/` are not tracked in git — they're either downloaded fresh (steps 3–6
    above) or built lazily on first use (e.g. the middle-down digest pool, cached to disk the first
    time you pick a given protease).
