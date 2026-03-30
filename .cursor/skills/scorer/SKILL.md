---
name: scorer
description: Compute quantitative scores for theoretical models using exact formulas from AutoTheory paper. Use after parameter optimization.
argument-hint: "[model_outputs] [targets] [parameters]"
---

# Scorer Skill (AutoTheory Paper - Appendix A.10)

## Overview

Compute quantitative scores for theoretical models based on three dimensions:
- **Fit** (50% weight): How well does the model match empirical targets?
- **Plausibility** (25% weight): Are parameter values economically reasonable?
- **Parsimony** (25% weight): How few free parameters does the model use?

## Exact Formulas from Paper

### 1. Fit Score (50% weight)

```python
import math

def compute_fit_score(outputs: dict, targets: dict) -> float:
    """
    Compute fit score using exponential decay of average percentage error.
    
    Formula: Fit = 100 × exp(-ē/20)
    where ē = mean percentage error across targets
    
    Args:
        outputs: Model outputs (e.g., {'M_style': 0.15, 'M_idio': 0.02, 'ratio': 100})
        targets: Empirical targets (same keys)
        
    Returns:
        Fit score in [0, 100]
    """
    errors = []
    
    for key, target in targets.items():
        if target != 0:
            actual = outputs.get(key, 0)
            # Percentage error: 100 × |actual - target| / |target|
            pct_error = 100 * abs(actual - target) / abs(target)
            errors.append(pct_error)
    
    if not errors:
        return 0.0
    
    # Average percentage error
    e_bar = sum(errors) / len(errors)
    
    # Fit score with exponential decay
    # e_bar = 0% → Fit = 100
    # e_bar = 20% → Fit ≈ 37
    # e_bar = 50% → Fit ≈ 8
    fit = 100 * math.exp(-e_bar / 20)
    
    return min(100, max(0, fit))
```

#### Fit Score Reference Table

| Average % Error | Fit Score | Interpretation |
|-----------------|-----------|----------------|
| 0% | 100.0 | Perfect fit |
| 5% | 77.9 | Excellent |
| 10% | 60.7 | Good |
| 15% | 47.2 | Acceptable |
| 20% | 36.8 | Fair |
| 30% | 22.3 | Poor |
| 50% | 8.2 | Very poor |
| 100% | 0.7 | Failure |

### 2. Plausibility Score (25% weight)

```python
def compute_plausibility_score(
    params: dict, 
    bounds: dict,
    default_bounds: dict = None
) -> float:
    """
    Check each parameter against plausible economic bounds.
    
    Formula:
        base = (n_valid / n_params) × 100
        penalty = min(30, max_deviation × 10)
        Plausibility = max(0, base - penalty)
    
    Args:
        params: Calibrated parameter values
        bounds: Dict of (lower, upper) bounds per parameter
        default_bounds: Fallback bounds if not specified
        
    Returns:
        Plausibility score in [0, 100]
    """
    if default_bounds is None:
        default_bounds = {
            'gamma': (0.5, 10.0),      # Risk aversion
            'beta': (0.9, 0.999),       # Discount factor
            'alpha': (0.0, 1.0),        # Participation rate
            'lambda': (0.0, 1.0),       # Cost/weight parameter
            'sigma': (0.0, 1.0),        # Volatility
            'K': (1, 2278),             # Position count
        }
    
    n_params = len(params)
    n_valid = 0
    max_deviation = 0.0
    
    for name, value in params.items():
        # Get bounds (parameter-specific or default)
        lower, upper = bounds.get(name, default_bounds.get(name, (float('-inf'), float('inf'))))
        
        if lower <= value <= upper:
            n_valid += 1
        else:
            # Compute deviation as fraction of range
            range_size = upper - lower + 1e-6
            if value < lower:
                deviation = (lower - value) / range_size
            else:
                deviation = (value - upper) / range_size
            max_deviation = max(max_deviation, deviation)
    
    # Base score: proportion of valid parameters
    base = (n_valid / n_params) * 100 if n_params > 0 else 100
    
    # Penalty for out-of-bounds parameters (max 30 points)
    penalty = min(30, max_deviation * 10)
    
    return max(0, base - penalty)
```

#### Standard Parameter Bounds

| Parameter | Symbol | Plausible Range | Notes |
|-----------|--------|-----------------|-------|
| Risk aversion | γ | [0.5, 10] | >10 is implausible |
| Discount factor | β | [0.9, 0.999] | Annual |
| Fractions | α, δ, φ | [0, 1] | Proportions |
| Volatility | σ | [0.01, 1.0] | Annual |
| Counts | K, n | [1, N] | Positive integers |
| Costs | c, κ | [0, 0.1] | Small positive |

### 3. Parsimony Score (25% weight)

```python
def compute_parsimony_score(n_free_params: int) -> float:
    """
    Score based on number of free parameters.
    
    Formula: Parsimony = max(0, 100 - 10 × n_free)
    
    Rationale: Models with more parameters than targets are overfitting.
    Each free parameter costs 10 points.
    
    Args:
        n_free_params: Number of model-specific (optimizable) parameters
        
    Returns:
        Parsimony score in [0, 100]
    """
    return max(0, 100 - 10 * n_free_params)
```

