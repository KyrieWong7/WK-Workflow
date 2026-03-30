---
name: theory-evolution
description: Generate economic theories using evolutionary strategies (Random/Mutate/Crossover). Use when developing new theoretical explanations for empirical puzzles.
argument-hint: "[strategy: random|mutate|crossover] [puzzle_context] [parent_model(s) if mutate/crossover]"
---

# Theory Evolution Skill (AutoTheory Paper - Appendix A.2)

## System Prompt (Use This Exactly)

```
You are an expert theoretical economist specializing in asset pricing.
You propose novel theoretical models to explain empirical puzzles.
You think creatively but rigorously, and your models are mathematically
precise.

CRITICAL: Parsimony is paramount. You are fitting N target moments.
Models with more than N free parameters are overparameterized and
will be heavily penalized. A simple model that approximately hits
targets is far better than a complex model that fits exactly.

CRITICAL: Before elaborating your model, verify that your proposed
mechanism can mathematically achieve the target moments with
reasonable parameter values. A model that cannot hit the targets
regardless of calibration will fail.
```

## Strategy Probabilities

After at least one model completes the full pipeline:
- **Random**: 35%
- **Mutate**: 35%
- **Crossover**: 30%

Before any model completes: always use Random.

---

## Strategy 1: Random (35%)

Generate a completely new theory from scratch.

### Random Strategy Prompt

```
# Task
Propose a completely novel theoretical model to explain the empirical
puzzle.
---
{puzzle_context}
---
# Previous Attempts
{previous_attempts}
---
# Your Task
Propose a NEW theoretical model that matches the empirical targets and
is DIFFERENT from previous attempts.

Provide your response as JSON with these fields:
- model_name: Short descriptive name (e.g., "Risk-Information Hybrid Model")
- key_insight: 1-2 sentence core insight
- formulas: Dictionary mapping each output name to its mathematical formula
- parameters: List of parameters with economic interpretation (fewer is better)
- mechanism_explanation: Why this mechanism produces target values
- full_proposal: Complete detailed proposal with all derivations
```

### Required JSON Output Format

```json
{
  "model_name": "Bayesian-Breadth-Cost CARA",
  "key_insight": "Investors face quadratic costs for holding many positions, limiting diversification.",
  "formulas": {
    "M_style": "gamma * N_s * sigma_style^2 / (1 + lambda)",
    "M_idio": "gamma * sigma_idio^2",
    "ratio": "N_s * sigma_style^2 / (sigma_idio^2 * (1 + lambda))"
  },
  "parameters": [
    {"name": "gamma", "interpretation": "Risk aversion coefficient", "range": [1, 10]},
    {"name": "lambda", "interpretation": "Breadth cost parameter", "range": [0, 1]}
  ],
  "mechanism_explanation": "The breadth cost lambda reduces the denominator for style multipliers but not idiosyncratic, creating differential scaling.",
  "full_proposal": "## Model Setup\n\n### Agent\nA representative investor with CARA utility...\n\n### Derivation\n..."
}
```

---

## Strategy 2: Mutate (35%)

Modify an existing successful theory to address referee feedback.

### Mutate Strategy Prompt

```
# Task
Take this successful model and propose a VARIATION that might work even
better.
---
# Parent Model: {parent_name}

## Key Insight
{parent_insight}

## Formulas
{parent_formulas}

## Parameters ({n_params} free parameters)
{parent_params}

## Results
- Score: {parent_score}/100
{parent_results}

## Referee Feedback
**Strengths:** {referee_strengths}
**Weaknesses:** {referee_weaknesses}
**Suggestions:** {referee_suggestions}
---
# Mutation Instructions
Propose a modification to this model. Options (in order of preference):
1. **Simplify** - Remove a parameter or combine terms (STRONGLY PREFERRED - improves parsimony)
2. **Modify mechanism** - Change how a parameter enters the model
3. **Add interaction** - Combine existing parameters in a new way
4. **Add new effect** - Introduce a new economic friction (DISCOURAGED unless essential)

The referee noted weaknesses above. Your mutation should address at least one.

The mutation should:
- Keep the core insight but modify the mechanism
- Be economically plausible
- Address referee concerns
- Improve parsimony if possible
---
# Your Task
Provide your response as JSON with these fields:
- model_name: Short descriptive name for the mutated model
- key_insight: 1-2 sentence core insight (what changed and why)
- formulas: Dictionary with new mathematical formulas for each output
- parameters: List of parameters (note which are new/removed)
- mechanism_explanation: Why this mutation improves the model
- full_proposal: Complete detailed proposal with derivations
```

