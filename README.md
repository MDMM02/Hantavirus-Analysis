# Hantavirus Molecular Dataset Builder

Goal: build a clean molecular dataset for the project question:

> How do S, M and L segment sequences differ across major Orthohantavirus species, and can sequence-level features support molecular classification and epidemiological interpretation?

This project downloads real viral sequence packages from NCBI Datasets, then builds clean CSV/FASTA files with sequence-level metadata and QC flags.

## Target taxa v1

The v1 dataset targets five major Orthohantavirus species:

| Short name | Scientific name | NCBI taxid | Main angle |
|---|---:|---:|---|
| puumala | Orthohantavirus puumalaense | 3052493 | Europe / HFRS-nephropathia epidemica |
| hantaan | Orthohantavirus hantanense | 3052480 | Asia / HFRS |
| seoul | Orthohantavirus seoulense | 3052498 | urban/rat-associated / HFRS |
| sinnombre | Orthohantavirus sinnombreense | 3052499 | North America / HPS-HCPS |
| andes | Orthohantavirus andesense | 1980456 | South America / HPS-HCPS |

Edit `config/target_taxa.csv` to add more species later.

## 1. Install Python dependencies

With pip:

```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Or with conda:

```bash
conda env create -f environment.yml
conda activate hantavirus-molecular
```

## 2. Install NCBI Datasets CLI

Windows PowerShell:

```powershell
.\scripts\00_install_ncbi_cli_windows.ps1
```

Linux/macOS:

```bash
bash scripts/00_install_ncbi_cli_linux_mac.sh
```

NCBI also supports conda installation:

```bash
conda install -c conda-forge ncbi-datasets-cli
```

## 3. Download NCBI virus packages

Windows PowerShell:

```powershell
.\scripts\01_download_ncbi_by_taxon.ps1
```

Linux/macOS:

```bash
bash scripts/01_download_ncbi_by_taxon.sh
```

The zip files will be stored in:

```text
data/raw/ncbi/
```

## 4. Unpack packages

```bash
python scripts/02_unpack_ncbi_packages.py
```

## 5. Build the master table

```bash
python scripts/03_build_sequence_table.py
```

Outputs:

```text
data/interim/orthohantavirus_sequences_master.csv
data/interim/orthohantavirus_sequences_master.fasta
```

Main columns:

| Column | Meaning |
|---|---|
| accession | NCBI sequence accession |
| species | species name from NCBI metadata when available |
| segment | inferred segment: S, M, or L |
| length | sequence length in nucleotides |
| gc_content | GC fraction |
| n_count | number of N bases |
| ambiguous_rate | N + non-ACGT/U fraction |
| seq_sha256 | exact sequence hash for deduplication |
| length_qc_flag | expected length QC flag for S/M/L |
| host, country, collection_date | metadata when available |

## 6. Clean and filter

```bash
python scripts/04_filter_clean_dataset.py
```

Outputs:

```text
data/processed/orthohantavirus_sequences_clean_all.csv
data/processed/orthohantavirus_sequences_clean_strict.csv
data/processed/qc_summary.csv
```

Use `clean_all` for exploratory analyses. Use `clean_strict` when you need conservative segment-length filtering.

## 7. Build k-mer features

```bash
python scripts/05_build_kmer_features.py
```

Outputs:

```text
data/processed/kmer_features_k3.csv
data/processed/kmer_features_k4.csv
```

These can be used for PCA, clustering, or species/segment classification.

## 8. Quick EDA figures

```bash
python scripts/06_quick_eda.py
```

Outputs:

```text
reports/figures/
```

## Dataset policy

Do not mix this real NCBI molecular dataset with the Kaggle detection dataset as if they came from the same patients. Keep them separate:

- NCBI/GenBank = molecular sequence analysis;
- Kaggle detection dataset = ML/data leakage audit/pedagogical clinical dataset;
- epidemiology dataset = public health context.

## Notes

NCBI Datasets virus packages usually contain `genomic.fna` and `data_report.jsonl` by default, with optional CDS, protein, annotation, and BioSample files when requested. The scripts here parse the genomic FASTA and metadata report defensively because metadata completeness varies between records.
