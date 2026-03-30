---
name: evaluator
description: Run robustness tests and parameter optimization for theoretical models. Use after code generation and calibration setup.
argument-hint: "[model_code] [calibration_spec] [targets]"
---

# Evaluator Skill (AutoTheory Paper - Appendix A.9)

## Overview

The evaluator performs two critical functions:
1. **Robustness Check**: Verify code runs without errors across parameter ranges
2. **Parameter Optimization**: Find parameters that best match empirical targets

This is a Python-based stage (no LLM) that bridges code generation and scoring.

## 1. Robustness Check

### Purpose

Before optimization, verify that the code:
- Runs without exceptions across parameter ranges
- Produces finite outputs (no NaN, Inf)
- Handles edge cases gracefully

### Implementation

```python
import numpy as np
from dataclasses import replace
from typing import Callable, Dict, List, Any

def run_robustness_check(
    compute_fn: Callable,
    params_class: type,
    calibration: Dict[str, Any]
) -> Dict[str, Any]:
    """
    Test model code with various parameter configurations.
    
    Args:
        compute_fn: The compute_multipliers function
        params_class: The ModelParams dataclass
        calibration: Output from calibration agent
        
    Returns:
        Dict with 'passed', 'failed_configs', 'summary'
    """
    base_params = params_class()
    failed_configs = []
    tested = 0
    
    # Test 1: Default parameters
    tested += 1
    result, error = test_single_config(compute_fn, base_params)
    if error:
        failed_configs.append({
            'config': 'default',
            'error': str(error)
        })
    
    # Test 2: Each parameter at test values
    for param_spec in calibration['parameters']:
        param_name = param_spec['name']
        for test_val in param_spec['test_values']:
            tested += 1
            try:
                test_params = replace(base_params, **{param_name: test_val})
                result, error = test_single_config(compute_fn, test_params)
                if error:
                    failed_configs.append({
                        'config': f'{param_name}={test_val}',
                        'error': str(error)
                    })
            except Exception as e:
                failed_configs.append({
                    'config': f'{param_name}={test_val}',
                    'error': f'Failed to set parameter: {e}'
                })
    
    passed = len(failed_configs) == 0
    
    return {
        'passed': passed,
        'total_tests': tested,
        'failed_count': len(failed_configs),
        'failed_configs': failed_configs,
        'summary': f"Robustness: {tested - len(failed_configs)}/{tested} passed"
    }


def test_single_config(
    compute_fn: Callable,
    params: Any
) -> tuple:
    """
    Test a single parameter configuration.
    
    Returns:
        (result_dict or None, error_message or None)
    """
    try:
        result = compute_fn(params)
        
        # Check for required outputs
        required_keys = ['M_style', 'M_idio', 'ratio']
        for key in required_keys:
            if key not in result:
                return None, f"Missing required output: {key}"
            
            value = result[key]
            if not np.isfinite(value):
                return None, f"Non-finite value for {key}: {value}"
        
        return result, None
        
    except Exception as e:
        return None, str(e)


def all_finite(result: Dict) -> bool:
    """Check if all numerical values in result are finite."""
    for key in ['M_style', 'M_idio', 'ratio']:
        if key in result:
            if not np.isfinite(result[key]):
                return False
    return True
```

## 2. Parameter Optimization

### Purpose

Find parameter values that minimize the loss function (match empirical targets).

### Objective Function

```python
def compute_loss(outputs: Dict, targets: Dict) -> float:
    """
    Compute sum of squared relative errors.
    
    Formula: L(x) = Σᵢ ((yᵢ(x) - tᵢ) / tᵢ)²
    
    where:
        yᵢ(x) = model output for target i
        tᵢ = empirical target i
    
    Equal weights on all targets.
    """
    loss = 0.0
    for key, target in targets.items():
        if target != 0:
            actual = outputs.get(key, 0)
            relative_error = (actual - target) / target
            loss += relative_error ** 2
    return loss
```

### Optimization Implementation

