# AGENTS.MD -- AI-Assisted Economics Research Workflow

**Project:** AI Economics Research Workflow  
**Framework:** Sant'Anna + AutoTheory + Project APE  
**Version:** 3.0 (完整 Pipeline + APE Integration)

---

## Framework Architecture (Three-Layer Integration)

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    AI Economics Research Workflow                        │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │                    Layer 1: Sant'Anna Core                       │   │
│  │   - AGENTS.MD / MEMORY.md (persistent context)                  │   │
│  │   - Plan-first workflow                                          │   │
│  │   - Quality gates (80/90/95 thresholds)                         │   │
│  │   - Session logging                                              │   │
│  │   - Skills & Rules system                                        │   │
│  └─────────────────────────────────────────────────────────────────┘   │
│                              │                                          │
│                              ▼                                          │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │                    Layer 2: AutoTheory Engine                    │   │
│  │   - 11-stage pipeline (Problem→Scorer)                          │   │
│  │   - Evolutionary search (Random/Mutate/Crossover)               │   │
│  │   - Math Auditor (PASS/FAIL verification)                       │   │
│  │   - 20 expert personas                                          │   │
│  │   - Quantitative scoring (Fit/Plausibility/Parsimony)           │   │
│  └─────────────────────────────────────────────────────────────────┘   │
│                              │                                          │
│                              ▼                                          │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │                    Layer 3: APE Evaluation                       │   │
│  │   - 6-stage review (Advisor→Revision)                           │   │
│  │   - Tournament system (TrueSkill rating)                        │   │
│  │   - Code integrity checks                                       │   │
│  │   - Multi-model ensemble (no self-evaluation)                   │   │
│  │   - Pre-analysis plan compliance                                │   │
│  └─────────────────────────────────────────────────────────────────┘   │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Core Principles

1. **Plan-first** -- enter plan mode before non-trivial tasks; save plans to `quality_reports/plans/`
2. **Verify-after** -- compile/execute and confirm output at the end of every task
3. **Quality gates** -- nothing ships below 80/100 (see Scoring section)
4. **Mathematical rigor** -- all derivations must pass Math Auditor (PASS/FAIL)
5. **No fudge factors** -- all outputs must be derived from economic primitives
6. **[LEARN] tags** -- when corrected, save `[LEARN:category] wrong → right` to MEMORY.md
7. **Evolutionary search** -- Random/Mutate/Crossover strategies for theory generation

---

## Complete AutoTheory Pipeline (11 Stages)

