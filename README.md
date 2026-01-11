# DRIP BigWig -> Gene Exon/Intron Percentile Table + Violin Plots

This repository contains `IntronvsExonDRIP.R`, an R script to aggregate BigWig coverage over gene exons and introns and generate percentile-based summaries and violin plots.

## Features

- Import BigWig coverage (per sample) and average replicates per group.
- Compute a chosen percentile (default 95th) of coverage across exons and introns per gene.
- Produce a gene-by-(group,region) table and optional violin plots of log10-transformed signals.
- Filter chromosomes (UCSC names) and apply minimum width and signal thresholds.

## Requirements

This script requires R (>= 4.0 recommended) and the following packages:

- optparse
- GenomicFeatures (Bioconductor)
- GenomicRanges (Bioconductor)
- GenomeInfoDb (Bioconductor)
- rtracklayer (Bioconductor)
- dplyr
- tidyr
- ggplot2

Install Bioconductor packages with BiocManager, for example:

```r
if (!requireNamespace("BiocManager", quietly = TRUE)) install.packages("BiocManager")
BiocManager::install(c("GenomicFeatures","GenomicRanges","GenomeInfoDb","rtracklayer"))
install.packages(c("optparse","dplyr","tidyr","ggplot2"))
```

## Usage

Prepare a CSV (`bw_files.csv`) with columns: `sample,group,bw`

Example:

```
sample,group,bw
rep1_ctrl,control,/path/to/rep1_ctrl.bw
rep2_ctrl,control,/path/to/rep2_ctrl.bw
rep1_treat,treatment,/path/to/rep1_treat.bw
```

Run:

```bash
Rscript organize_drip.R --gtf annotations.gtf --bw-csv bw_files.csv --out-prefix DRIP_analysis
```

Main options:

- `--gtf` : GTF/GFF annotation file (required)
- `--bw-csv` : CSV listing BigWig files (required)
- `--out-prefix` : Output filename prefix (default `DRIP`)
- `--percentile` : Percentile to use for aggregation (0-1, default `0.95`)
- `--min-width` : Minimum width (bp) for intron/exon to be considered (default `500`)
- `--min-signal` : Pseudocount/minimum signal for filtering/plots (default `1e-4`)
- `--chromosomes` : Comma-separated list of chromosomes to keep (UCSC style)
- `--fixed-y` : Fixed Y limits for plots (comma separated), e.g. `-1,2.5`
- `--no-plot` : Do not generate plots

## Outputs

- `<out-prefix>.coverage.<group>.rds` : averaged per-group coverage RleList
- `<out-prefix>.Int_Ex_table_q<percentile%>.rds` and `.csv` : gene table with exon/intron percentile values
- `<out-prefix>_Groups_ExonIntron_violin_log10_q<percentile%>.pdf` : violin plot per group
- `<out-prefix>_AllGroups_ExonIntron_violin_log10_q<percentile%>.pdf` : violin plot merging groups (if applicable)

## Notes

- The script expects UCSC-style chromosome names (e.g., `chr1`, `chrX`). Use the `--chromosomes` option to restrict which chromosomes to use.
- Ensure the paths to BigWig files are correct and accessible.
- The script uses a pseudocount (`--min-signal`) before log10 transformation to avoid -Inf.

## License

This project is provided under the MIT License (see LICENSE).
