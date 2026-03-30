# Replication Package: [paper_id]

This folder contains all code and data needed to replicate the analysis.

## Requirements

### Software
- R 4.2+ (for econometric analysis)
- Python 3.10+ (for data processing)

### R Packages
```r
install.packages(c(
  "fixest",       # Fast fixed effects
  "did",          # Callaway-Sant'Anna estimator
  "data.table",   # Fast data manipulation
  "ggplot2",      # Visualization
  "modelsummary", # Tables
  "tidyverse"     # Data wrangling
))
```

### Python Packages
```bash
pip install pandas numpy requests
```

## Data

**Sources:**
- [Data source 1]: [Description, URL]
- [Data source 2]: [Description, URL]

**Cached data files:**
- `data/raw/[filename]` — [Description]
- `data/processed/[filename]` — [Description]

## Code

**Scripts (run in order):**

| Order | Script | Description |
|-------|--------|-------------|
| 1 | `code/01_fetch_data.R` | Download raw data from APIs |
| 2 | `code/02_clean_data.R` | Process and clean data |
| 3 | `code/03_analysis.R` | Run main regressions |
| 4 | `code/04_robustness.R` | Robustness checks |
| 5 | `code/05_figures.R` | Generate figures |
| 6 | `code/06_tables.R` | Generate tables |

## Replication Instructions

### Quick Replication (using cached data)

```bash
# Clone repository
git clone [repo_url]
cd [paper_id]

# Run analysis (assumes data already cached)
Rscript code/03_analysis.R
Rscript code/04_robustness.R
Rscript code/05_figures.R
Rscript code/06_tables.R
```

### Full Replication (from scratch)

```bash
# 1. Fetch raw data (may require API keys)
Rscript code/01_fetch_data.R

# 2. Clean and process data
Rscript code/02_clean_data.R

# 3. Run analysis
Rscript code/03_analysis.R

# 4. Robustness checks
Rscript code/04_robustness.R

# 5. Generate outputs
Rscript code/05_figures.R
Rscript code/06_tables.R
```

## Output Files

| File | Description |
|------|-------------|
| `output/figures/fig1_event_study.pdf` | Main event study |
| `output/figures/fig2_*.pdf` | Additional figures |
| `output/tables/tab1_main.tex` | Main results table |
| `output/tables/tab2_*.tex` | Additional tables |

## Data Sources

| Dataset | Provider | Access | Documentation |
|---------|----------|--------|---------------|
| [Name] | [Provider] | [URL] | [Doc URL] |

## Notes

- Scripts may contain hardcoded paths that need adjustment for your system
- Cached data files allow replication without re-downloading
- For full replication from scratch, delete cached files and re-run fetch scripts
- Random seeds are set for reproducibility

## Verification

After running all scripts, compare outputs to:
- `paper.pdf` figures and tables
- `output/verification/checksums.txt`

---

*Generated automatically. Last updated: [DATE]*
