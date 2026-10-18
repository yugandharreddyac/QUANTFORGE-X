# PROJECT ARCHITECTURE & SYSTEM DESIGN

**QUANTFORGE X — Technical Architecture, Layered System Design & Engineering Standards**

---

## 1. Architectural Philosophy & Principles

QUANTFORGE X is engineered not as a loose collection of disparate Jupyter notebooks, but as an integrated, production-grade quantitative research laboratory. The architecture is governed by five non-negotiable principles:

1. **Mathematical Transparency & Modular Separation:** Foundational mathematical engines (SDE solvers, Itô calculus, distributions) are completely decoupled from downstream consumer modules (pricing engines, portfolio optimizers, RL agents).
2. **Strict Provenance & Anti-Leakage Separation:** Synthetic simulation environments (where true underlying parameters are known by construction) are strictly segregated from empirical market data experiments. The data layer enforces point-in-time timestamp causality at the tensor level.
3. **Reproducibility by Design (No Global State):** Every experiment execution is completely deterministic and reproducible via explicit pseudorandom number generator (PRNG) state management using NumPy's `Generator` with `PCG64` initialized from a `SeedSequence`. Parallel and batched workers spawn independent streams via `SeedSequence.spawn()`. Reliance on global state (`np.random.seed`) is strictly prohibited.
4. **Parsimony & Baseline-First Hierarchy:** No advanced deep learning or sequential decision model is trained without an automated, contemporaneous evaluation against an appropriate classical baseline (e.g., OLS/Ridge vs Transformers; UCB1 vs Risk-Sensitive Bandits; Markowitz vs DRL).
5. **Windows-Native Robustness & Dependency Parsimony:** Fully native to Windows 10/11 environments using Python 3.11/3.13. Heavy third-party frameworks (such as PyTorch, Pydantic, CVXPY) are treated as **proposals requiring explicit approval**, not assumed dependencies. Milestone 1 is implemented strictly with standard-library configuration and minimal mathematical tools (NumPy, SciPy).

---

## 2. Layered Architecture Diagram (Aligned with Phased Roadmap)

```
+----------------------------------------------------------------------------------------------------+
|                                    LAYER 8: DASHBOARD & CLI [CORE]                                 |
|             Offline Artifact Reader  *  Interactive HTML Reports  *  CLI Experiment Runner         |
+----------------------------------------------------------------------------------------------------+
                                                  |
+----------------------------------------------------------------------------------------------------+
|                        LAYER 7: MODERN AI: TRANSFORMERS & DIFFUSION [STRETCH]                      |
|       Causal Dilated TCNs  *  Decoder-Only Transformers  *  1D Temporal Diffusion DDPM             |
|       Toy Moment Verification  *  Stylized Fact Validator  *  Regime Shift Auditing                |
+----------------------------------------------------------------------------------------------------+
                                                  |
+----------------------------------------------------------------------------------------------------+
|                      LAYER 6: BANDITS & SEQUENTIAL DECISIONS [CORE & STRETCH]                      |
|       * Multi-Armed Bandits (UCB1, Thompson Sampling vs Auer 2002 Bound) [CORE]                    |
|       * Financial Trading MDP  *  CVaR-UCB Risk Bandits  *  PPO Portfolio Agent [STRETCH]          |
+----------------------------------------------------------------------------------------------------+
                                                  |
+----------------------------------------------------------------------------------------------------+
|                     LAYER 5: CONVEX & ROBUST PORTFOLIO OPTIMIZATION [CORE]                         |
|       Markowitz Solvers (OSQP/SCS Status & Residuals)  *  Transaction Costs & Turnover Penalty     |
|       Ledoit-Wolf Covariance Shrinkage  *  Robust Uncertainty Sets  *  2-Asset Boundary Formula    |
+----------------------------------------------------------------------------------------------------+
                                                  |
+----------------------------------------------------------------------------------------------------+
|                   LAYER 4: RESEARCH PROTOCOL, RISK & VALIDATION FRAMEWORK [CORE]                   |
|       Purged/Embargoed Walk-Forward Splits  *  Trial Registry  *  Multiple Testing Options         |
|       Coherent Risk (VaR & CVaR)  *  Extreme Value Theory (POT/GPD)  *  Hill Tail Estimator        |
+----------------------------------------------------------------------------------------------------+
                                                  |
+----------------------------------------------------------------------------------------------------+
|                      LAYER 3: STATISTICAL LEARNING & KERNEL METHODS [CORE]                         |
|       Regularized Linear Models (Ridge, Lasso)  *  Support Vector Machines (SVR/SVC)               |
|       Reproducing Kernel Hilbert Spaces (RKHS)  *  Gaussian Processes (Prior Synthetic Test)      |
+----------------------------------------------------------------------------------------------------+
                                                  |
+----------------------------------------------------------------------------------------------------+
|                      LAYER 2: FINANCIAL ENGINEERING & DERIVATIVES PRICING [CORE]                   |
|       Black-Scholes Analytical Pricing & Greeks  *  Monte Carlo Derivative Engines                 |
|       Variance Reduction (Antithetic & Control)  *  Securitized Waterfall Engine [STRETCH]         |
+----------------------------------------------------------------------------------------------------+
                                                  |
+----------------------------------------------------------------------------------------------------+
|                LAYER 1: REPRODUCIBLE STOCHASTIC FOUNDATIONS (MILESTONE 1) [CORE]                   |
|       Brownian Motion Path Generators  *  Quadratic Variation L^2 Convergence                      |
|       Geometric Brownian Motion & Moments  *  Euler-Maruyama & Milstein SDE Solvers                |
|       Deterministic Weak Moment Recursions  *  Itô Left-Endpoint Pathwise Invariant                |
+----------------------------------------------------------------------------------------------------+
                                                  |
+----------------------------------------------------------------------------------------------------+
|                              LAYER 0: CORE SYSTEM INFRASTRUCTURE                                   |
|       Config Manager (Minimal Stdlib / Dict)  *  Artifact Manifests  *  SeedSequence PCG64 RNG    |
|       FAST / SLOW Pytest Markers              *  Base Interfaces     *  Float64 rtol+atol Policy   |
+----------------------------------------------------------------------------------------------------+
```