```
┌─────────────────────────────────────────────────────────────────┐
│                    AutoTheory Pipeline                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  1. Problem Analyzer ─────────────────────────────────────────► │
│         │                                                       │
│         ▼                                                       │
│  2. Theory Evolution ──────────────────┐                        │
│     (Random 35% / Mutate 35% / Crossover 30%)                   │
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
│     (Robustness + scipy.optimize)      │                        │
│         │ PASS                         │                        │
│         ▼                              │                        │
│  10. Scorer                            │                        │
│      (Fit 50% + Plausibility 25% + Parsimony 25%)              │
│         │                              │                        │
│         ▼                              │                        │
│  11. Simulated Referee ────────────────┘                        │
│         │                                                       │
│         ▼                                                       │
│     Model Registry ◄─────── Parent Selection (score ≥ 35)       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Folder Structure

```
AI研究workflow/
├── AGENTS.MD                    # This file (project constitution)
├── MEMORY.md                    # Cross-session persistence
├── README.md                    # Project documentation (bilingual)
├── .cursor/
│   ├── rules/                   # Auto-loaded rules
│   │   ├── plan-first-workflow.md
│   │   ├── orchestrator-protocol.md
│   │   ├── quality-gates.md
│   │   └── session-logging.md
│   ├── skills/                  # Reusable slash commands
│   │   │                        # === AutoTheory Skills ===
│   │   ├── problem-analyzer/    # A.1 - Puzzle analysis
│   │   ├── theory-evolution/    # A.2 - Theory generation
│   │   ├── scorer/              # A.10 - Quantitative scoring
│   │   ├── evaluator/           # A.9 - Robustness + optimization
│   │   ├── pipeline-orchestrator/ # C - Full pipeline coordination
│   │   ├── expert-personas/     # 20 methodological perspectives
│   │   │                        # === APE Skills ===
│   │   ├── ape-review-process/  # [NEW] 6-stage review pipeline
│   │   ├── tournament/          # [UPDATED] TrueSkill + APE judge
│   │   ├── integrity-check/     # [NEW] Code scan, replication
│   │   │                        # === Core Skills ===
│   │   ├── data-analysis/
│   │   ├── lit-review/
│   │   ├── review-paper/
│   │   ├── code-review/
│   │   ├── replicate/
│   │   ├── proofread/
│   │   ├── commit/
│   │   └── learn/
│   ├── agents/                  # Specialized reviewers
│   │   │                        # === AutoTheory Agents ===
│   │   ├── math-auditor.md      # A.5 - Math verification
│   │   ├── simulated-referee.md # A.11 - Peer review
│   │   ├── pseudocode-agent.md  # A.6 - Pseudocode translation
│   │   ├── pseudocode-reviewer.md # A.7 - Pseudocode verification
│   │   ├── coding-agent.md      # A.3 - Python implementation
│   │   ├── code-reviewer.md     # A.8 - Code verification
│   │   ├── calibration-agent.md # A.4 - Parameter specification
│   │   │                        # === APE Agents ===
│   │   └── tournament-judge.md  # [UPDATED] APE verbatim prompts
│   ├── prompts/                 # Original prompts from papers
│   │   ├── autotheory-prompts.md # All AutoTheory prompts (Appendix A)
│   │   └── ape-prompts.md       # [NEW] All APE prompts & schemas
│   └── config/
│       └── model-ensemble.yaml
├── quality_reports/
│   ├── plans/
│   ├── session_logs/
│   └── specs/
├── models/                      # Model registry storage
├── scripts/
└── templates/
    └── ape-paper/               # [NEW] APE paper structure templates
        ├── PROMPT.md            # Research question definition
        ├── pre_analysis.md      # Pre-analysis plan template
        ├── ideas.md             # Research ideas template
        ├── metadata.json        # Paper status schema
        └── REPLICATION.md       # Reproducibility instructions
