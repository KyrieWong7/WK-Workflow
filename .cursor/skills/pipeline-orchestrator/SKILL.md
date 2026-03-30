---
name: pipeline-orchestrator
description: Coordinate the complete AutoTheory pipeline from puzzle analysis to model registry. Use when running the full theory discovery workflow.
argument-hint: "[puzzle_context] [mode: single|batch] [max_iterations]"
---

# Pipeline Orchestrator (AutoTheory Paper - Appendix C)

## Overview

The orchestrator coordinates all 11 pipeline stages to run the complete theory discovery workflow. It handles:
- Sequential execution of stages
- Retry logic at each stage
- Parent selection for evolution
- Model registry management
- Batch execution with parallelization

## Complete Pipeline (11 Stages)

```
┌─────────────────────────────────────────────────────────────────┐
│                    AutoTheory Pipeline                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  1. Problem Analyzer ─────────────────────────────────────────► │
│         │                                                       │
│         ▼                                                       │
│  2. Theory Evolution ──────────────────┐                        │
│     (Random/Mutate/Crossover)          │                        │
│         │                              │ Feedback               │
│         ▼                              │                        │
│  3. Math Auditor ◄────────┐            │                        │
│         │ PASS            │ Fix        │                        │
│         │          FAIL───┘ (max 2x)   │                        │
│         ▼                              │                        │
│  4. Pseudocode Agent                   │                        │
│         │                              │                        │
│         ▼                              │                        │
│  5. Pseudocode Reviewer ◄───┐          │                        │
│         │ PASS              │ Retry    │                        │
│         │           FAIL────┘ (max 2x) │                        │
│         ▼                              │                        │
│  6. Coding Agent ◄─────────┐           │                        │
│         │                  │ Retry     │                        │
│         │           Error──┘ (2×3=6)   │                        │
│         ▼                              │                        │
│  7. Code-vs-Pseudocode Reviewer ◄──┐   │                        │
│         │ PASS                     │   │                        │
│         │                   FAIL───┘   │                        │
│         ▼                              │                        │
│  8. Calibration Agent                  │                        │
│         │                              │                        │
│         ▼                              │                        │
│  9. Evaluator                          │                        │
│     (Robustness + Optimization)        │                        │
│         │ PASS                         │                        │
│         ▼                              │                        │
│  10. Scorer                            │                        │
│      (Fit/Plausibility/Parsimony)      │                        │
│         │                              │                        │
│         ▼                              │                        │
│  11. Simulated Referee ────────────────┘                        │
│         │                                                       │
│         ▼                                                       │
│     Model Registry ◄─────── Parent Selection                    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Retry Configuration

| Stage | Retry Type | Max Attempts |
|-------|------------|--------------|
| LLM API | Exponential backoff | 3 (1s, 2s, 4s) |
| Math Auditor | Fix-and-reaudit | 1 + 2 fixes |
| Pseudocode Agent | Generate + review-retry | 1 + 2 retries |
| Coding Agent | Fresh starts × retries | 2 × 3 = 6 |
| Code-vs-Pseudocode | Review + retry | 1 + 1 retry |
| Evaluator | No retry | 1 |

## Pipeline Execution

### Single Run Mode

```python
def run_single_pipeline(
    puzzle_context: Dict,
    model_registry: ModelRegistry,
    max_attempts: int = 1
) -> Dict:
    """
    Run a single pipeline iteration.
    
    Args:
        puzzle_context: Puzzle description, targets, baseline
        model_registry: Registry of existing models
        max_attempts: Max pipeline runs before giving up
        
    Returns:
        Result dict with status, model, scores
    """
    for attempt in range(max_attempts):
        try:
            # Stage 1: Problem Analysis (cached if same puzzle)
            analysis = run_problem_analyzer(puzzle_context)
            
            # Stage 2: Theory Evolution
            strategy, parents = select_strategy(model_registry)
            proposal = run_theory_evolution(
                puzzle_context, 
                analysis,
                strategy=strategy,
                parents=parents
            )
            
            # Stage 3: Math Audit (with fix loop)
            audit_result = run_math_audit_with_fixes(proposal, max_fixes=2)
            if audit_result['verdict'] != 'PASS':
                continue  # Try again
            
            # Stage 4-5: Pseudocode (with review loop)
            pseudocode = run_pseudocode_with_review(
                audit_result['proposal'], 
                max_retries=2
            )
            if pseudocode['status'] != 'PASS':
                continue
            
            # Stage 6-7: Coding (with review)
            code = run_coding_with_review(
                audit_result['proposal'],
                pseudocode['pseudocode'],
                max_fresh_starts=2,
                max_fixes_per_start=3
            )
            if code['status'] != 'PASS':
                continue
            
            # Stage 8: Calibration
            calibration = run_calibration(
                audit_result['proposal'],
                code['code']
            )
            
            # Stage 9: Evaluation (robustness + optimization)
            evaluation = run_evaluator(
                code['code'],
                calibration,
                puzzle_context['targets']
            )
            if evaluation['status'] != 'PASSED':
                continue
            
            # Stage 10: Scoring
            scores = run_scorer(
                evaluation['final_outputs'],
                puzzle_context['targets'],
                evaluation['final_params'],
                calibration
            )
            
            # Stage 11: Referee
            referee_report = run_referee(
                audit_result['proposal'],
                scores,
                evaluation
            )
            
            # Register model
            model = Model(
                proposal=audit_result['proposal'],
                code=code['code'],
                params=evaluation['final_params'],
                outputs=evaluation['final_outputs'],
                scores=scores,
                referee_report=referee_report,
                strategy=strategy,
                parents=parents
            )
            model_registry.add(model)
            
            return {
                'status': 'SUCCESS',
                'model': model,
                'attempt': attempt + 1
            }
            
        except PipelineError as e:
            log_error(f"Attempt {attempt + 1} failed: {e}")
            continue
    
    return {
        'status': 'FAILED',
        'error': f'All {max_attempts} attempts failed'
    }