---

## 3. Dependency Governance & Framework Proposals

To avoid dependency bloat and installation fragility on Windows, all non-standard-library packages are strictly governed:

| Framework / Tool | Proposed Layer | Status | Architectural Justification & Fallback Policy |
|:---|:---:|:---:|:---|
| **Python 3.11 / 3.13** | Core | Host Verified | Python 3.13.2 verified present on host OS. Decision awaiting approval whether to keep 3.13.2 or install an older interpreter (e.g. 3.11/3.12). |
| **NumPy** | Layer 0–8 | Proposal Pending Approval | Versions to be determined at install time, must support host Python, pinned exactly and recorded in each experiment record. Essential for vectorized math and `SeedSequence`/`PCG64`. |
| **SciPy** | Layer 1–5 | Proposal Pending Approval (Optional) | Versions to be determined at install time. Standard library (`math.comb`, `statistics.NormalDist`) may make SciPy unnecessary in Milestone 1; SciPy is listed as optional with a stated justification requirement. |
| **pytest** | Testing | Proposal Pending Approval | Standard test framework for CI/CD assertions with custom markers (`@pytest.mark.fast`, `@pytest.mark.slow`). |
| **Pydantic** | Layer 0 | **PROPOSAL ONLY (EXCLUDED FROM M1)** | Typed configuration validation. Milestone 1 will use standard library `json` parsing to avoid unapproved dependencies. |
| **CVXPY & OSQP** | Layer 5 | **PROPOSAL ONLY (EXCLUDED FROM M1)** | Convex quadratic programming. Deferred strictly to Phase 5. |
| **PyTorch** | Layer 6–7 | **PROPOSAL ONLY (EXCLUDED FROM M1)** | Autograd and deep learning. Deferred strictly to Phase 6/7. No CUDA or PyTorch code in Milestone 1. |
| **Cloud / Paid Services** | All | **STRICTLY EXCLUDED** | Entire laboratory executes 100% locally and offline without paid APIs, cloud services, or external database servers. |

---

## 4. Detailed Component Decomposition & Modules (Restored Architecture)

*(Restored and realigned to the 8-phase roadmap and 8-layer architecture)*