```python
from scipy.optimize import differential_evolution
import numpy as np

def optimize_parameters(
    compute_fn: Callable,
    params_class: type,
    calibration: Dict[str, Any],
    targets: Dict[str, float],
    max_iter: int = 100,
    tol: float = 1e-6,
    seed: int = 42
) -> Dict[str, Any]:
    """
    Optimize model parameters to match empirical targets.
    
    Uses scipy.optimize.differential_evolution with:
    - maxiter=100
    - tol=1e-6  
    - polish=True (local refinement after global search)
    - seed=42 (reproducibility)
    
    Args:
        compute_fn: The compute_multipliers function
        params_class: The ModelParams dataclass
        calibration: Parameter bounds and specs from calibration agent
        targets: Empirical targets to match
        
    Returns:
        Dict with optimized params, outputs, loss, and metadata
    """
    # Extract parameter info
    param_specs = calibration['parameters']
    param_names = [p['name'] for p in param_specs]
    bounds = [(p['min_bound'], p['max_bound']) for p in param_specs]
    
    # Build base params
    base_params = params_class()
    
    def objective(x: np.ndarray) -> float:
        """Objective function for optimization."""
        try:
            # Create params with current values
            param_dict = dict(zip(param_names, x))
            params = replace(base_params, **param_dict)
            
            # Compute outputs
            outputs = compute_fn(params)
            
            # Compute loss
            loss = compute_loss(outputs, targets)
            
            # Penalty for non-finite outputs
            if not all_finite(outputs):
                return 1e10
            
            return loss
            
        except Exception:
            return 1e10
    
    # Run optimization
    result = differential_evolution(
        objective,
        bounds=bounds,
        maxiter=max_iter,
        tol=tol,
        polish=True,  # Local refinement
        seed=seed,
        disp=False
    )
    
    # Extract optimized parameters
    optimized_params = dict(zip(param_names, result.x))
    
    # Compute final outputs
    final_params = replace(base_params, **optimized_params)
    final_outputs = compute_fn(final_params)
    final_loss = result.fun
    
    return {
        'success': result.success,
        'params': optimized_params,
        'outputs': final_outputs,
        'loss': final_loss,
        'iterations': result.nit,
        'function_evals': result.nfev,
        'message': result.message
    }
```

## 3. Complete Evaluator Pipeline

```python
def run_evaluator(
    compute_fn: Callable,
    params_class: type,
    calibration: Dict[str, Any],
    targets: Dict[str, float],
    loss_threshold: float = 5.0
) -> Dict[str, Any]:
    """
    Complete evaluator pipeline: robustness + optimization.
    
    Args:
        compute_fn: Model's compute function
        params_class: Parameter dataclass
        calibration: From calibration agent
        targets: Empirical targets
        loss_threshold: Max loss to accept (default 5.0)
        
    Returns:
        Complete evaluation results
    """
    # Step 1: Robustness check
    robustness = run_robustness_check(compute_fn, params_class, calibration)
    
    if not robustness['passed']:
        return {
            'status': 'FAILED',
            'stage': 'robustness',
            'robustness': robustness,
            'optimization': None,
            'error': f"Robustness check failed: {robustness['failed_count']} configurations"
        }
    
    # Step 2: Parameter optimization
    try:
        optimization = optimize_parameters(
            compute_fn, params_class, calibration, targets
        )
    except Exception as e:
        return {
            'status': 'FAILED',
            'stage': 'optimization',
            'robustness': robustness,
            'optimization': None,
            'error': f"Optimization failed: {e}"
        }
    
    # Step 3: Check if optimization succeeded
    if optimization['loss'] > loss_threshold:
        return {
            'status': 'FAILED',
            'stage': 'optimization',
            'robustness': robustness,
            'optimization': optimization,
            'error': f"Loss {optimization['loss']:.4f} exceeds threshold {loss_threshold}"
        }
    
    return {
        'status': 'PASSED',
        'stage': 'complete',
        'robustness': robustness,
        'optimization': optimization,
        'final_params': optimization['params'],
        'final_outputs': optimization['outputs'],
        'final_loss': optimization['loss']
    }
```

## Failure Handling

### Robustness Failures

| Failure Type | Action |
|--------------|--------|
| Exception in compute | Reject model |
| NaN/Inf outputs | Reject model |
| Missing outputs | Reject model |
| Timeout | Reject model |

### Optimization Failures

| Failure Type | Action |
|--------------|--------|
| Convergence failure | Continue if loss < threshold |
| Loss > 5.0 | Reject model |
| Timeout | Reject model |

From paper: "A run is discarded only if the solver fails to converge AND the final loss exceeds L > 5.0"

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
Evaluator (YOU ARE HERE)
   ├── Robustness Check → FAIL → Reject
   └── Optimization → Loss > 5.0 → Reject
        ↓ PASS
Scorer
        ↓
Simulated Referee
        ↓
Model Registry
```

## Statistics from Paper

### Price Multiplier Puzzle
- Total invocations: 1,000
- Failed at optimizer: 93 (9%)
- Failed at timeout: 7 (1%)
- Scored: 458 (46%)

### Dividend Strip Puzzle
- Total invocations: 2,153
- Failed at optimizer: 159 (7%)
- Failed at timeout: 157 (7%)
- Scored: 257 (12%)

The higher failure rates for dividend strips reflect the puzzle's greater mathematical complexity.
