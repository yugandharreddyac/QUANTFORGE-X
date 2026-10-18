# REPOSITORY MODULARIZATION & ARCHITECTURE REORGANIZATION PLAN

**QUANTFORGE X — Enterprise Modular Structure, Component Boundaries & Staged Migration Plan**

---

## 1. Executive Context & Phase Separation Policy

### 1.1 Context & Current Project State
QUANTFORGE X is an independent educational quantitative-finance research laboratory. As of October 18, 2026, the repository is in **Phase 0 (Planning, Mathematical Validation, and Evidence-Integrity Audit)**.
- **Implementation Status:** Zero application code, zero test files, zero datasets, and zero notebooks currently exist on disk.
- **Active Documentation Audit:** Phase 0.4 (Evidence Integrity, Statistical Budget, and Milestone 1 Specification Repair) is currently in progress / awaiting review.
- **Git Status:** Git is not currently installed on the host system PATH, and no `.git` repository has been initialized.
- **Workspace Location:** `c:\Users\ADMIN\OneDrive\Desktop\QUANTFORGE X` (active Microsoft OneDrive synchronization).

### 1.2 Phase Separation & Safety Guarantee
Per Section 1 of the Repository Modularization directive:
> *"Do not interrupt or silently expand an active Phase 0.4 documentation-only audit. If Phase 0.4 remains in progress, first complete the repository inspection and produce a proposed modularization plan without moving application files. Then stop and report that the physical migration should occur after the audit has been reviewed and approved."*

To protect the integrity of the active Phase 0.4 audit:
1. **Existing Documentation Kept In Place:** All 13 Markdown files in the root and `docs/` remain in their canonical paths (`docs/ACCEPTANCE_CRITERIA.md`, `docs/MATHEMATICAL_VALIDATION_PLAN.md`, etc.). Moving or partitioning `docs/` into subdirectories at this stage would break over 72 active cross-document links and invalidate ongoing audit references.
2. **No Speculative Placeholder Directories:** Empty folders (e.g. `frontend/src/`, `backend/api/`, `ml/transformers/`) will **not** be created prior to implementation. Creating empty placeholder directories creates misleading architectural signals and clutter in an untracked repository.
3. **Staged Implementation Mapping:** The target structure defined herein serves as the **official architectural blueprint** to be adopted progressively as implementation milestones commence (starting with Milestone 1).

---

## 2. Target Architecture & Logical Directory Topology

The target directory structure organizes QUANTFORGE X into decoupled, testable, and logically bounded packages:

