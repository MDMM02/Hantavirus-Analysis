
# Notebook 01 — Molecular Dataset Audit Summary

## Project question

How do S, M and L segment sequences differ across major Orthohantavirus species, and can sequence-level features support molecular classification and epidemiological interpretation?

## Dataset versions

- clean_all: 6,747 sequences
- strict: 3,165 sequences
- analysis_ready: 3,109 sequences
- complete_reference: 720 sequences

## Main conclusions

The strict dataset is biologically plausible for first-pass molecular analysis.

The three expected Orthohantavirus genome segments, S, M and L, are represented in the dataset.

Sequence length distributions are coherent with the expected organization of a segmented hantavirus genome: S is the shortest segment, M is intermediate, and L is the longest.

GC content differs between segments, suggesting that nucleotide composition may provide useful molecular information.

Most sequences have a low ambiguous base rate, allowing the creation of an analysis-ready dataset for k-mer, PCA and clustering analyses.

A smaller complete-reference dataset was also created for more conservative phylogenetic analyses.

## Main limitations

The dataset is not perfectly balanced between species and segments.

Some sequences are partial.

Host metadata are limited or incomplete.

Country and collection date metadata are useful for descriptive interpretation, but they should not be treated as direct epidemiological incidence data.

## Recommended next step

Use `data/processed/orthohantavirus_sequences_analysis_ready.csv` for Notebook 02 on nucleotide composition, k-mers, PCA and molecular clustering.

Use `data/processed/orthohantavirus_sequences_complete_reference.csv` later for conservative phylogenetic analyses.
