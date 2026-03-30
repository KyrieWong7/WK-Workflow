---
name: replicate
description: Verify reproducibility by re-executing analysis and comparing outputs. Use to validate that code produces claimed results.
argument-hint: "[script or analysis directory]"
---

# Replicate Skill (Project APE)

## Overview

Execute analysis code and verify outputs match expected results.

## Replication Protocol

### Phase 1: Environment Setup
1. Check R/Python/Stata version
2. Install required packages
3. Set working directory
4. Verify data files exist

### Phase 2: Execution
1. Run scripts in documented order
2. Capture all outputs
3. Log any errors or warnings
4. Record runtime

### Phase 3: Comparison
1. Compare generated outputs to expected
2. Check numerical precision
3. Verify figures match
4. Document any differences

### Phase 4: Report
1. Generate replication report
2. Flag any failures
3. Provide diagnostic information

## Tolerance Thresholds

| Output Type | Tolerance | Rationale |
|-------------|-----------|-----------|
| Point estimates | 1e-6 | Numerical precision |
| Standard errors | 1e-4 | MC variability |
| Coverage rates | ±0.01 | Simulation noise |
| Figures | Visual match | Allow minor rendering differences |

## Execution Order

If `00_master.R` or similar exists, use it. Otherwise:
1. `00_setup.R` or `install_packages.R`
2. `01_data_prep.R` or similar
3. `02_analysis.R` or similar
4. `03_tables.R` or similar
5. `04_figures.R` or similar

## Report Format

```markdown
# Replication Report

## Analysis: [Name]
## Date: [YYYY-MM-DD]

## Environment
- R version: [version]
- Key packages: [list with versions]
- OS: [operating system]

## Execution Log

### Script 1: [filename]
- **Status:** ✓ Success / ✗ Failed
- **Runtime:** [time]
- **Warnings:** [count]
- **Errors:** [count]

[If errors, show error message]

### Script 2: [filename]
...

## Output Comparison

### Table 1: [name]
| Statistic | Expected | Reproduced | Difference | Status |
|-----------|----------|------------|------------|--------|
| [stat] | [value] | [value] | [diff] | ✓/✗ |

### Figure 1: [name]
- **Status:** ✓ Match / ~ Similar / ✗ Different
- **Notes:** [any differences]

## Summary
- **Overall Status:** [✓ Full / ~ Partial / ✗ Failed]
- **Scripts Passed:** [N/M]
- **Outputs Matched:** [N/M]

## Issues Found
1. [Issue 1]
2. [Issue 2]

## Recommendations
1. [Recommendation 1]
2. [Recommendation 2]
```

## Common Failure Causes

| Cause | Diagnostic | Fix |
|-------|------------|-----|
| Missing package | Error message | Add to setup script |
| Wrong version | Different results | Pin versions |
| Missing data | File not found | Check paths |
| Random seed | Different each run | Add set.seed() |
| Path issue | File not found | Use relative paths |

## Integration

```
/code-review → Pass → /replicate → Pass → Ready for commit
                ↓              ↓
              Fix issues    Fix issues
```
