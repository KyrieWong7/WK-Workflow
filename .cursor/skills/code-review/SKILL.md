---
name: code-review
description: Automated code scan for errors, suspicious patterns, and reproducibility issues. Use when reviewing R/Python/Stata scripts before running or committing.
argument-hint: "[script file or directory]"
---

# Code Review Skill (Project APE)

## Overview

Automated scanning of analysis code for errors, suspicious patterns, and reproducibility issues.

## Review Status Markers

| Marker | Meaning |
|--------|---------|
| ✓ Full | All scripts executed successfully, outputs match |
| ~ Partial | Some scripts failed or outputs differ |
| ✗ Failed | Critical scripts failed, unable to reproduce |
| 🧐 Issues | Suspicious patterns flagged |
| 🚫 Errors | Critical errors detected |

## What to Check

### 1. Syntax and Runtime Errors
- Code parses without errors
- All packages installed
- Functions exist
- Types compatible

### 2. Reproducibility
- `set.seed()` present and deterministic
- Relative paths only (no `/Users/...`)
- Package versions documented
- Data files exist

### 3. Suspicious Patterns
- Hardcoded results
- Commented-out alternative specifications
- Excessive p-value hunting indicators
- Inconsistent sample sizes

### 4. Best Practices
- Clear variable names
- Modular functions
- Error handling
- Documentation

## Review Protocol

### Phase 1: Static Analysis
```
Parse code → Check syntax → Identify issues
```

### Phase 2: Dependency Check
```
List packages → Verify installed → Check versions
```

### Phase 3: Path Analysis
```
Find file paths → Check if relative → Verify files exist
```

### Phase 4: Pattern Detection
```
Scan for suspicious patterns → Flag potential issues
```

## Suspicious Pattern Catalog

### High Severity
| Pattern | Why Suspicious |
|---------|----------------|
| `p.value < 0.05` hardcoded | May indicate p-hacking |
| Multiple `# COMMENTED OUT` alternatives | Specification searching |
| Results assigned without computation | Fabrication risk |
| `rm(list=ls())` at end only | Hiding intermediate state |

### Medium Severity
| Pattern | Why Suspicious |
|---------|----------------|
| No `set.seed()` | Non-reproducible |
| Absolute paths | Machine-specific |
| `install.packages()` in script | Version control issue |
| Magic numbers without explanation | Unclear reasoning |

### Low Severity
| Pattern | Why Suspicious |
|---------|----------------|
| Long lines (>100 chars) | Readability |
| No comments | Maintainability |
| Unclear variable names | Readability |

## Report Format

```markdown
# Code Review Report

## File: [filename]
## Date: [YYYY-MM-DD]

## Summary
- **Status:** [✓ Full / ~ Partial / ✗ Failed / 🧐 Issues / 🚫 Errors]
- **Critical Issues:** [N]
- **Warnings:** [N]
- **Notes:** [N]

## Critical Issues (must fix)

### Issue 1: [Title]
**Location:** Line [N]
**Code:**
```[language]
[problematic code]
```
**Problem:** [explanation]
**Fix:** [suggestion]

## Warnings (should fix)

### Warning 1: [Title]
[Same format]

## Notes (consider)

### Note 1: [Title]
[Same format]

## Reproducibility Checklist

- [ ] set.seed() present
- [ ] All paths relative
- [ ] Packages documented
- [ ] Data files accessible
- [ ] Output deterministic

## Recommendations
1. [Recommendation 1]
2. [Recommendation 2]
```

## Integration with Replication

After code review passes, run `/replicate` to verify outputs match.