```

### Batch Mode (Parallel)

```python
import asyncio
from concurrent.futures import ProcessPoolExecutor

async def run_batch_pipeline(
    puzzle_context: Dict,
    n_iterations: int = 100,
    max_workers: int = 128,
    seed: int = 42
) -> Dict:
    """
    Run multiple pipeline iterations in parallel.
    
    From paper: "128 concurrent workers"
    
    Args:
        puzzle_context: Puzzle specification
        n_iterations: Total runs to attempt
        max_workers: Concurrent workers
        seed: Random seed for reproducibility
        
    Returns:
        Summary statistics and model registry
    """
    model_registry = ModelRegistry()
    results = []
    
    with ProcessPoolExecutor(max_workers=max_workers) as executor:
        futures = []
        for i in range(n_iterations):
            run_seed = seed + i
            future = executor.submit(
                run_single_pipeline,
                puzzle_context,
                model_registry,
                run_seed
            )
            futures.append(future)
        
        # Collect results
        for future in futures:
            result = future.result()
            results.append(result)
    
    # Compute statistics
    n_success = sum(1 for r in results if r['status'] == 'SUCCESS')
    success_rate = n_success / n_iterations
    
    scored_models = model_registry.get_all()
    if scored_models:
        scores = [m.referee_report['score'] for m in scored_models]
        best_score = max(scores)
        mean_score = sum(scores) / len(scores)
    else:
        best_score = 0
        mean_score = 0
    
    return {
        'n_iterations': n_iterations,
        'n_success': n_success,
        'success_rate': success_rate,
        'n_scored': len(scored_models),
        'best_score': best_score,
        'mean_score': mean_score,
        'model_registry': model_registry
    }
```

## Strategy Selection

```python
import random

def select_strategy(model_registry: ModelRegistry) -> tuple:
    """
    Select strategy and parents for theory evolution.
    
    From paper:
    - Before any models: always Random
    - After first model: Random 35%, Mutate 35%, Crossover 30%
    - Parents selected from top 10 with score >= 35
    """
    scored_models = model_registry.get_scored()
    
    # No models yet: always random
    if not scored_models:
        return 'random', None
    
    # Select strategy
    r = random.random()
    if r < 0.35:
        strategy = 'random'
        parents = None
    elif r < 0.70:
        strategy = 'mutate'
        parents = select_parents(scored_models, n=1)
    else:
        strategy = 'crossover'
        parents = select_parents(scored_models, n=2)
    
    return strategy, parents