```

---

## Scoring System (Paper A.10)

### Score Components

| Component | Weight | Formula |
|-----------|--------|---------|
| **Fit** | 50% | `100 × exp(-ē/20)` where ē = mean % error |
| **Plausibility** | 25% | `(valid_params/total_params) × 100 - penalty` |
| **Parsimony** | 25% | `max(0, 100 - 10 × n_free_params)` |

### Fit Score Reference

| Avg Error | Score |
|-----------|-------|
| 0% | 100 |
| 10% | 61 |
| 20% | 37 |
| 50% | 8 |

### Parsimony Score Reference

| Params | Score |
|--------|-------|
| 1 | 90 |
| 2 | 80 |
| 3 | 70 |
| 5 | 50 |
| 10+ | 0 |

### Quality Thresholds

| Score | Gate | Recommendation |
|-------|------|----------------|
| 90-100 | Excellence | Accept |
| 80-89 | PR Ready | Minor Revision |
| 60-79 | Commit OK | Major Revision |
| 40-59 | Review | Revise & Resubmit |
| <40 | Reject | Fundamental issues |

---

## Skills Quick Reference

### Pipeline Skills (AutoTheory)

| Command | Stage | Description |
|---------|-------|-------------|
| `/problem-analyzer [puzzle]` | 1 | Analyze empirical puzzle |
| `/theory-evolution [strategy]` | 2 | Generate theory (random/mutate/crossover) |
| `/expert-personas [type]` | 2 | Activate methodological perspective |
| `/scorer [outputs] [targets]` | 10 | Compute quantitative scores |
| `/evaluator [code] [calibration]` | 9 | Robustness + optimization |
| `/pipeline [puzzle]` | All | Run complete pipeline |

### Agent Commands

| Agent | Stage | What It Does |
|-------|-------|--------------|
| math-auditor | 3 | Verify mathematical derivations → PASS/FAIL |
| pseudocode-agent | 4 | Translate theory to structured pseudocode |
| pseudocode-reviewer | 5 | Verify pseudocode fidelity → PASS/FAIL |
| coding-agent | 6 | Translate pseudocode to Python |
| code-reviewer | 7 | Verify code matches pseudocode → PASS/FAIL |
| calibration-agent | 8 | Extract parameter bounds and defaults |
| simulated-referee | 11 | Full peer review with feedback |

### APE Skills

| Command | Description |
|---------|-------------|
| `/ape-review [paper]` | Run 6-stage review pipeline |
| `/ape-review --stage [stage] [paper]` | Run specific stage (advisor/theory/exhibits/prose/referee/revision) |
| `/tournament [paper1] [paper2]` | TrueSkill head-to-head comparison |
| `/integrity-check [paper_dir]` | Code scan + data verify + replication |
| `/integrity-check --quick [dir]` | Quick code scan only |

### Core Skills (Sant'Anna)

| Command | Description |
|---------|-------------|
| `/data-analysis [dataset]` | End-to-end R analysis |
| `/lit-review [topic]` | Literature search + synthesis |
| `/review-paper [file]` | Full manuscript review |
| `/code-review [script]` | Automated code scan |
| `/replicate [analysis]` | Verify reproducibility |
| `/proofread [file]` | Grammar and style check |
| `/commit [message]` | Git commit with review |

---

## Theory Evolution Strategies

### Strategy Selection

```
Before any model: 100% Random
After first model:
  - Random: 35%
  - Mutate: 35%
  - Crossover: 30%
```

### Parent Selection (for Mutate/Crossover)

```
1. Filter to referee_score >= 35
2. Take top 10 by score
3. Select with probability ∝ score
```

### 20 Expert Personas

| Category | Personas |
|----------|----------|
| **Formal** | mathematician, game_theorist, information_theorist, decision_theorist |
| **Empirical** | bayesian, econometrician, robust_statistician |
| **Field** | macro_theorist, micro_theorist, financial_economist, behavioral, institutional, monetary_economist, network_theorist |
| **Methodological** | dynamic_programmer, minimalist, contrarian, physicist, pragmatist |

---

## Retry Configuration

| Stage | Type | Max Attempts |
|-------|------|--------------|
| LLM API | Exponential backoff | 3 (1s, 2s, 4s) |
| Math Auditor | Fix-and-reaudit | 1 + 2 fixes |
| Pseudocode | Generate + review | 1 + 2 retries |
| Coding | Fresh × fixes | 2 × 3 = 6 |
| Code Review | Review + retry | 1 + 1 |

---

## Pipeline Statistics (From Paper)

### Price Multiplier Puzzle

| Metric | Value |
|--------|-------|
| Invocations | 1,000 |
| Success Rate | 46% |
| Math Audit Reject | 41% |
| Best Score | 84 |
| Mean Score | 48.5 |

### Dividend Strip Puzzle

| Metric | Value |
|--------|-------|
| Invocations | 2,153 |
| Success Rate | 12% |
| Math Audit Reject | 66% |
| Best Score | 58 |
| Mean Score | 27.8 |

---

## APE Integration (Project APE - https://ape.socialcatalystlab.org/)

### APE 6-Stage Review Process

```
┌─────────────────────────────────────────────────────────────────┐
│                    APE Review Pipeline                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Stage 1: Advisor Review (4 models, 3/4 must pass)             │
│           → Format check + Content check                        │
│                          ↓                                      │
│  Stage 2: Theory Review (if structural models/proofs)          │
│           → Model consistency, math correctness                 │
│                          ↓                                      │
│  Stage 3: Exhibit Review (iterative until all approved)        │
│           → Figures, tables publication quality                 │
│                          ↓                                      │
│  Stage 4: Prose Review (iterative)                             │
│           → Clarity, precision, academic tone                   │
│                          ↓                                      │
│  Stage 5: Referee Review (3 independent referees)              │
│           → Full journal-style peer review                      │
│                          ↓                                      │
│  Stage 6: Revision                                             │
│           → Response to referees, all comments addressed        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### APE Tournament System

