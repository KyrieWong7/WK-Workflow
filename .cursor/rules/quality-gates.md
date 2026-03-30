# Quality Gates & Scoring Rubrics

## Thresholds

- **80/100 = Commit** -- good enough to save
- **90/100 = PR** -- ready for review
- **95/100 = Excellence** -- publication-ready

## Scoring Dimensions (AutoTheory)

| Dimension | Weight | Description |
|-----------|--------|-------------|
| Fit | 50% | How closely outputs match empirical targets |
| Plausibility | 25% | Whether parameters are economically reasonable |
| Parsimony | 25% | Fewer free parameters = better |

### Fit Score Calculation

```python
fit_score = 100 * exp(-sum((y_i - t_i) / t_i)^2)
```

Where `y_i` is model output, `t_i` is target.

### Plausibility Score

- Risk aversion γ in [1, 10]: Full points
- Fractions in [0, 1]: Full points
- Out of bounds: -20 per violation

### Parsimony Score

- 1-2 parameters: 100
- 3-4 parameters: 80
- 5-6 parameters: 60
- 7+ parameters: 0

## R Scripts (.R)

| Severity | Issue | Deduction |
|----------|-------|-----------|
| Critical | Syntax errors | -100 |
| Critical | Missing set.seed() | -20 |
| Critical | Hardcoded absolute paths | -20 |
| Major | Missing package versions | -10 |
| Major | No comments on key steps | -5 |
| Minor | Style violations | -1 |

## Theoretical Derivations

| Severity | Issue | Deduction |
|----------|-------|-----------|
| Critical | Mathematical error | -100 |
| Critical | Undefined notation | -30 |
| Critical | Market clearing violated | -30 |
| Major | Missing assumptions | -15 |
| Major | Incomplete derivation | -10 |
| Minor | Notation inconsistency | -3 |

## Tournament Evaluation (Project APE)

### Five Evaluation Dimensions

1. **Identification Strategy** (30%)
   - Is the causal inference credible?
   - Are assumptions stated and reasonable?

2. **Novelty** (20%)
   - Does it contribute new insights?
   - Is it differentiated from existing work?

3. **Policy Relevance** (20%)
   - Does it matter for real-world decisions?
   - Are implications clearly stated?

4. **Execution Quality** (20%)
   - Is the analysis well-executed?
   - Are robustness checks included?

5. **Appropriate Scope** (10%)
   - Is the scope well-calibrated?
   - Are limitations acknowledged?

### TrueSkill Rating

- **μ (mu):** Estimated skill level
- **σ (sigma):** Uncertainty
- **Conservative Rating:** μ - 3σ (for ranking)

## Enforcement

- **Score < 80:** Block commit. List blocking issues.
- **Score < 90:** Allow commit, warn. List recommendations.
- User can override with justification.

## Quality Reports

Save to `quality_reports/` with format:
- Plans: `plans/YYYY-MM-DD_description.md`
- Session logs: `session_logs/YYYY-MM-DD_description.md`
- Merge reports: `merges/YYYY-MM-DD_branch-name.md`
