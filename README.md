# WK-Workflow: AI 经济学研究工作流 | AI Economics Research Workflow

[English](#english) | [中文](#中文)

---

<a name="中文"></a>
## 中文

### 概述

一个整合三大前沿方法的 AI 辅助经济学研究工作流：

| 来源 | 项目 | 核心贡献 |
|------|------|----------|
| **Sant'Anna** | [claude-code-my-workflow](https://github.com/pedrohcgs/claude-code-my-workflow) | 学术工作流框架 |
| **AutoTheory** | [Lopez-Lira et al. (2026)](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=6464059) | LLM 理论发现系统 |
| **Project APE** | [ape.socialcatalystlab.org](https://ape.socialcatalystlab.org/) | 自主政策评估系统 |

### 框架架构图

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         WK-Workflow 整体架构                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐           │
│  │   Sant'Anna     │   │   AutoTheory    │   │   Project APE   │           │
│  │   基础框架       │   │   理论发现       │   │   评估系统       │           │
│  └────────┬────────┘   └────────┬────────┘   └────────┬────────┘           │
│           │                     │                     │                     │
│           ▼                     ▼                     ▼                     │
│  ┌─────────────────────────────────────────────────────────────────┐       │
│  │                        配置层 (Configuration)                    │       │
│  │  ┌───────────┐  ┌───────────┐  ┌───────────┐  ┌───────────┐    │       │
│  │  │ AGENTS.md │  │ MEMORY.md │  │  Rules/   │  │  Config/  │    │       │
│  │  │ 项目宪法   │  │ 跨会话记忆 │  │  规则文件  │  │ 模型配置   │    │       │
│  │  └───────────┘  └───────────┘  └───────────┘  └───────────┘    │       │
│  └─────────────────────────────────────────────────────────────────┘       │
│                                    │                                        │
│                                    ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────┐       │
│  │                        执行层 (Execution)                        │       │
│  │                                                                  │       │
│  │  ┌─────────────────────────────────────────────────────────┐   │       │
│  │  │                    Skills 技能 (11个)                     │   │       │
│  │  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐       │   │       │
│  │  │  │ data-   │ │ lit-    │ │ review- │ │ theory- │ ...   │   │       │
│  │  │  │ analysis│ │ review  │ │ paper   │ │evolution│       │   │       │
│  │  │  └─────────┘ └─────────┘ └─────────┘ └─────────┘       │   │       │
│  │  └─────────────────────────────────────────────────────────┘   │       │
│  │                                                                  │       │
│  │  ┌─────────────────────────────────────────────────────────┐   │       │
│  │  │                    Agents 智能体 (5个)                    │   │       │
│  │  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐       │   │       │
│  │  │  │ proof-  │ │ domain- │ │ math-   │ │simulated│ ...   │   │       │
│  │  │  │ reader  │ │ reviewer│ │ auditor │ │ referee │       │   │       │
│  │  │  └─────────┘ └─────────┘ └─────────┘ └─────────┘       │   │       │
│  │  └─────────────────────────────────────────────────────────┘   │       │
│  └─────────────────────────────────────────────────────────────────┘       │
│                                    │                                        │
│                                    ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────┐       │
│  │                        质量层 (Quality)                          │       │
│  │  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐       │       │
│  │  │  Quality Gates │  │  Tournament   │  │  TrueSkill    │       │       │
│  │  │  80/90/95 评分  │  │  头对头比赛    │  │  贝叶斯评级    │       │       │
│  │  └───────────────┘  └───────────────┘  └───────────────┘       │       │
│  └─────────────────────────────────────────────────────────────────┘       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 工作流程图

```
┌──────────────────────────────────────────────────────────────────────────┐
│                            研究工作流程                                    │
└──────────────────────────────────────────────────────────────────────────┘

     用户输入研究任务
            │
            ▼
    ┌───────────────┐
    │  Plan-First   │ ◄─── 规则: plan-first-workflow.md
    │  先计划后执行   │
    └───────┬───────┘
            │
            ▼
    ┌───────────────┐
    │  Orchestrator │ ◄─── 规则: orchestrator-protocol.md
    │  编排器协调    │
    └───────┬───────┘
            │
            ▼
    ┌───────────────────────────────────────────────────┐
    │                    执行循环                        │
    │  ┌─────────┐  ┌─────────┐  ┌─────────┐          │
    │  │ IMPLEMENT│─▶│ VERIFY  │─▶│ REVIEW  │          │
    │  │ 实现     │  │ 验证    │  │ 审查    │          │
    │  └─────────┘  └─────────┘  └────┬────┘          │
    │                                  │               │
    │  ┌─────────┐  ┌─────────┐  ┌────▼────┐          │
    │  │ SCORE   │◄─│RE-VERIFY│◄─│  FIX    │          │
    │  │ 评分    │  │ 重新验证 │  │ 修复    │          │
    │  └────┬────┘  └─────────┘  └─────────┘          │
    └───────┼───────────────────────────────────────────┘
            │
            ▼
      Score >= 80?
      ┌────┴────┐
      │         │
     YES       NO
      │         │
      ▼         ▼
   完成输出    继续循环
              (最多5轮)
```

### 目录结构

```
WK-Workflow/
├── AGENTS.md                    # 项目宪法
├── MEMORY.md                    # 跨会话记忆
├── README.md                    # 本文件
├── .cursor/
│   ├── rules/                   # 规则 (4个)
│   │   ├── plan-first-workflow.md
│   │   ├── orchestrator-protocol.md
│   │   ├── quality-gates.md
│   │   └── session-logging.md
│   ├── skills/                  # 技能 (11个)
│   │   ├── data-analysis/       # 数据分析
│   │   ├── lit-review/          # 文献综述
│   │   ├── review-paper/        # 论文评审
│   │   ├── proofread/           # 校对
│   │   ├── commit/              # 提交
│   │   ├── learn/               # 学习
│   │   ├── expert-personas/     # 专家人格 (AutoTheory)
│   │   ├── theory-evolution/    # 理论进化 (AutoTheory)
│   │   ├── tournament/          # 锦标赛 (Project APE)
│   │   ├── code-review/         # 代码审查 (Project APE)
│   │   └── replicate/           # 复制验证 (Project APE)
│   ├── agents/                  # 智能体 (5个)
│   │   ├── proofreader.md       # 校对员
│   │   ├── domain-reviewer.md   # 领域审查员
│   │   ├── math-auditor.md      # 数学审计员 (AutoTheory)
│   │   ├── simulated-referee.md # 模拟审稿人 (AutoTheory)
│   │   └── tournament-judge.md  # 锦标赛裁判 (Project APE)
│   └── config/
│       └── model-ensemble.yaml  # 多模型配置 (综合*)
└── quality_reports/             # 质量报告
```

### 核心组件说明

#### 来自 Sant'Anna 的组件

| 组件 | 说明 |
|------|------|
| **Plan-First Workflow** | 非平凡任务先进入计划模式 |
| **Orchestrator Protocol** | 计划批准后自动协调执行 |
| **Quality Gates** | 80分提交，90分PR，95分卓越 |
| **Session Logging** | 记录决策原因，而非仅记录变更 |

#### 来自 AutoTheory 论文的组件

| 组件 | 说明 | 论文原文 |
|------|------|----------|
| **20 专家人格** | 多样化方法论视角 | "20 methodological perspectives (e.g., bayesian, behavioral, physicist, minimalist)" |
| **三种进化策略** | Random (35%), Mutate (35%), Crossover (30%) | "random... mutate... crossover" |
| **数学审计** | 独立验证推导正确性 | "independent mathematical audit" |
| **评分维度** | Fit (50%), Plausibility (25%), Parsimony (25%) | "fit (50%)... plausibility (25%)... parsimony (25%)" |
| **模拟审稿** | 模拟顶级期刊审稿人 | "referee LLM produces a final score and feedback" |

#### 来自 Project APE 的组件

| 组件 | 说明 | 原始来源 |
|------|------|----------|
| **Tournament 系统** | 头对头比赛评估 | "Papers compete in head-to-head matches" |
| **TrueSkill 评级** | 微软贝叶斯评级系统 | "Rankings use TrueSkill" |
| **位置交换** | 控制 LLM 位置偏差 | "run each comparison twice with papers swapped" |
| **5个评估维度** | 识别策略、新颖性、政策相关性、执行质量、适当范围 | 来自评估提示 |

#### 本项目综合扩展 (*)

| 组件 | 说明 |
|------|------|
| **各人格的"关键问题"** | 基于人格特点扩展的启发式问题 |
| **model-ensemble.yaml** | 基于 APE 多模型理念的综合配置 |

### 使用方法

#### 1. 克隆仓库
```bash
git clone https://github.com/KyrieWong7/WK-Workflow.git
cd WK-Workflow
```

#### 2. 在 Cursor 中打开
工作流会自动从 `.cursor/` 目录加载。

#### 3. 使用技能命令

**核心技能 (Sant'Anna):**
```
/data-analysis [数据集]    # 端到端计量分析
/lit-review [主题]         # 文献搜索与综合
/review-paper [文件]       # 论文评审
/proofread [文件]          # 校对
/commit [消息]             # 提交
/learn [技能名]            # 提取发现为技能
```

**AutoTheory 技能:**
```
/expert-personas [类型]    # 激活专家人格
/theory-evolution [策略]   # 理论进化 (random/mutate/crossover)
```

**Project APE 技能:**
```
/tournament [论文1] [论文2] # 头对头比较
/code-review [脚本]        # 代码审查
/replicate [分析]          # 复制验证
```

### 质量评分体系

#### AutoTheory 评分维度
| 维度 | 权重 | 说明 |
|------|------|------|
| Fit | 50% | 模型输出与实证目标的匹配度 |
| Plausibility | 25% | 参数是否在经济学合理范围内 |
| Parsimony | 25% | 自由参数越少越好 |

#### Project APE 评估维度
| 维度 | 权重 | 说明 |
|------|------|------|
| Identification Strategy | 30% | 因果推断是否可信 |
| Novelty | 20% | 是否有新颖贡献 |
| Policy Relevance | 20% | 是否与政策相关 |
| Execution Quality | 20% | 分析执行质量 |
| Appropriate Scope | 10% | 范围是否适当 |

### 致谢

- **Sant'Anna Workflow**: Pedro H.C. Sant'Anna (Emory University)
- **AutoTheory**: Alejandro Lopez-Lira, Sina Seyfi, Yuehua Tang (University of Florida / Aalto University)
- **Project APE**: Social Catalyst Lab (University of Zurich)

---

<a name="english"></a>
## English

### Overview

An integrated AI-assisted workflow for economics research, combining three cutting-edge approaches:

| Source | Project | Core Contribution |
|--------|---------|-------------------|
| **Sant'Anna** | [claude-code-my-workflow](https://github.com/pedrohcgs/claude-code-my-workflow) | Academic workflow framework |
| **AutoTheory** | [Lopez-Lira et al. (2026)](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=6464059) | LLM-based theory discovery |
| **Project APE** | [ape.socialcatalystlab.org](https://ape.socialcatalystlab.org/) | Autonomous policy evaluation |

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         WK-Workflow Architecture                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐           │
│  │   Sant'Anna     │   │   AutoTheory    │   │   Project APE   │           │
│  │   Framework     │   │ Theory Discovery│   │   Evaluation    │           │
│  └────────┬────────┘   └────────┬────────┘   └────────┬────────┘           │
│           │                     │                     │                     │
│           ▼                     ▼                     ▼                     │
│  ┌─────────────────────────────────────────────────────────────────┐       │
│  │                     Configuration Layer                          │       │
│  │  ┌───────────┐  ┌───────────┐  ┌───────────┐  ┌───────────┐    │       │
│  │  │ AGENTS.md │  │ MEMORY.md │  │  Rules/   │  │  Config/  │    │       │
│  │  │Constitution│  │  Memory   │  │   Rules   │  │Model Conf │    │       │
│  │  └───────────┘  └───────────┘  └───────────┘  └───────────┘    │       │
│  └─────────────────────────────────────────────────────────────────┘       │
│                                    │                                        │
│                                    ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────┐       │
│  │                       Execution Layer                            │       │
│  │  ┌─────────────────────────────────────────────────────────┐   │       │
│  │  │                    Skills (11 total)                      │   │       │
│  │  │  data-analysis, lit-review, review-paper, theory-evolution│   │       │
│  │  │  tournament, code-review, replicate, expert-personas...   │   │       │
│  │  └─────────────────────────────────────────────────────────┘   │       │
│  │  ┌─────────────────────────────────────────────────────────┐   │       │
│  │  │                    Agents (5 total)                       │   │       │
│  │  │  proofreader, domain-reviewer, math-auditor,              │   │       │
│  │  │  simulated-referee, tournament-judge                      │   │       │
│  │  └─────────────────────────────────────────────────────────┘   │       │
│  └─────────────────────────────────────────────────────────────────┘       │
│                                    │                                        │
│                                    ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────┐       │
│  │                        Quality Layer                             │       │
│  │  Quality Gates (80/90/95) │ Tournament │ TrueSkill Rating       │       │
│  └─────────────────────────────────────────────────────────────────┘       │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Workflow Diagram

```
User Input → Plan-First → Orchestrator → Execute Loop → Output
                                              │
                              ┌───────────────┼───────────────┐
                              ▼               ▼               ▼
                          IMPLEMENT → VERIFY → REVIEW → FIX → RE-VERIFY → SCORE
                                                                           │
                                                              Score >= 80? ─┴─ Loop (max 5)
```

### Core Components

#### From Sant'Anna
- **Plan-First Workflow**: Enter plan mode before non-trivial tasks
- **Orchestrator Protocol**: Autonomous coordination after plan approval
- **Quality Gates**: 80 to commit, 90 for PR, 95 for excellence
- **Session Logging**: Record why, not just what

#### From AutoTheory Paper
| Component | Description | Paper Quote |
|-----------|-------------|-------------|
| **20 Expert Personas** | Diverse methodological perspectives | "20 methodological perspectives" |
| **3 Evolution Strategies** | Random (35%), Mutate (35%), Crossover (30%) | Direct from paper |
| **Mathematical Audit** | Independent derivation verification | "independent mathematical audit" |
| **Scoring Dimensions** | Fit (50%), Plausibility (25%), Parsimony (25%) | Direct from paper |
| **Simulated Referee** | Simulates top journal referee | "referee LLM produces a final score" |

#### From Project APE
| Component | Description | Source |
|-----------|-------------|--------|
| **Tournament System** | Head-to-head paper comparison | "Papers compete in head-to-head matches" |
| **TrueSkill Rating** | Microsoft Bayesian rating | "Rankings use TrueSkill" |
| **Position Swapping** | Control for LLM position bias | "run each comparison twice with papers swapped" |
| **5 Evaluation Dimensions** | Identification, Novelty, Policy, Execution, Scope | From evaluation prompt |

#### Synthesized Extensions (*)
- **Key Questions for personas**: Heuristic questions based on persona characteristics
- **model-ensemble.yaml**: Synthesized configuration based on APE's multi-model approach

### Usage

#### 1. Clone Repository
```bash
git clone https://github.com/KyrieWong7/WK-Workflow.git
cd WK-Workflow
```

#### 2. Open in Cursor
The workflow auto-loads from the `.cursor/` directory.

#### 3. Use Skill Commands

**Core Skills (Sant'Anna):**
```
/data-analysis [dataset]   # End-to-end econometric analysis
/lit-review [topic]        # Literature search and synthesis
/review-paper [file]       # Manuscript review
/proofread [file]          # Grammar and consistency check
/commit [message]          # Stage and commit
/learn [skill-name]        # Extract discovery into skill
```

**AutoTheory Skills:**
```
/expert-personas [type]    # Activate expert persona
/theory-evolution [strategy] # Theory evolution (random/mutate/crossover)
```

**Project APE Skills:**
```
/tournament [paper1] [paper2] # Head-to-head comparison
/code-review [script]      # Automated code scan
/replicate [analysis]      # Verify reproducibility
```

### Credits

- **Sant'Anna Workflow**: Pedro H.C. Sant'Anna (Emory University)
- **AutoTheory**: Alejandro Lopez-Lira, Sina Seyfi, Yuehua Tang (University of Florida / Aalto University)
- **Project APE**: Social Catalyst Lab (University of Zurich)

### License

MIT