def select_parents(models: List, n: int = 1, min_score: int = 35) -> List:
    """
    Select parent(s) for mutation/crossover.
    
    - Filter to score >= 35
    - Take top 10
    - Weight selection by score
    """
    eligible = [m for m in models if m.referee_report['score'] >= min_score]
    if len(eligible) < n:
        return None  # Fall back to random
    
    top_10 = sorted(eligible, key=lambda m: m.referee_report['score'], reverse=True)[:10]
    weights = [m.referee_report['score'] for m in top_10]
    
    parents = random.choices(top_10, weights=weights, k=n)
    return parents
```

## Model Registry

```python
from dataclasses import dataclass
from typing import List, Optional
import json
import os

@dataclass
class Model:
    """A scored theoretical model."""
    id: str
    proposal: str
    code: str
    params: Dict
    outputs: Dict
    scores: Dict
    referee_report: Dict
    strategy: str
    parents: Optional[List[str]]
    generation: int

class ModelRegistry:
    """Persistent storage for models."""
    
    def __init__(self, path: str = './models'):
        self.path = path
        self.models = {}
        os.makedirs(path, exist_ok=True)
        self._load()
    
    def add(self, model: Model):
        """Add model to registry."""
        self.models[model.id] = model
        self._save(model)
    
    def get_scored(self) -> List[Model]:
        """Get all models with referee scores."""
        return [m for m in self.models.values() 
                if m.referee_report and 'score' in m.referee_report]
    
    def get_top(self, n: int = 10) -> List[Model]:
        """Get top N models by score."""
        scored = self.get_scored()
        return sorted(scored, key=lambda m: m.referee_report['score'], reverse=True)[:n]
    
    def _save(self, model: Model):
        """Save model to disk."""
        path = os.path.join(self.path, f'{model.id}.json')
        with open(path, 'w') as f:
            json.dump(model.__dict__, f, indent=2)
    
    def _load(self):
        """Load models from disk."""
        for filename in os.listdir(self.path):
            if filename.endswith('.json'):
                path = os.path.join(self.path, filename)
                with open(path) as f:
                    data = json.load(f)
                    self.models[data['id']] = Model(**data)
```

## Paper Statistics

### Price Multiplier Puzzle

| Metric | Value |
|--------|-------|
| Total invocations | 1,000 |
| Pipeline success rate | 46% |
| Scored models | 458 |
| Best score | 84 |
| Mean score | 48.5 |
| Models ≥ 80 | 23 |

### Dividend Strip Puzzle

| Metric | Value |
|--------|-------|
| Total invocations | 2,153 |
| Pipeline success rate | 12% |
| Scored models | 257 |
| Best score | 58 |
| Mean score | 27.8 |
| Models ≥ 80 | 0 |

### Attrition by Stage

| Stage | Price Mult | Div Strips |
|-------|------------|------------|
| Math Audit | 41% | 66% |
| Code Generation | 3% | 4% |
| Optimizer | 9% | 7% |
| Timeout | 1% | 7% |

## Usage in Cursor

### Manual Pipeline Run

```
1. Open puzzle context file
2. Run /problem-analyzer to understand puzzle
3. Run /theory-evolution random to generate theory
4. Have math-auditor agent review
5. Run /pseudocode-agent to translate
6. Have coding-agent implement
7. Run /evaluator for testing
8. Run /scorer for quantitative assessment
9. Have simulated-referee evaluate
```

### With Python Automation

```python
# In a Python script or notebook:

from autotheory import PipelineOrchestrator

# Initialize
orchestrator = PipelineOrchestrator(
    puzzle_path='puzzles/price_multiplier.yaml',
    model_registry_path='./models'
)

# Single run
result = orchestrator.run_single()
print(f"Score: {result['model'].referee_report['score']}")

# Batch run
batch_result = orchestrator.run_batch(n_iterations=100, max_workers=8)
print(f"Best score: {batch_result['best_score']}")
```

## Implementation Notes

1. **Caching**: Problem analysis is cached per puzzle
2. **Seeding**: Each run has unique seed for reproducibility
3. **Logging**: All stages log to session files
4. **Checkpointing**: Partial results saved for recovery
5. **Resource limits**: Timeout per stage prevents hangs