```
QUANTFORGE-X/
├── README.md                           # Master repository overview, setup, and navigation
├── LICENSE                             # Educational research license
├── .gitignore                          # Standard Python, IDE, OS, and large artifact exclusions
├── .editorconfig                       # UTF-8, LF line endings, 4-space indentation standards
│
├── quantforge/                         # Core Quantitative Engine (Reusable, zero-framework math)
│   ├── README.md                       # Quantitative engine documentation & mathematical invariants
│   ├── __init__.py                     # Package entry point (exposes version and core primitives)
│   ├── core/                           # Infrastructure: config parser, RNGManager (PCG64), logging
│   ├── probability/                    # Distribution fitting, analytical density functions, MLE
│   ├── stochastic/                     # Brownian motion, Itô calculus, quadratic variation
│   ├── simulation/                     # Monte Carlo sample-mean estimators, confidence intervals
│   ├── numerical/                      # SDE solvers (Euler-Maruyama, Milstein 1D), root finders
│   ├── finance/                        # Returns, Black-Scholes pricing & Greeks, option MC
│   └── validation/                     # Deterministic invariants & analytical moment fixtures
│
├── ml/                                 # Statistical & Machine Learning Research Models
│   ├── README.md                       # ML research protocols, baseline models, causality rules
│   ├── __init__.py                     # ML package entry point
│   ├── statistical_learning/           # OLS, Ridge, Lasso, ElasticNet with coordinate descent
│   ├── kernel_methods/                 # SVR/SVC, RKHS kernels (RBF, Matérn), Gram matrices
│   ├── gp/                             # Exact Gaussian Process regression, Cholesky solver, PIT
│   ├── deep_learning/                  # Temporal Convolutional Networks (TCN), causal dilations
│   ├── transformers/                   # Decoder-only causal masked attention blocks
│   ├── diffusion/                      # 1D temporal DDPM for synthetic financial scenario generation
│   ├── bandits/                        # Multi-armed bandits (eps-Greedy, UCB1, Thompson Sampling)
│   └── reinforcement_learning/         # Financial trading MDP, risk-aware bandits, PPO allocator
│
├── portfolio/                          # Portfolio Construction, Optimization & Risk
│   ├── README.md                       # Portfolio theory, solver interfaces, transaction cost models
│   ├── __init__.py                     # Portfolio package entry point
│   ├── optimization/                   # Markowitz mean-variance, CVXPY interfaces (OSQP/SCS)
│   ├── constraints/                    # Box constraints, sector exposure caps, turnover penalties
│   └── risk/                           # VaR, CVaR (Expected Shortfall), Ledoit-Wolf shrinkage
│
├── frontend/                           # Offline Presentation & Research Dashboard (Phase 8)
│   ├── README.md                       # Dashboard UI architecture, offline manifest reader
│   ├── src/                            # Modern UI source (React/TypeScript or Vanilla Web)
│   ├── components/                     # Tear-sheet widgets, parameter controllers, metrics tables
│   ├── charts/                         # Plotly/Canvas convergence curves, efficient frontier plots
│   └── styles/                         # Theme definitions (dark mode, glassmorphic research UI)
│
├── backend/                            # Local Research API / Application Service (Phase 8)
│   ├── README.md                       # Local API documentation, IPC conventions
│   ├── src/                            # API endpoints, JSON/Parquet deserialization services
│   └── tests/                          # API endpoint integration tests
│
├── experiments/                        # Empirical Research Protocol & Controlled Studies
│   ├── README.md                       # Experiment registry, negative results policy
│   ├── benchmarks/                     # Standard baseline models (1/N, Ridge AR, naive MC)
│   ├── research/                       # Controlled experiment runners (EXP-01 through EXP-07)
│   └── results/                        # Serialized run manifests, metrics JSON, PNG tear-sheets
│
├── data/                               # Data Governance, Schemas & Provenance
│   ├── README.md                       # Licensing terms, anti-leakage rules, synthetic data first
│   ├── manifests/                      # Dataset provenance manifests and checksum logs
│   ├── schemas/                        # Immutable feature and price table schemas
│   └── local/                          # Local cache directory (gitignored; no raw data in repo)
│
├── tests/                              # Global Verification & Test Hierarchy
│   ├── README.md                       # Test taxonomy, FAST vs SLOW suite execution rules
│   ├── unit/                           # FAST: deterministic logic, config parsing, algebra
│   ├── numerical/                      # FAST/SLOW: discretization tolerances, exact moment checks
│   ├── statistical/                    # SLOW: binomial coverage bands, FWER, hypothesis tests
│   ├── integration/                    # Pipeline tests: walk-forward splits, anti-leakage guards
│   └── regression/                     # Fixed-seed bitwise and golden output reproducibility
│
├── config/                             # Experiment Configuration & Parameter Sets
│   ├── README.md                       # Config schema documentation, immutability rules
│   └── examples/                       # Reference JSON/YAML configs for M1, EXP-01..07
│
├── scripts/                            # Operational & Verification Scripts
│   ├── README.md                       # Script usage guide
│   ├── verification/                   # Standalone mathematical derivation & tolerance checkers
│   └── dev/                            # Environment inspection, migration, and backup helpers
│
└── docs/                               # Formal Research Specifications & Audit Records
    ├── ACCEPTANCE_CRITERIA.md          # Quantitative acceptance criteria & test matrix
    ├── MATHEMATICAL_VALIDATION_PLAN.md # Analytical derivations, proof steps, and tolerances
    ├── PROJECT_ARCHITECTURE.md         # System topology, interfaces, and compute standards
    ├── IMPLEMENTATION_ROADMAP.md       # Phased milestone dependency graph & exit gates
    ├── DOMAIN_COVERAGE_MATRIX.md       # 8-domain master coverage mapping (A through H)
    ├── EXPERIMENTAL_DESIGN.md          # Controlled research protocol & hypothesis inventory
    ├── DATA_AND_LICENSE_STRATEGY.md    # Data provenance, licensing & anti-leakage guards
    ├── RISK_REGISTER.md                # Quantitative, engineering & operational risk register
    ├── SOURCE_TRACEABILITY.md          # Institutional attribution & academic references
    ├── AUDIT_FINDINGS.md               # Master defect and audit findings log (FND-01..38)
    ├── CHANGELOG.md                    # Project-wide append-only modification history
    ├── REPOSITORY_MODULARIZATION_PLAN.md # This architectural reorganization plan
    └── _audit_backup/                  # Timestamped pre-edit audit backups (PERMANENT ARCHIVE)
```