| Component | Description |
|-----------|-------------|
| Judge Model | Gemini 2.5 Flash Lite (distinct from paper generator) |
| Match Protocol | 2 rounds with position swap, must win both to win |
| Rating System | TrueSkill (μ=25, σ=8.33) |
| Ranking | Conservative rating: μ - 3σ |
| Criteria | Identification (30%), Novelty (20%), Policy (20%), Execution (20%), Scope (10%) |

### APE Integrity Checks

| Check | What It Does |
|-------|--------------|
| Code Scan | Detect hardcoded results, fabrication, p-hacking |
| Data Verify | Checksums, source documentation |
| Replication | Run code from scratch, compare outputs |
| Pre-Analysis | Verify compliance with pre-registered plan |

### APE Paper Template Structure

```
paper_directory/
├── PROMPT.md           # Research question definition
├── pre_analysis.md     # Pre-registered analysis plan (SHA-256)
├── ideas.md            # Generated research ideas
├── metadata.json       # Paper status, tournament results
├── REPLICATION.md      # Reproducibility instructions
├── code/               # All analysis scripts
├── data/               # Raw and processed data
│   ├── manifest.json   # File checksums
│   └── README.md       # Data documentation
└── output/             # Results, figures, tables
```

---

## Current Project State

| Component | Status | Description |
|-----------|--------|-------------|
| **Sant'Anna Core** | | |
| Core Framework | ✅ | AGENTS.md, MEMORY.md, Rules |
| Skills System | ✅ | Reusable slash commands |
| Quality Gates | ✅ | 80/90/95 thresholds |
| **AutoTheory Engine** | | |
| Problem Analyzer | ✅ | A.1 implemented |
| Theory Evolution | ✅ | A.2 with exact prompts |
| Math Auditor | ✅ | A.5 with PASS/FAIL |
| Pseudocode Agent | ✅ | A.6 implemented |
| Pseudocode Reviewer | ✅ | A.7 implemented |
| Coding Agent | ✅ | A.3 implemented |
| Code Reviewer | ✅ | A.8 implemented |
| Calibration Agent | ✅ | A.4 implemented |
| Evaluator | ✅ | A.9 with scipy.optimize |
| Scorer | ✅ | A.10 exact formulas |
| Simulated Referee | ✅ | A.11 with feedback loop |
| Pipeline Orchestrator | ✅ | Complete coordination |
| **APE Evaluation** | | |
| Tournament System | ✅ | TrueSkill + position swap |
| Tournament Judge | ✅ | APE verbatim prompts |
| 6-Stage Review | ✅ | Full review pipeline |
| Integrity Checks | ✅ | Code scan, replication |
| Paper Templates | ✅ | PROMPT, pre_analysis, metadata |
| APE Prompts | ✅ | All prompts documented |

---

## Key Differences from Original Paper

| Aspect | Paper | This Implementation |
|--------|-------|---------------------|
| Execution | 128 parallel workers | Manual or small-scale parallel |
| LLM | gpt-oss-120b | Cursor's model |
| Iterations | 1,000-2,153 | User-controlled |
| Automation | Fully automated Python | Skills + Agents in Cursor |

To achieve paper-level results, you need:
1. Large-scale iteration (100+ runs)
2. Automated pipeline orchestration
3. Persistent model registry