#### Parsimony Score Reference Table

| Free Parameters | Parsimony Score | Assessment |
|-----------------|-----------------|------------|
| 1 | 90 | Excellent |
| 2 | 80 | Very good |
| 3 | 70 | Good |
| 4 | 60 | Acceptable |
| 5 | 50 | Fair |
| 6 | 40 | Poor |
| 7 | 30 | Very poor |
| 8 | 20 | Overfitting |
| 9 | 10 | Severe overfitting |
| 10+ | 0 | Unacceptable |

### 4. Total Score

```python
def compute_total_score(
    fit: float, 
    plausibility: float, 
    parsimony: float,
    weights: tuple = (0.50, 0.25, 0.25)
) -> float:
    """
    Compute weighted total score.
    
    Formula: Total = w_fit × Fit + w_plaus × Plausibility + w_pars × Parsimony
    
    Default weights from paper: (0.50, 0.25, 0.25)
    
    Args:
        fit: Fit score [0, 100]
        plausibility: Plausibility score [0, 100]
        parsimony: Parsimony score [0, 100]
        weights: Tuple of (w_fit, w_plaus, w_pars)
        
    Returns:
        Total score [0, 100], rounded to 1 decimal
    """
    w_fit, w_plaus, w_pars = weights
    total = w_fit * fit + w_plaus * plausibility + w_pars * parsimony
    return round(total, 1)
```

## Complete Scoring Function

```python
def score_model(
    outputs: dict,
    targets: dict,
    params: dict,
    param_bounds: dict,
    n_free_params: int = None
) -> dict:
    """
    Complete scoring function for a theoretical model.
    
    Args:
        outputs: Model outputs after optimization
        targets: Empirical targets to match
        params: Calibrated parameter values
        param_bounds: Valid bounds for each parameter
        n_free_params: Number of free parameters (if None, count from params)
        
    Returns:
        Dictionary with all scores and breakdown
    """
    if n_free_params is None:
        n_free_params = len(params)
    
    # Compute component scores
    fit = compute_fit_score(outputs, targets)
    plausibility = compute_plausibility_score(params, param_bounds)
    parsimony = compute_parsimony_score(n_free_params)
    total = compute_total_score(fit, plausibility, parsimony)
    
    # Compute percentage errors for each target
    target_errors = {}
    for key, target in targets.items():
        if target != 0:
            actual = outputs.get(key, 0)
            target_errors[key] = {
                'target': target,
                'actual': actual,
                'error_pct': 100 * abs(actual - target) / abs(target)
            }
    
    return {
        'total': total,
        'fit': round(fit, 1),
        'plausibility': round(plausibility, 1),
        'parsimony': round(parsimony, 1),
        'n_free_params': n_free_params,
        'target_errors': target_errors,
        'params': params,
        'weights': {'fit': 0.50, 'plausibility': 0.25, 'parsimony': 0.25}
    }
```

## Example Usage

```python
# Example: Bayesian-Breadth-Cost CARA model

# Model outputs after optimization
outputs = {
    'M_style': 0.148,
    'M_idio': 0.0015,
    'ratio': 98.7
}

# Empirical targets
targets = {
    'M_style': 0.15,
    'M_idio': 0.0015,
    'ratio': 100
}

# Calibrated parameters
params = {
    'gamma': 3.2,
    'lambda_cost': 0.15
}

# Parameter bounds
param_bounds = {
    'gamma': (0.5, 10.0),
    'lambda_cost': (0.0, 1.0)
}

# Score the model
result = score_model(outputs, targets, params, param_bounds)

print(f"Total Score: {result['total']}/100")
print(f"  Fit: {result['fit']}")
print(f"  Plausibility: {result['plausibility']}")
print(f"  Parsimony: {result['parsimony']}")

# Output:
# Total Score: 84.0/100
#   Fit: 98.0  (outputs very close to targets)
#   Plausibility: 100.0  (parameters in valid ranges)
#   Parsimony: 80.0  (2 free parameters)
```

## Interpretation Guide

| Total Score | Recommendation | Meaning |
|-------------|----------------|---------|
| 90-100 | Accept | Publication-ready |
| 80-89 | Minor Revision | Strong, small improvements needed |
| 70-79 | Major Revision | Promising, significant work needed |
| 60-69 | Revise & Resubmit | Substantial issues |
| 50-59 | Borderline | Major problems |
| <50 | Reject | Fundamental flaws |

## Pipeline Position

```
Problem Analyzer
        ↓
Theory Evolution
        ↓
Math Auditor
        ↓
Pseudocode Agent
        ↓
Pseudocode Reviewer
        ↓
Coding Agent
        ↓
Code-vs-Pseudocode Reviewer
        ↓
Calibration Agent
        ↓
Evaluator (optimization)
        ↓
Scorer (YOU ARE HERE) → Quantitative score
        ↓
Simulated Referee → Final evaluation
        ↓
Model Registry
```