### Layer 0: Core Infrastructure (`quantforge.core`)
- **`config.py`**: Minimal typed configuration handler using Python standard library (`json` / dictionaries). Validates schema and parameter boundaries without external dependencies.
- **`rng.py`**: Centralized seed management using NumPy `Generator` with `PCG64` initialized from `SeedSequence`. Parallel and batched workers spawn via `SeedSequence.spawn()`.
- **`registry.py`**: Cryptographic artifact manager. Calculates `SHA-256` hashes of input configurations and registers experiment runs, outputs, and git commit hashes (when available).
- **`base.py`**: Abstract base classes defining system-wide contracts (`BaseExperiment`, `BaseSDESolver`, `BasePricer`, `BaseBanditPolicy`, `BasePortfolioSolver`).

### Layer 1: Reproducible Stochastic Foundations (`quantforge.math` - Phase 1 [CORE])
- **`brownian.py`**: Standard Brownian motion path simulation with Gaussian increment verification and quadratic variation scaling ($2T^2/N$).
- **`gbm.py`**: Geometric Brownian Motion exact analytical path simulation and analytical first/second moment verifications.
- **`sde.py`**: Vectorized Euler-Maruyama and Milstein 1D numerical solvers with strong convergence evaluation and deterministic weak moment tests.
- **`ito.py`**: Stochastic calculus validator verifying the exact pathwise Itô identity $I_{\text{discrete}} - 0.5(W_T^2 - T) \equiv 0.5(T - \sum (\Delta W)^2)$ and $L^2$ RMS convergence rate $\sqrt{T \Delta t / 2}$.
- **`monte_carlo.py`**: Basic sample-mean estimator on $E[S_T]$ with CLT confidence intervals and repeated-experiment binomial coverage testing.

### Layer 2: Financial Engineering & Option Pricing (`quantforge.pricing` - Phase 2 [CORE & STRETCH])
- **`black_scholes.py`**: Vectorized European call/put closed-form pricing and Put-Call parity invariance verifier.
- **`greeks.py`**: Analytical first and second-order Greeks evaluated against finite-difference numerical approximations.
- **`monte_carlo_options.py`**: European and path-dependent derivative pricer with batch convergence tracking.
- **`variance_reduction.py`**: Antithetic variates and control variates with variance ratio uncertainty estimation.
- **`securitization.py`** `[STRETCH]`: Subordinated cash-flow waterfall modeling (Senior, Mezzanine, Equity tranches) with CPR prepayment and Poisson default hazard rates.

### Layer 3: Statistical Learning & Kernel Methods (`quantforge.statlearn` - Phase 3 [CORE])
- **`regression.py`**: OLS, Ridge, Lasso, and ElasticNet estimators with coordinate descent paths and regularized shrinkage checks.
- **`svm.py`**: Support Vector Regression/Classification with primal/dual formulation and KKT complementary slackness checks.
- **`kernels.py`**: Reproducing Kernel Hilbert Space (RKHS) library (Linear, RBF, Matérn) with Mercer Gram matrix eigenvalue checks ($\lambda_{\min} \ge -10^{-10}$).
- **`gaussian_process.py`**: Exact Gaussian Process regression with analytical posterior mean/covariance, verified against synthetic samples from the prior.
- **`calibration.py`**: Continuous regression calibration tools (PIT histograms, coverage-vs-nominal curves, CRPS).

