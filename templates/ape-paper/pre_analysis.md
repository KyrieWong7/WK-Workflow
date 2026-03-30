# Pre-Analysis Plan: [Title]

**Locked before analysis begins. SHA-256 checksum created on lock.**

---

## Research Question

[Specific, testable question. What causal effect are you estimating?]

## Conceptual Framework

### Theory 1: [Name]

**Mechanism:** [How does the policy affect outcomes? Be specific about the causal chain.]

**Prediction:**
- **Sign:** [Positive/Negative/Ambiguous] — [Rationale]
- **Magnitude:** [Expected effect size with justification]
- **Heterogeneity:** Effects should concentrate among:
  - [Subgroup 1 and why]
  - [Subgroup 2 and why]

### Theory 2: [Alternative Theory]

**Mechanism:** [Alternative explanation for observed patterns]

**Prediction:**
- **Sign:** [...]
- **Magnitude:** [...]
- **Heterogeneity:** [...]

## Primary Specification

### Main Analysis: [Method Name]

- **Question:** [Restated specific question]

- **Unit of observation:** [e.g., Individual-year, Firm-quarter]

- **Sample:** 
  - Population: [e.g., Adults ages 18-64]
  - Industries/occupations: [If restricted]
  - Time period: [e.g., 2010-2023]
  - Geographic scope: [e.g., All 50 states + DC]
  
- **Outcome variables:**
  1. [Primary outcome]: [Definition, variable name, measurement]
  2. [Secondary outcome]: [Definition]
  3. [Mechanism outcome]: [Definition]

- **Treatment variable:** [Definition, coding rules]

- **Model specification:**
  
  $$Y_{ist} = \alpha + \beta \cdot Treatment_{st} + \gamma_s + \delta_t + X_{ist}'\theta + \varepsilon_{ist}$$
  
  Where:
  - $Y_{ist}$ is [outcome definition]
  - $Treatment_{st}$ = 1 if [condition]
  - $\gamma_s$ are [fixed effects type]
  - $\delta_t$ are [fixed effects type]
  - $X_{ist}$ are [controls list]
  - Standard errors clustered at [level]

- **Expected sign:** $\beta$ [>0 / <0] because [reasoning]

## Policy Treatment Coding

Based on [source documentation], the treatment timeline is:

**Pre-period (control baseline):**
- [States/years that serve as control]

**Treatment states (with dates):**
| State | Effective Date | Notes |
|-------|----------------|-------|
| ... | ... | ... |

**Never-treated (control group):**
- [States that never adopted the policy]

## Where Mechanism Should Operate (REQUIRED)

### Who is directly affected by this policy?

The policy primarily affects:
1. [Group 1]: [Why they are affected]
2. [Group 2]: [Why they are affected]

The "first stage" (policy bite) is strongest for:
- [Characteristic 1]
- [Characteristic 2]

### Who is NOT affected?

- [Group 1]: [Why unaffected]
- [Group 2]: [Why unaffected]

### Heterogeneity tests:

1. **By [dimension 1]:**
   - Primary: [Subgroup with largest expected effects]
   - Comparison: [Subgroup with smaller expected effects]
   
2. **By [dimension 2]:**
   - [Subgroup A]
   - [Subgroup B]

3. **By [dimension 3]:**
   - [...]

## Robustness Checks

1. **Event study specification:**
   - Include leads and lags around treatment year
   - Test for pre-trends (leads should be zero)
   - Examine dynamics (how quickly effects appear/fade)

2. **Sample restrictions:**
   - [Alternative sample 1]
   - [Alternative sample 2]

3. **Alternative outcomes:**
   - [Outcome 1]
   - [Outcome 2]

4. **Weighting:**
   - Unweighted
   - Population weighted
   - [Other weighting scheme]

5. **Clustering:**
   - Cluster at [level] (primary)
   - Wild bootstrap cluster (if few clusters)
   - [Alternative clustering]

6. **Modern DiD estimators:** (if applicable)
   - Callaway-Sant'Anna
   - Sun-Abraham
   - de Chaisemartin-D'Haultfœuille

## Validity Checks

1. **Parallel trends:**
   - Visual inspection of pre-treatment trends
   - Event study showing no significant pre-trends
   - Compare treated vs. control before treatment

2. **Placebo tests:**
   - Run same analysis on occupations/industries NOT affected
   - Should find no effect

3. **Timing tests:**
   - Use alternative treatment years (1 year before/after)
   - Should find no effect at wrong timing

4. **Covariate balance:**
   - Check that observables don't change discontinuously at treatment

## What Would Invalidate the Design

1. **[Threat 1]:** [Description and what would indicate this problem]

2. **[Threat 2]:** [Description]

3. **[Threat 3]:** [Description]

4. **[Threat 4]:** [Description]

5. **[Threat 5]:** [Description]

## Data Notes

### Variable Definitions
| Variable | Source | Definition | Codes |
|----------|--------|------------|-------|
| ... | ... | ... | ... |

### Measurement Limitations
1. [Limitation 1]
2. [Limitation 2]
3. [Limitation 3]

---

**Checksum Instructions:**
After finalizing this document, generate SHA-256 checksum:
```bash
sha256sum pre_analysis.md > pre_analysis.md.checksum
```

This checksum proves the pre-analysis plan was written before seeing results.