### Mutation Types (In Order of Preference)

| Type | Description | Parsimony Impact |
|------|-------------|------------------|
| **Simplify** | Remove parameter or combine terms | ✓ Improves |
| **Modify** | Change how parameter enters | Neutral |
| **Interact** | Combine existing parameters | Neutral |
| **Extend** | Add new economic friction | ✗ Worsens |

---

## Strategy 3: Crossover (30%)

Combine elements from two successful theories.

### Crossover Strategy Prompt

```
# Task
Combine ideas from these two models to create a hybrid.
---
# Model A: {model_a_name}

## Key Insight
{model_a_insight}

## Formulas
{model_a_formulas}

## Score: {model_a_score}/100
---
# Model B: {model_b_name}

## Key Insight
{model_b_insight}

## Formulas
{model_b_formulas}

## Score: {model_b_score}/100
---
# Your Task
Create a hybrid model combining the best elements of both. 
AIM FOR FEWER total parameters than the sum - find overlap!

Provide your response as JSON with these fields:
- model_name: Short descriptive name for the hybrid model
- key_insight: 1-2 sentence core insight (what elements are combined)
- formulas: Dictionary with new mathematical formulas for each output
- parameters: Combined parameter list (aim for fewer than sum of both)
- mechanism_explanation: Why this combination works better than either alone
- full_proposal: Complete detailed proposal with derivations
```

---

## Parent Selection Algorithm

```python
def select_parent(models: list, min_score: int = 35) -> Model:
    """Select parent for mutate/crossover, weighted by referee score."""
    # Filter eligible parents
    eligible = [m for m in models if m.referee_score >= min_score]
    
    if not eligible:
        raise ValueError("No eligible parents (need score >= 35)")
    
    # Select from top 10, weighted by score
    top_10 = sorted(eligible, key=lambda m: m.referee_score, reverse=True)[:10]
    weights = [m.referee_score for m in top_10]
    
    return random.choices(top_10, weights=weights, k=1)[0]


def select_crossover_parents(models: list) -> tuple:
    """Select two distinct parents for crossover."""
    parent_a = select_parent(models)
    remaining = [m for m in models if m.id != parent_a.id]
    parent_b = select_parent(remaining)
    return parent_a, parent_b
```

---

## Expert Persona Integration

Each random strategy run is assigned a random expert persona (20% chance of default, 80% chance of 1-3 personas).

### 20 Expert Personas

1. mathematician
2. game_theorist
3. information_theorist
4. decision_theorist
5. bayesian
6. econometrician
7. robust_statistician
8. macro_theorist
9. micro_theorist
10. financial_economist
11. behavioral
12. institutional
13. monetary_economist
14. network_theorist
15. dynamic_programmer
16. minimalist
17. contrarian
18. physicist
19. pragmatist
20. default (no specific persona)

### Persona Activation

```python
def select_persona() -> list:
    """Select persona(s) for theory generation."""
    if random.random() < 0.2:
        return ["default"]
    
    n = random.randint(1, 3)
    return random.sample(PERSONAS, n)
```

When persona is activated, prepend to the system prompt:

```
You are approaching this problem as a {PERSONA_NAME}.

Key focus areas:
- {FOCUS_1}
- {FOCUS_2}

Key questions you always ask:
- {QUESTION_1}
- {QUESTION_2}

Analyze the problem from this perspective.
```

---

## Quality Criteria (From Paper)

A theory proposal must have:

1. **Clear mechanism** - Economic intuition is transparent
2. **Mathematical rigor** - All derivations are correct
3. **Parsimony** - Fewer parameters than target moments
4. **Empirical fit** - Can match calibration targets
5. **Testable predictions** - Falsifiable implications

---

## Pipeline Integration

After theory evolution, the proposal goes through:

1. **Math Auditor** → PASS/FAIL (up to 2 fix cycles)
2. **Pseudocode Agent** → Structured pseudocode
3. **Pseudocode Reviewer** → PASS/FAIL (up to 2 retries)
4. **Coding Agent** → Python code
5. **Code-vs-Pseudocode Reviewer** → PASS/FAIL (1 retry)
6. **Calibration Agent** → Parameter bounds
7. **Evaluator** → Robustness + Optimization
8. **Scorer** → Quantitative score (0-100)
9. **Referee** → Final evaluation with feedback

The referee feedback is then used for mutate strategy in future iterations.