### Layer 4: Research Protocol, Risk & Validation Framework (`quantforge.research` - Phase 4 [CORE])
- **`walk_forward.py`**: Chronological expanding and rolling window validation runner with configurable purge/embargo windows.
- **`leakage_guard.py`**: Runtime assertion layer verifying $\max(\text{train\_indices}) < \min(\text{test\_indices})$ and checking target-masking.
- **`risk_measures.py`**: Coherent risk metrics (VaR, Expected Shortfall / CVaR) with subadditivity checks and loss sign convention ($L_t = -r_t$).
- **`evt.py`**: Extreme Value Theory Peaks-Over-Threshold (POT) Generalized Pareto Distribution fitting with Kupiec POF and Christoffersen backtests.
- **`heavy_tails.py`**: Tail index estimation via the Hill estimator with derived $z/\sqrt{k}$ error bounds.
- **`multiple_testing.py`**: Multiple-testing candidate adjustments (Bonferroni, Benjamini-Hochberg FDR, White's Reality Check).
- **`failure_registry.py`**: Persistent structured registry (`failed_hypotheses_registry.json`) cataloging rejected hypotheses, ablations, and negative results.

### Layer 5: Convex & Robust Portfolio Optimization (`quantforge.portfolio` - Phase 5 [CORE])
- **`convex_opt.py`**: Markowitz mean-variance optimization using convex programming (OSQP/SCS) with primal/dual residual audits and status checks.
- **`analytical_2asset.py`**: Analytical 2-asset minimum-variance formula with covariance $\sigma_{12}$ and boundary handling ($w_1^* < 0$ or $w_1^* > 1$).
- **`constraints.py`**: Box bounds, sector caps, and turnover constraints.
- **`transaction_costs.py`**: Linear spread and quadratic market impact friction modeling with turnover penalty monotonicity checks.
- **`robust_opt.py`**: Robust optimization under ellipsoidal return uncertainty sets and Ledoit-Wolf covariance shrinkage.

### Layer 6: Bandits & Sequential Decisions (`quantforge.rl_bandits` - Phase 6 [CORE & STRETCH])
- **`mab.py`** `[CORE]`: Multi-Armed Bandit policies ($\epsilon$-Greedy, UCB1, Thompson Sampling) with pseudo-regret bounded by Auer et al. (2002) finite-time bounds.
- **`risk_bandits.py`** `[STRETCH]`: Risk-sensitive bandits implementing CVaR-UCB under heavy-tailed Pareto rewards.
- **`financial_mdp.py`** `[STRETCH]`: Gymnasium-compliant discrete/continuous financial trading environment with transaction cost deduction.
- **`drl_portfolio.py`** `[STRETCH]`: Deep Reinforcement Learning continuous asset allocator (PPO) projecting onto the probability simplex.

### Layer 7: Modern AI: Transformers & Diffusion (`quantforge.deeplearn` - Phase 7 [STRETCH])
- **`networks.py`**: Baseline MLPs and Temporal Convolutional Networks (TCN) with causal dilations and zero-leakage gradient checks.
- **`transformers.py`**: Causal self-attention layers and decoder-only Transformer blocks with triangular masking.
- **`diffusion.py`**: 1D temporal Denoising Diffusion Probabilistic Model (DDPM) for synthetic scenario generation, verified on 1D Gaussian targets.
- **`stylized_facts.py`**: Statistical validator evaluating heavy tails, volatility clustering, and absence of raw return autocorrelation.

### Layer 8: Dashboard & Reporting (`quantforge.dashboard` - Phase 8 [CORE])
- **`artifact_reader.py`**: Safe deserializer reading genuine experiment JSON and Parquet artifacts without mock data.
- **`report_gen.py`**: Generates standalone, self-contained Markdown/HTML research tear-sheets and metric comparison tables.

---

## 5. Formal Interface Contracts

To guarantee that components interoperate seamlessly without brittle coupling, all core modules adhere to abstract base classes defined in `quantforge.core.base`:

```python
# Conceptual Interface Contracts (No External Framework Dependencies)

from abc import ABC, abstractmethod
from pathlib import Path
from typing import Any, Dict
import numpy as np

class BaseRNGService(ABC):
    """Abstract manager for deterministic pseudo-random number streams."""
    @abstractmethod
    def get_generator(self) -> np.random.Generator: ...
    
    @abstractmethod
    def spawn_generators(self, n: int) -> list[np.random.Generator]: ...

class BaseSDESolver(ABC):
    """Abstract interface for 1D numerical SDE solvers."""
    @abstractmethod
    def step(self, x: np.ndarray, t: float, dt: float, dW: np.ndarray) -> np.ndarray: ...
    
    @abstractmethod
    def solve(self, x0: float, T: float, N: int, dW: np.ndarray) -> np.ndarray: ...

class BaseExperiment(ABC):
    """Abstract lifecycle for all research experiments."""
    @abstractmethod
    def setup(self, config: Dict[str, Any]) -> None: ...
    @abstractmethod
    def run(self) -> Dict[str, Any]: ...
    @abstractmethod
    def validate(self) -> Dict[str, Any]: ...
    @abstractmethod
    def export_artifacts(self, artifact_dir: Path) -> None: ...

class BasePricer(ABC):
    """Abstract interface for derivatives pricing engines."""
    @abstractmethod
    def price(self, spot: float, strike: float, rate: float, vol: float, maturity: float) -> float: ...
    @abstractmethod
    def greeks(self, spot: float, strike: float, rate: float, vol: float, maturity: float) -> Dict[str, float]: ...

class BaseBanditPolicy(ABC):
    """Abstract interface for multi-armed and risk-sensitive bandits."""
    @abstractmethod
    def select_arm(self) -> int: ...
    @abstractmethod
    def update(self, arm: int, reward: float) -> None: ...
    @abstractmethod
    def get_regret(self) -> float: ...

class BasePortfolioSolver(ABC):
    """Abstract interface for portfolio optimization."""
    @abstractmethod
    def solve(self, expected_returns: np.ndarray, cov_matrix: np.ndarray,
              constraints: Dict[str, Any]) -> np.ndarray: ...
```

---

## 6. Data Flow Architecture & Segregation (Restored)

The system establishes an absolute barrier between **Synthetic Path Generation** and **Empirical Market Ingestion**:

```
+------------------------------------+          +------------------------------------+
|       SYNTHETIC GENERATOR          |          |      EMPIRICAL MARKET INGESTION    |
| (GBM, Heston, OU, Jump-Diffusion)  |          |  (Yahoo Finance / FRED Daily Data) |
+------------------------------------+          +------------------------------------+
                 |                                                 |
       [Tag: SYNTHETIC_DATA]                             [Tag: HISTORICAL_DATA]
                 |                                                 |
                 v                                                 v
+------------------------------------+          +------------------------------------+
|  MATHEMATICAL & SDE VERIFICATION   |          |    POINT-IN-TIME FEATURE STORE     |
| * Analytical BS convergence tests  |          | * Strictly lagged feature matrix   |
| * SDE strong/weak error checks     |          | * Purged & embargoed time windows  |
| * Variance reduction benchmarks    |          | * Leakage assertion runtime audit  |
+------------------------------------+          +------------------------------------+
                 |                                                 |
                 +-----------------------+-------------------------+
                                         |
                                         v
                        +---------------------------------+
                        |    COMMON EXPERIMENT HARNESS    |
                        | * Config Hashing & Seed Locking |
                        | * Out-of-Sample Walk-Forward    |
                        | * Baseline vs Advanced Models   |
                        +---------------------------------+
                                         |
                                         v
                        +---------------------------------+
                        |   UNIFIED ARTIFACT GENERATOR    |
                        | * JSON Metrics & Parameters     |
                        | * High-Resolution PNG Plots     |
                        | * Markdown Research Summaries   |
                        +---------------------------------+
```

---

## 7. Windows OS Compatibility & Compute Standards (Restored)

- **Filesystem Paths:** All path resolutions use Python's standard `pathlib.Path` ensuring proper handling of Windows backslashes (`\`) and drive letters (`C:\...`).
- **Process Isolation:** Script execution is compatible with Windows PowerShell 7 and standard Windows command line environments without reliance on Bash or Unix-only utilities.
- **Compute Acceleration Strategy:**
  - Standard compute relies on multi-threaded NumPy / SciPy leveraging Intel MKL or OpenBLAS automatically provided with Windows wheels.
  - Deep learning components (Layer 7 [STRETCH]) execute on CPU by default for rapid test validation, with optional detection of NVIDIA CUDA via `torch.cuda.is_available()`.
  - Heavy optimization tasks (Layer 5) leverage CVXPY's native OSQP/SCS C-solvers precompiled for Windows.

---

## 8. Random Number Generation & Numerical Equality Policy

1. **SeedSequence Policy:** Central entrypoint accepts an integer seed or entropy string, constructing a master `np.random.SeedSequence`. All generators are spawned from this master sequence.
2. **Numerical Assertions:** Float64 array equality must use combined relative and absolute tolerances:
   ```python
   np.allclose(actual, expected, rtol=1e-9, atol=1e-12)
   ```
3. **Cross-Platform Reproducibility:** Bitwise identity (`np.array_equal`) is an expectation **only** on the same machine, same OS, and identical NumPy binary wheel. Cross-platform reproducibility across Intel, AMD, or ARM architectures is evaluated within numerical tolerance `rtol=1e-9`.
