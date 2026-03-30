# AGENTS.MD -- AI-Assisted Economics Research Workflow

**Project:** AI Economics Research Workflow
**Framework:** Sant'Anna + AutoTheory + Project APE
**Version:** 1.0

---

## Core Principles

1. **Plan-first** -- enter plan mode before non-trivial tasks; save plans to `quality_reports/plans/`
2. **Verify-after** -- compile/execute and confirm output at the end of every task
3. **Quality gates** -- nothing ships below 80/100 (TrueSkill conservative rating)
4. **Mathematical rigor** -- all derivations must pass mathematical audit
5. **[LEARN] tags** -- when corrected, save `[LEARN:category] wrong → right` to MEMORY.md
6. **Adversarial review** -- use Tournament system for comparative evaluation
7. **Multi-model ensemble** -- leverage specialized models for different tasks

---

## Folder Structure

```
AI研究workflow/
├── AGENTS.MD                    # This file (project constitution)
├── MEMORY.md                    # Cross-session persistence
├── .cursor/                     # Rules, skills, agents, hooks
│   ├── rules/                   # Auto-loaded rules
│   ├── skills/                  # Reusable slash commands
│   ├── agents/                  # Specialized reviewers
│   ├── hooks/                   # Automation triggers
│   ├── config/                  # Model ensemble config
│   └── tournament/              # Tournament evaluation system
├── quality_reports/             # Plans, session logs, specs
│   ├── plans/                   # Saved plans
│   ├── session_logs/            # Session reasoning logs
│   └── specs/                   # Requirements specifications
├── scripts/                     # Utility scripts + analysis code
│   └── R/                       # R scripts for econometrics
├── explorations/                # Research sandbox
└── templates/                   # Document templates
```

---

## Quality Thresholds

| Score | Gate | Meaning |
|-------|------|---------|
| 80 | Commit | Good enough to save |
| 90 | PR | Ready for review |
| 95 | Excellence | Publication-ready |

### Scoring Dimensions (AutoTheory)

| Dimension | Weight | Description |
|-----------|--------|-------------|
| Fit | 50% | Empirical target matching |
| Plausibility | 25% | Parameter reasonableness |
| Parsimony | 25% | Model simplicity |

---

## Skills Quick Reference

### Core Skills (Sant'Anna)

| Command | What It Does |
|---------|-------------|
| `/proofread [file]` | Grammar/typo/consistency review |
| `/review-paper [file]` | Full manuscript review |
| `/data-analysis [dataset]` | End-to-end R analysis |
| `/lit-review [topic]` | Literature search + synthesis |
| `/commit [msg]` | Stage, commit, push |
| `/learn [skill-name]` | Extract discovery into skill |

### AutoTheory Skills

| Command | What It Does |
|---------|-------------|
| `/expert-persona [type]` | Activate methodological perspective |
| `/theory-evolve [strategy]` | Generate theory (random/mutate/crossover) |
| `/math-audit [derivation]` | Verify mathematical derivations |
| `/calibrate [model]` | Optimize parameters against targets |

### Project APE Skills

| Command | What It Does |
|---------|-------------|
| `/tournament [paper1] [paper2]` | Head-to-head comparison |
| `/code-review [script]` | Automated code scan |
| `/replicate [analysis]` | Verify reproducibility |

---

## Expert Personas (AutoTheory)

20 methodological perspectives for diverse theory generation:

| Persona | Focus |
|---------|-------|
| mathematician | Fixed-point theorems, duality |
| bayesian | Priors, posteriors, value of information |
| behavioral | Bounded rationality, heuristics |
| game_theorist | Strategic interactions, equilibria |
| econometrician | Identification, estimation |
| macro_theorist | General equilibrium, dynamics |
| micro_theorist | Individual optimization |
| minimalist | Fewest assumptions possible |
| contrarian | Challenge conventional wisdom |
| physicist | Conservation laws, symmetries |

---

## Tournament Evaluation (Project APE)

### Evaluation Dimensions

1. **Identification Strategy** - Is the causal inference credible?
2. **Novelty** - Does it contribute new insights?
3. **Policy Relevance** - Does it matter for real-world decisions?
4. **Execution Quality** - Is the analysis well-executed?
5. **Appropriate Scope** - Is the scope well-calibrated?

### TrueSkill Rating

- **μ (mu)**: Estimated skill level
- **σ (sigma)**: Uncertainty
- **Conservative Rating**: μ - 3σ (used for ranking)

---

## Current Project State

| Component | Status | Description |
|-----------|--------|-------------|
| Core Framework | ✅ | Sant'Anna workflow adapted |
| AutoTheory | 🔄 | Expert personas, math audit |
| Project APE | 🔄 | Tournament, code review |