---

## 3. Component Boundaries & Responsibility Matrix

| Subsystem / Layer | Allowed Inbound Dependencies | Allowed Outbound Dependencies | Forbidden Dependencies | Primary Responsibility |
|:---|:---|:---|:---|:---|
| **Quantitative Engine (`quantforge/`)** | `ml/`, `portfolio/`, `experiments/`, `backend/`, `tests/` | Standard library, `numpy`, optional `scipy` | `frontend/`, `backend/`, `ml/`, `portfolio/` | Foundational stochastic calculus, PRNG state, Brownian paths, numerical SDE solvers, exact moment invariants. **Must be completely standalone and zero-framework.** |
| **Machine Learning (`ml/`)** | `experiments/`, `backend/`, `tests/` | `quantforge/`, `numpy`, `scipy` | `frontend/`, `backend/`, `portfolio/` (no cyclic link) | Regularized regression, kernel methods, Gaussian Processes, neural sequence models, diffusion scenario generators, multi-armed bandits. |
| **Portfolio & Risk (`portfolio/`)** | `experiments/`, `backend/`, `tests/` | `quantforge/`, `numpy`, optional `cvxpy` | `frontend/`, `backend/`, `ml/` (use shared interfaces) | Mean-variance optimization, box/turnover constraints, robust shrinkage, VaR/CVaR coherent risk measures. |
| **Data Governance (`data/`)** | `quantforge/`, `ml/`, `portfolio/`, `experiments/` | Standard library, `numpy` | Network APIs, live broker connectors | Pure schema definitions, point-in-time temporal alignment, chronological train/test splits, anti-leakage guards. Zero raw proprietary data. |
| **Experiments (`experiments/`)** | `backend/`, `tests/` | `quantforge/`, `ml/`, `portfolio/`, `data/` | `frontend/` | Controlled research studies (EXP-01 to EXP-07), ablation runs, parameter sweep records, negative result tracking in `failed_hypotheses_registry.json`. |
| **Backend Service (`backend/`)** | `frontend/` (via HTTP/IPC) | `quantforge/`, `ml/`, `portfolio/`, `experiments/` | UI rendering code, browser DOM APIs | Local read-only deserialization service reading serialized experiment artifacts (JSON/Parquet) for dashboard display. |
| **Frontend UI (`frontend/`)** | User / Web Browser | Backend API or local JSON manifests | `quantforge/`, `ml/`, `portfolio/` direct execution | Interactive presentation, parameter exploration, tear-sheet visualization, chart rendering. **Zero live compute in presentation layer.** |
| **Test Hierarchy (`tests/`)** | CI / Test Runner | All internal modules | None | Rigorous test execution partitioned into FAST unit tests (< 5s) and SLOW validation suites governed by the 20-decision statistical budget. |

---

## 4. Circular Dependency Mitigation Strategy

To prevent coupling and circular import cycles between scientific packages:
1. **Unidirectional Dependency Flow:**
   $$\text{quantforge} \longrightarrow \text{ml} \longrightarrow \text{experiments}$$
   $$\text{quantforge} \longrightarrow \text{portfolio} \longrightarrow \text{experiments}$$
2. **Bandits & Sequential Allocation Boundary:**
   - Multi-armed bandits (`ml/bandits/`) evaluate reward distributions and regret bounds independently of financial portfolios.
   - When bandits or RL allocate across portfolio weights (`ml/reinforcement_learning/drl_allocator.py`), they consume portfolio constraint abstractions via duck typing or functional callbacks, never by importing internal solver routines from `portfolio/optimization/`.
