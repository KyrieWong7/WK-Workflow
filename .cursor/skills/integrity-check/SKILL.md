# Integrity Check Skill (Project APE)

**来源:** https://ape.socialcatalystlab.org/methodology  
**Purpose:** 确保研究代码和数据的完整性和可复现性

---

## Overview

APE 项目对所有论文执行严格的完整性检查：

```
┌─────────────────────────────────────────────────────────────────┐
│                  APE Integrity Pipeline                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌─────────────────────────────────────────────────────────┐  │
│   │              Stage 1: Code Scanning                      │  │
│   │   - Check for hardcoded results                         │  │
│   │   - Check for fabricated data                           │  │
│   │   - Check for irreproducible operations                 │  │
│   └─────────────────────────────────────────────────────────┘  │
│                          │                                      │
│                          ▼                                      │
│   ┌─────────────────────────────────────────────────────────┐  │
│   │              Stage 2: Data Verification                  │  │
│   │   - Verify data sources exist                           │  │
│   │   - Check data checksums                                │  │
│   │   - Validate variable definitions                        │  │
│   └─────────────────────────────────────────────────────────┘  │
│                          │                                      │
│                          ▼                                      │
│   ┌─────────────────────────────────────────────────────────┐  │
│   │              Stage 3: Replication Test                   │  │
│   │   - Run code from scratch                               │  │
│   │   - Compare outputs to paper                            │  │
│   │   - Document any discrepancies                          │  │
│   └─────────────────────────────────────────────────────────┘  │
│                          │                                      │
│                          ▼                                      │
│   ┌─────────────────────────────────────────────────────────┐  │
│   │              Stage 4: Robustness Verification            │  │
│   │   - Re-run with different seeds                         │  │
│   │   - Test sensitivity to specifications                  │  │
│   │   - Verify pre-analysis plan compliance                 │  │
│   └─────────────────────────────────────────────────────────┘  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Stage 1: Code Scanning

### System Prompt for Code Auditor

```
You are a code auditor reviewing research code for integrity issues.

Your job is to scan the code and flag potential problems that could
indicate fabrication, p-hacking, or irreproducibility.

Be thorough but fair. Not all flagged patterns are necessarily problems -
some may have legitimate explanations. Your role is to identify issues
for human review.
```

### Issue Categories

#### CRITICAL ERRORS (Automatic Fail)

| Issue | Pattern | Example |
|-------|---------|---------|
| Hardcoded results | Numbers assigned directly to output variables | `coef = 0.0345` |
| Fabricated data | Random data presented as real | `data = np.random.randn(1000)` without documentation |
| Irreproducible results | Non-deterministic without seed | `sample(data, 100)` without set.seed |
| Missing data files | Referenced but not provided | `read.csv("confidential.csv")` |

#### SERIOUS ISSUES (Requires Explanation)

| Issue | Pattern | Example |
|-------|---------|---------|
| Selective reporting | Commented-out specifications | `# reg y x z  # didn't work` |
| P-hacking indicators | Multiple similar specifications | `reg y x if condition1`, `reg y x if condition2`, ... |
| Post-hoc outlier removal | Outlier removal after seeing results | `drop if resid > 3` after estimation |
| Post-hoc hypothesis | HARKing indicators | Comments suggesting hypothesis changed |

#### WARNINGS (Document and Proceed)

| Issue | Pattern | Example |
|-------|---------|---------|
| Missing error handling | No try/catch | Long scripts without error handling |
| Hardcoded paths | Local file paths | `/Users/john/data/file.csv` |
| No random seed | Missing seed in simulations | Monte Carlo without `set.seed()` |
| Missing package versions | No requirements file | No `requirements.txt` or `renv.lock` |

### Code Scan Output Format

