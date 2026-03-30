# AI Economics Research Workflow

An integrated AI-assisted workflow for economics research, combining three cutting-edge approaches:

1. **[Sant'Anna's Claude Code Workflow](https://github.com/pedrohcgs/claude-code-my-workflow)** - Academic workflow framework
2. **[AutoTheory](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=6464059)** - LLM-based theory discovery
3. **[Project APE](https://ape.socialcatalystlab.org/)** - Autonomous policy evaluation

## Quick Start

1. Clone this repository
2. Open in Cursor IDE
3. The workflow will auto-load from `.cursor/` directory

## Core Principles

- **Plan-first**: Always plan before non-trivial tasks
- **Verify-after**: Compile/execute and confirm outputs
- **Quality gates**: Nothing ships below 80/100
- **Mathematical rigor**: All derivations must pass audit
- **Adversarial review**: Use tournament system for evaluation

## Directory Structure

```
.
├── AGENTS.md                    # Project constitution
├── MEMORY.md                    # Cross-session persistence
├── .cursor/
│   ├── rules/                   # Auto-loaded rules
│   │   ├── plan-first-workflow.md
│   │   ├── orchestrator-protocol.md
│   │   ├── quality-gates.md
│   │   └── session-logging.md
│   ├── skills/                  # Reusable commands
│   │   ├── data-analysis/
│   │   ├── lit-review/
│   │   ├── review-paper/
│   │   ├── theory-evolution/    # AutoTheory
│   │   ├── tournament/          # Project APE
│   │   └── ...
│   ├── agents/                  # Specialized reviewers
│   │   ├── proofreader.md
│   │   ├── domain-reviewer.md
│   │   ├── math-auditor.md      # AutoTheory
│   │   ├── simulated-referee.md # AutoTheory
│   │   └── tournament-judge.md  # Project APE
│   └── config/
│       └── model-ensemble.yaml  # Multi-model config
├── quality_reports/
│   ├── plans/
│   ├── session_logs/
│   └── specs/
├── scripts/
│   └── R/
└── explorations/
```

## Key Skills

### Core (Sant'Anna)
| Command | Description |
|---------|-------------|
| `/data-analysis` | End-to-end econometric analysis |
| `/lit-review` | Literature search and synthesis |
| `/review-paper` | Full manuscript review |
| `/proofread` | Grammar and consistency check |
| `/commit` | Stage and commit changes |
| `/learn` | Extract discoveries into skills |

### AutoTheory
| Command | Description |
|---------|-------------|
| `/expert-personas` | Activate methodological perspectives |
| `/theory-evolution` | Generate theories (random/mutate/crossover) |

### Project APE
| Command | Description |
|---------|-------------|
| `/tournament` | Head-to-head comparison |
| `/code-review` | Automated code scan |
| `/replicate` | Verify reproducibility |

## Quality Scoring

### AutoTheory Dimensions
- **Fit (50%)**: Empirical target matching
- **Plausibility (25%)**: Parameter reasonableness
- **Parsimony (25%)**: Model simplicity

### Project APE Dimensions
- Identification Strategy (30%)
- Novelty (20%)
- Policy Relevance (20%)
- Execution Quality (20%)
- Appropriate Scope (10%)

## Expert Personas (20 Perspectives)

| Category | Personas |
|----------|----------|
| Formal | mathematician, game_theorist, information_theorist, decision_theorist |
| Empirical | bayesian, econometrician, robust_statistician |
| Fields | macro_theorist, micro_theorist, financial_economist, behavioral, institutional |
| Methods | dynamic_programmer, minimalist, contrarian, physicist, pragmatist |

## Credits

- **Sant'Anna Workflow**: Pedro H.C. Sant'Anna (Emory University)
- **AutoTheory**: Lopez-Lira, Seyfi, Tang (University of Florida / Aalto)
- **Project APE**: Social Catalyst Lab (University of Zurich)

## License

MIT