3. **Risk Measures Boundary:**
   - Coherent risk calculations (VaR, CVaR, EVT Peaks-Over-Threshold) reside in `portfolio/risk/`.
   - When statistical learning models evaluate predictive uncertainty (e.g. GP continuous ranked probability score), they evaluate PIT calibration internally in `ml/gp/` without calling portfolio risk engines.

---

## 5. Migration Manifest & Staged Implementation Roadmap

Because the repository currently contains only documentation, the migration is executed in **logical milestone stages** as implementation code is authored:

| Stage | Trigger / Phase Gate | Target Directory Action | Files Created / Moved | Reference Updates |
|:---|:---|:---|:---|:---|
| **Stage 0 (Current)** | Phase 0.4 Audit Approval | Establish `.gitignore`, `.editorconfig`, `docs/REPOSITORY_MODULARIZATION_PLAN.md` | New configuration files in root; all documentation preserved in `docs/` | Update `PROJECT_ARCHITECTURE.md` Section 4 to reflect modular topology. |
| **Stage 1 (Milestone 1)** | Phase 1 Kickoff | Initialize `quantforge/` package tree | `quantforge/__init__.py`, `quantforge/core/`, `quantforge/stochastic/`, `quantforge/simulation/`, `quantforge/numerical/` | `tests/unit/`, `tests/numerical/`, `tests/statistical/` initialized for M1 requirements. |
| **Stage 2 (Milestone 1.1)** | M1 Gate Passed | Expand `quantforge/` for Probability Toolkit | `quantforge/probability/` (distributions, MLE, Markov chains) | Test suites expanded in `tests/numerical/test_distributions.py`. |
| **Stage 3 (Phase 2)** | M1.1 Gate Passed | Initialize `quantforge/finance/` | Black-Scholes analytical, options MC, variance reduction, securitization waterfall | `experiments/research/exp01_variance_reduction/` initialized. |
| **Stage 4 (Phase 3 & 4)** | Phase 2 Gate Passed | Initialize `ml/statistical_learning/`, `ml/gp/`, `data/` | Linear models, SVR, GP regression, anti-leakage data store, EVT tail estimators | `experiments/research/exp02_tail_risk/`, `exp03_volatility/`. |
| **Stage 5 (Phase 5 & 6)** | Phase 4 Gate Passed | Initialize `portfolio/`, `ml/bandits/` | Mean-variance QP, transaction costs, MAB regret, risk-sensitive bandits | `experiments/research/exp05_risk_bandits/`, `exp06_robust_portfolio/`. |
| **Stage 6 (Phase 7 & 8)** | Phase 6 Gate Passed | Initialize `ml/deep_learning/`, `frontend/`, `backend/` | TCN, Transformers, 1D DDPM, Offline Dashboard presentation layer | End-to-end master replication script in `scripts/verify_all.py`. |

---

## 6. Verification & Test Discovery Conventions

The modular repository enforces strict test discovery conventions using standard `pytest`:
1. **Marker Partitioning:**
   - `@pytest.mark.fast`: Fast unit tests, pathwise algebraic invariants (`[MATH_INVARIANT]`), and config tests. Must complete in `< 5` seconds total.
   - `@pytest.mark.slow`: Monte Carlo replications, strong convergence regressions, and statistical hypothesis tests (`[STAT_VALIDATION]`). Governed by the aggregate false-failure budget ($\alpha_{\text{suite}} \le 10^{-2}$).
2. **Test File Naming:**
   - `tests/unit/test_config.py`
   - `tests/numerical/test_sde_invariants.py`
   - `tests/statistical/test_mc_coverage.py`
3. **Execution Commands:**
   - Fast suite: `pytest -m fast tests/`
   - Slow suite: `pytest -m slow tests/`
   - Full suite: `pytest tests/`

---

## 7. Rollback & Disaster Recovery Procedures

1. **Pre-Migration Backups:** Before any physical file relocation occurs, an automated snapshot is created in `docs/_audit_backup/*.bak`.
2. **Reversible Directory Creation:** If directory restructuring fails or introduces cyclic dependencies, the repository can be cleanly restored to its baseline documentation-only state by removing newly created empty subdirectories without touching `docs/` or root files.
3. **Audit Trail Immutability:** `docs/_audit_backup/` is permanent and must never be cleaned, pruned, or moved.