```json
{
  "scan_id": "scan_20260330_001",
  "paper_id": "apep_0464",
  "timestamp": "2026-03-30T10:30:00Z",
  "status": "FAIL | PASS_WITH_WARNINGS | PASS",
  "summary": {
    "critical_errors": 0,
    "serious_issues": 2,
    "warnings": 5
  },
  "issues": [
    {
      "severity": "SERIOUS",
      "type": "selective_reporting",
      "file": "analysis/main_regression.R",
      "line": 145,
      "code": "# reg y x z  # results not robust",
      "explanation": "Commented-out specification suggests selective reporting",
      "recommendation": "Document why this specification was excluded in the paper"
    },
    {
      "severity": "WARNING",
      "type": "hardcoded_path",
      "file": "analysis/data_cleaning.R",
      "line": 12,
      "code": "setwd('/Users/researcher/project')",
      "explanation": "Hardcoded local path prevents portability",
      "recommendation": "Use relative paths or here::here()"
    }
  ],
  "files_scanned": 15,
  "lines_scanned": 2847
}
```

---

## Stage 2: Data Verification

### Required Checks

```python
def verify_data_integrity(paper_dir: str) -> dict:
    """
    Verify data sources and checksums.
    
    Returns:
        Verification report with pass/fail status
    """
    report = {
        "data_sources_documented": False,
        "all_files_present": False,
        "checksums_match": False,
        "variables_defined": False
    }
    
    # 1. Check data documentation
    data_readme = paper_dir / "data" / "README.md"
    report["data_sources_documented"] = data_readme.exists()
    
    # 2. Check file presence
    manifest = load_manifest(paper_dir / "data" / "manifest.json")
    report["all_files_present"] = all(
        (paper_dir / "data" / f["path"]).exists() 
        for f in manifest["files"]
    )
    
    # 3. Verify checksums
    report["checksums_match"] = all(
        compute_sha256(paper_dir / "data" / f["path"]) == f["sha256"]
        for f in manifest["files"]
    )
    
    # 4. Check variable definitions
    codebook = paper_dir / "data" / "codebook.md"
    report["variables_defined"] = codebook.exists()
    
    return report
```

### Data Manifest Format

```json
{
  "manifest_version": "1.0",
  "paper_id": "apep_0464",
  "files": [
    {
      "path": "raw/census_2020.csv",
      "source": "US Census Bureau",
      "url": "https://data.census.gov/...",
      "accessed": "2026-03-15",
      "sha256": "a1b2c3d4e5f6...",
      "rows": 50000,
      "columns": 25
    }
  ]
}
```

---

## Stage 3: Replication Test

### Replication Protocol

```bash
#!/bin/bash
# replication_test.sh

set -e  # Exit on any error

PAPER_DIR=$1
OUTPUT_DIR="replication_$(date +%Y%m%d)"

echo "Starting replication test for $PAPER_DIR"

# 1. Create clean environment
mkdir -p $OUTPUT_DIR
cd $OUTPUT_DIR

# 2. Copy code (not data or outputs)
cp -r $PAPER_DIR/code .
cp -r $PAPER_DIR/data ./data  # Assuming data is included

# 3. Install dependencies
if [ -f "requirements.txt" ]; then
    pip install -r requirements.txt
fi
if [ -f "renv.lock" ]; then
    Rscript -e "renv::restore()"
fi

# 4. Run main analysis
Rscript code/main.R 2>&1 | tee replication_log.txt

# 5. Compare outputs
python compare_outputs.py \
    --original $PAPER_DIR/output \
    --replicated $OUTPUT_DIR/output \
    --tolerance 0.001

echo "Replication test complete"
```

### Output Comparison Rules

| Output Type | Tolerance | Notes |
|-------------|-----------|-------|
| Point estimates | 0.1% relative | Account for floating point |
| Standard errors | 1% relative | Bootstrap may vary |
| P-values | 0.01 absolute | Round to 3 decimals |
| Figures | Visual diff | Check axes, trends |
| Tables | Exact match | After rounding |

---

## Stage 4: Robustness Verification

### Pre-Analysis Plan Compliance

