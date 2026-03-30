---
name: data-analysis
description: End-to-end econometric analysis with R. Use when user provides a dataset, asks to "analyze data", "run regressions", "produce tables/figures", or requests empirical analysis.
argument-hint: "[dataset or data description]"
---

# Data Analysis Skill

## Overview

Perform end-to-end econometric analysis producing publication-ready output.

## Steps

### Phase 1: Data Exploration
1. Load and inspect the dataset
2. Generate summary statistics
3. Check for missing values and outliers
4. Create exploratory visualizations

### Phase 2: Model Specification
1. Identify the research question
2. Specify the econometric model
3. Document identification strategy
4. List assumptions and potential threats

### Phase 3: Estimation
1. Run baseline regressions
2. Include appropriate fixed effects
3. Cluster standard errors appropriately
4. Run robustness checks

### Phase 4: Output Generation
1. Create publication-ready tables (use `fixest::etable` or `stargazer`)
2. Generate figures with consistent styling
3. Document all specifications

### Phase 5: Quality Check
1. Run code review agent
2. Verify reproducibility
3. Score against quality gates

## R Code Template

```r
# Header
# Title: [Analysis Name]
# Author: [Name]
# Date: [YYYY-MM-DD]
# Purpose: [Brief description]

# Setup
set.seed(20260330)  # YYYYMMDD format
library(fixest)
library(data.table)
library(ggplot2)

# Load data
dt <- fread("data/[filename].csv")

# Summary statistics
summary(dt)

# Main specification
model <- feols(y ~ x1 + x2 | fe1 + fe2, 
               data = dt, 
               cluster = ~cluster_var)

# Results table
etable(model, 
       tex = TRUE, 
       file = "output/table1.tex")

# Figure
ggplot(dt, aes(x = x1, y = y)) +
  geom_point() +
  theme_minimal() +
  labs(title = "Title", x = "X Label", y = "Y Label")
ggsave("output/figure1.pdf", width = 8, height = 6)
```

## Output

- R script saved to `scripts/R/`
- Tables saved to `output/tables/`
- Figures saved to `output/figures/`
- Quality report in `quality_reports/`

## Quality Checklist

- [ ] set.seed() at top
- [ ] All packages loaded at top
- [ ] Relative paths only
- [ ] Comments on key steps
- [ ] Robustness checks included
- [ ] Output reproducible