```python
def check_preanalysis_compliance(paper_dir: str) -> dict:
    """
    Verify paper follows its pre-analysis plan.
    """
    # Load pre-analysis plan
    pap = load_preanalysis_plan(paper_dir / "pre_analysis.md")
    
    # Load paper results
    paper = load_paper(paper_dir / "paper.pdf")
    
    compliance = {
        "primary_specification_matches": False,
        "all_preregistered_tests_run": False,
        "no_unregistered_primary_tests": False,
        "deviations_documented": False
    }
    
    # Check primary specification
    compliance["primary_specification_matches"] = (
        paper.primary_spec == pap.primary_spec
    )
    
    # Check all pre-registered tests are run
    registered_tests = set(pap.robustness_checks)
    run_tests = set(paper.robustness_checks)
    compliance["all_preregistered_tests_run"] = registered_tests <= run_tests
    
    # Check for unregistered primary analyses
    compliance["no_unregistered_primary_tests"] = (
        not paper.has_unregistered_primary()
    )
    
    # Check deviations are documented
    if paper.deviations:
        compliance["deviations_documented"] = all(
            d.documented for d in paper.deviations
        )
    else:
        compliance["deviations_documented"] = True
    
    return compliance
```

### Seed Sensitivity Test

```python
def test_seed_sensitivity(code_path: str, n_seeds: int = 10) -> dict:
    """
    Run analysis with multiple random seeds.
    
    Returns:
        Summary of result variation across seeds
    """
    results = []
    
    for seed in range(n_seeds):
        # Run analysis with this seed
        output = run_analysis(code_path, seed=seed)
        results.append(output)
    
    # Compute variation
    variation = {
        "main_coefficient": {
            "mean": np.mean([r.main_coef for r in results]),
            "std": np.std([r.main_coef for r in results]),
            "cv": np.std([r.main_coef for r in results]) / 
                  np.mean([r.main_coef for r in results])
        },
        "significance_consistent": all(
            r.p_value < 0.05 for r in results
        ) or all(
            r.p_value >= 0.05 for r in results
        )
    }
    
    return variation
```

---

## Final Integrity Report

```json
{
  "paper_id": "apep_0464",
  "check_date": "2026-03-30",
  "overall_status": "PASS | PASS_WITH_ISSUES | FAIL",
  "stages": {
    "code_scan": {
      "status": "PASS",
      "critical_errors": 0,
      "serious_issues": 0,
      "warnings": 3
    },
    "data_verification": {
      "status": "PASS",
      "sources_documented": true,
      "files_present": true,
      "checksums_valid": true
    },
    "replication": {
      "status": "PASS",
      "all_outputs_match": true,
      "max_deviation": 0.0001
    },
    "robustness": {
      "status": "PASS",
      "preanalysis_compliant": true,
      "seed_sensitive": false
    }
  },
  "issues_requiring_attention": [
    "Warning: 3 hardcoded paths should be made relative"
  ],
  "certified": true,
  "certified_by": "APE Integrity System v1.0"
}
```

---

## Usage in Cursor

```bash
# Run full integrity check
/integrity-check paper_directory/

# Run specific stage
/integrity-check --stage code-scan paper_directory/
/integrity-check --stage data-verify paper_directory/
/integrity-check --stage replicate paper_directory/
/integrity-check --stage robustness paper_directory/

# Quick scan (code only)
/integrity-check --quick paper_directory/
```

---

## Integration with Paper Workflow

论文必须通过完整性检查才能进入 tournament：

```python
def is_publication_ready(paper: Paper) -> bool:
    """Check if paper meets all publication requirements."""
    return (
        paper.review_status.all_stages_passed and
        paper.integrity_check.overall_status in ["PASS", "PASS_WITH_ISSUES"] and
        paper.integrity_check.stages["replication"]["status"] == "PASS"
    )
```

---

*Based on APE methodology (https://ape.socialcatalystlab.org/methodology)*
