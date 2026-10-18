# MATHEMATICAL & SCIENTIFIC VALIDATION PLAN

**QUANTFORGE X — Analytical Benchmarks, Numerical Convergence Limits & Mathematical Proofs**

---

## 1. Principles of Mathematical Validation & Criteria Taxonomy

In QUANTFORGE X, no quantitative routine is considered acceptable merely because code executes without raising an exception. However, scientific rigor also demands that we do not conflate **software correctness** with **empirical research hypotheses**.

Every validation test and acceptance criterion in this laboratory is strictly categorized into one of six classes:

1. **`[MATH_INVARIANT]` (Analytical Invariant):** Closed-form identity or exact conservation law that must hold to floating-point relative tolerance on every execution. **Status: PASS/FAIL.**
2. **`[NUM_TOLERANCE]` (Numerical Approximation Tolerance):** Discretization error or optimization convergence governed by a proven asymptotic rate (e.g., SDE strong error slope, KKT residuals). **Status: PASS/FAIL.**
3. **`[STAT_VALIDATION]` (Statistical Algorithm Verification):** Statistical property of an algorithm verified on *synthetic data with known ground truth* (e.g., Monte Carlo coverage, SDE weak moments, MLE parameter consistency). **Status: PASS/FAIL (with explicitly derived false-failure budget).**
4. **`[EMP_HYPOTHESIS]` (Empirical Research Hypothesis):** A testable scientific hypothesis evaluated on real market data (e.g., whether deep learning beats linear baselines, whether EVT-VaR passes Kupiec tests during crises, whether risk-sensitive bandits reduce drawdown). **Status: SCIENTIFIC OUTCOME (Both positive and negative results are valid deliverables; NEVER a software pass/fail gate).**
5. **`[PERF_BENCHMARK]` (Performance & Resource Constraint):** Execution time, memory footprint, or hardware compatibility limits. **Status: PROPOSAL PENDING MEASUREMENT.**
6. **`[DOC_REPRO]` (Documentation & Reproducibility):** SHA-256 configuration hashes, seed logging, library version tracking, and failure registry entries. **Status: PASS/FAIL.**

### Aggregate False-Failure Budget & Statistical Threshold Formulation:
- Every statistical validation check in the test suite is allocated an explicit false-failure budget $\alpha_{\text{check}}$.
- The sum of false-failure probabilities across all statistical checks in the SLOW test suite must not exceed the overall suite-level false-failure budget:
  $$\sum_{j=1}^{M_{\text{checks}}} \alpha_{\text{check}, j} \le \alpha_{\text{overall}}$$
- **Suite-Level Budget & Decision Inventory:**
  - Overall suite budget: $\alpha_{\text{overall}} \le 10^{-2}$ ($1.0\%$) per full execution of the SLOW test suite.
  - Decision Inventory: Milestone 1 contains 13 distinct statistical decisions (MC coverage; MC slope; increments mean, variance, autocorrelation; quadratic variation mean, variance; GBM first, second moment; Milstein strong slope; Euler strong slope; solver weak moment; Itô RMS slope).
  - The suite statistical decision capacity is formally capped at $M_{\max} = 20$ statistical decisions.
  - **Per-Decision False-Failure Budget:**
    $$\alpha_{\text{check}} = \frac{\alpha_{\text{overall}}}{M_{\max}} = \frac{10^{-2}}{20} = 5.0 \times 10^{-4} \quad (0.05\%)$$
- **Union Bound Guarantee & Assumptions:**
  By Boole's inequality (the Union Bound):
  $$P(\text{at least one false failure}) = P\left(\bigcup_{j=1}^{M_{\max}} E_j\right) \le \sum_{j=1}^{M_{\max}} P(E_j) \le 20 \times (5.0 \times 10^{-4}) = 0.01 = 1.0\%$$
  - *Applicability & Robustness:* The union bound holds for **arbitrary joint dependence structures** (whether test statistics are independent, positively correlated, or negatively correlated).
  - *Assumptions:* (1) Tests are pre-specified and non-adaptive (no post-hoc hypothesis snooping); (2) Each individual test has its marginal false-rejection rate strictly upper-bounded by $\alpha_{\text{check}}$.
- **Budget-Derived Critical Acceptance Multipliers:**
  1. **Asymptotic / Known-Variance Normal Multiplier ($z$-threshold):**
     For two-sided checks with large sample sizes ($P \ge 10,000$) where variance is known or estimated with negligible relative uncertainty:
     $$z = \Phi^{-1}\left(1 - \frac{\alpha_{\text{check}}}{2}\right) = \Phi^{-1}(0.99975) \approx 3.480756 \approx 3.48$$
     *(Computed via Python standard library `statistics.NormalDist().inv_cdf(0.99975)`).*
  2. **Small-Sample Batch Estimation Student-$t$ Multiplier ($t$-threshold):**
     When estimating convergence slopes or variance ratios across $B = 10$ independent batches, the sample variance of the batch estimates has $df = B - 1 = 9$ degrees of freedom. Under normality of batch estimators, the test statistic follows Student-$t$ with 9 degrees of freedom.
     For two-sided $\alpha_{\text{check}} = 5.0 \times 10^{-4}$:
     $$t_{\text{crit}} = t_{0.99975, \, \text{df}=9} \approx 5.290654 \approx 5.29$$
     *(Critical Methodological Note: If the standard normal multiplier $z \approx 3.48$ were mistakenly applied to $t(9)$, the actual two-sided false rejection probability would be $6.93 \times 10^{-3}$, violating the $5 \times 10^{-4}$ per-check budget by nearly $14\times$. Therefore, small-sample batch tests must use $t_{\text{crit}} \approx 5.29$, or expand batch count to $B \ge 30$).*
  3. **Comparison with Fixed "3 SE" Rule:** A fixed 3-standard-error band ($z = 3.0$) corresponds to a two-sided false-failure probability of $2(1 - \Phi(3.0)) \approx 2.7 \times 10^{-3}$, which exceeds the $5 \times 10^{-4}$ budget by over $5\times$. Bare "3 SE" rules are eliminated throughout the project.

---

## 2. Mathematical Benchmark Inventory & Taxonomy

```
+--------------------------------------------------------------------------------------------------------------------+
|                                         MATHEMATICAL VALIDATION MAP                                                |
+--------------------------------------------------------------------------------------------------------------------+
| COMPONENT                   | NUMERICAL METHOD              | THEORETICAL BENCHMARK           | CLASSIFICATION     |
|-----------------------------+-------------------------------+---------------------------------+--------------------|
| Black-Scholes European Call | Closed-Form & Monte Carlo     | Analytical Put-Call Parity      | MATH_INVARIANT     |
| Brownian Quadratic Variation| Pathwise Discretization       | Sum (dW)^2 -> T in L2 & prob.   | NUM_TOLERANCE      |
| Itô Stochastic Calculus     | Left-Endpoint Discrete Sums   | I_discrete - 0.5(W_T^2 - T)     | MATH_INVARIANT/NUM |
| SDE Strong Convergence      | Euler-Maruyama & Milstein     | Exact Path Coupling on GBM      | NUM_TOLERANCE      |
| SDE Scheme Moment Check     | Solver Output vs Moment Factor| Exact Recursion x0^2 * f(h)^N   | STAT_VALIDATION    |
| SDE Weak Formula Check      | Analytical Moment Recursions  | Exact E[X_N^2] vs Analytical    | MATH_INVARIANT/NUM |
| Monte Carlo Coverage        | Repeated Sample Intervals     | Exact Binomial Coverage Band    | STAT_VALIDATION    |
| Variance Reduction          | Antithetic & Control Variates | Measured VRF with Uncertainty   | STAT_VALIDATION    |
| Tail Distribution Fitting   | Hill Estimator / GPD MLE      | Asymptotic SD 1/sqrt(k)         | STAT_VALIDATION    |
| Gaussian Process Regression | Cholesky Posterior Solver     | Exact GP Prior Synthetic Test   | STAT_VALIDATION    |
| Portfolio Optimization      | Convex QP Solvers (OSQP/SCS)  | KKT Residuals & Analytical 2-A  | NUM_TOLERANCE      |
| Tail Risk Backtesting       | EVT Peaks-Over-Threshold      | Kupiec POF & Christoffersen     | EMP_HYPOTHESIS     |
| Deep vs Linear Forecasting  | TCN / Transformer vs Ridge    | Out-of-sample RMSE & MASE       | EMP_HYPOTHESIS     |
| Risk-Sensitive Bandits      | CVaR-UCB vs Standard UCB1     | Finite-Time Regret Bound        | STAT_VALIDATION/EMP|
+--------------------------------------------------------------------------------------------------------------------+
```

---

## 3. Detailed Component Validation Protocols

### 3.1 Option Pricing & Monte Carlo Confidence Bounds

#### Analytical Ground Truth & Put-Call Parity (`[MATH_INVARIANT]`):
For European options on a non-dividend paying stock with geometric Brownian motion dynamics, the Put-Call parity identity must hold to floating-point precision on every strike and maturity grid point:
$$|C_{\text{BS}} - P_{\text{BS}} - (S_0 - K e^{-rT})| < 10^{-12}$$

#### Monte Carlo Estimator & Repeated Coverage Validation (`[STAT_VALIDATION]`):
For terminal expectation $\hat{\mu} = \frac{1}{N} \sum_{i=1}^N Y^{(i)}$, let the sample variance be $S_N^2$. The standard CLT asymptotic $100(1-\alpha)\%$ confidence interval is:
$$\left[ \hat{\mu} - z_{1-\alpha/2} \frac{S_N}{\sqrt{N}}, \, \hat{\mu} + z_{1-\alpha/2} \frac{S_N}{\sqrt{N}} \right]$$
1. **Nominal Coverage Level:** Nominal coverage is $p = 0.99$ with $z_{0.995} \approx 2.576$.
2. **Exact Binomial Coverage Band (Derived via math.comb):**
   Rather than guessing arbitrary bands, the empirical coverage $K$ across $R$ independent replications ($K \sim \text{Binomial}(R, p)$ with nominal $p = 0.99$) is evaluated against an exact Binomial distribution with an explicit false-failure budget $\alpha_{\text{check}} = 5.0 \times 10^{-4}$ ($0.05\%$) under the 20-decision suite budget (or $\alpha_{\text{check}} = 10^{-3}$ legacy budget).
   The acceptance rule is $K \ge k_0$, where $k_0$ is the smallest integer such that $P(K < k_0) = P(K \le k_0 - 1) \le \alpha_{\text{check}}$.
   - **For $R = 200, p = 0.99$:**
     - Exact probabilities computed via Python `math.comb`:
       - $P(K = 190) = 3.3260 \times 10^{-5}, \quad P(K \le 190) = 4.0142 \times 10^{-5}$
       - $P(K = 191) = 1.7239 \times 10^{-4}, \quad P(K \le 191) = 2.1254 \times 10^{-4}$
       - $P(K = 192) = 8.0002 \times 10^{-4}, \quad P(K \le 192) = 1.0126 \times 10^{-3}$
     - Critical Threshold Decision:
       - For both $\alpha_{\text{check}} = 5 \times 10^{-4}$ and $\alpha_{\text{check}} = 10^{-3}$:
         $$P(K < 192) = P(K \le 191) = 2.1254 \times 10^{-4} \le 5.0 \times 10^{-4}$$
         $$P(K < 193) = P(K \le 192) = 1.0126 \times 10^{-3} > 5.0 \times 10^{-4}$$
       - Therefore, the exact critical threshold is $k_0 = 192$ (coverage $\ge 96.0\%$), giving $P(\text{false fail}) = 2.1254 \times 10^{-4} \le 5.0 \times 10^{-4}$.
     - *Correction of Historical Reports (F01):* The original band $[96.0\%, 100\%]$ ($k \ge 192$) already satisfied the budget. Phase 0.2 hand approximations ($P(K \le 191) \approx 0.018, k \ge 189$) and Phase 0.3 unauthentic table figures ($P(K \le 191) = 1.33 \times 10^{-3}, P(K=192) = 4.2 \times 10^{-3}$) are mathematically incorrect and formally retracted.
   - **For $R = 1,000, p = 0.99$:**
     - Exact probabilities computed via Python `math.comb`:
       - $P(K = 977) = 1.6307 \times 10^{-4}, \quad P(K \le 977) = 2.7216 \times 10^{-4}$
       - $P(K = 978) = 3.7967 \times 10^{-4}, \quad P(K \le 978) = 6.5183 \times 10^{-4}$
       - $P(K = 979) = 8.4466 \times 10^{-4}, \quad P(K \le 979) = 1.4965 \times 10^{-3}$
       - $P(K = 980) = 1.7919 \times 10^{-3}, \quad P(K \le 980) = 3.2884 \times 10^{-3}$
     - Critical Threshold Decision:
       - Under the 20-decision budget $\alpha_{\text{check}} = 5.0 \times 10^{-4}$:
         $$P(K < 978) = P(K \le 977) = 2.7216 \times 10^{-4} \le 5.0 \times 10^{-4}$$
         $$P(K < 979) = P(K \le 978) = 6.5183 \times 10^{-4} > 5.0 \times 10^{-4}$$
         Therefore, the threshold is $k_0 = 978$ (coverage $\ge 97.8\%$, $P(\text{false fail}) = 2.7216 \times 10^{-4} \le 5.0 \times 10^{-4}$).
       - Under the legacy budget $\alpha_{\text{check}} = 10^{-3}$:
         $$P(K < 979) = P(K \le 978) = 6.5183 \times 10^{-4} \le 10^{-3}$$
         Therefore, $k_0 = 979$ (coverage $\ge 97.9\%$, $P(\text{false fail}) = 6.5183 \times 10^{-4} \le 10^{-3}$).
     - *Correction Note:* Phase 0.2 proposed $k \ge 980$, which had $P(K < 980) = P(K \le 979) \approx 1.50 \times 10^{-3} > 10^{-3}$, violating the false-failure budget.
3. **Skewness Limitation:** CLT-based intervals under-cover for highly skewed payoff distributions (e.g., deep out-of-the-money options) when sample size $N$ is small; this limitation must be recorded in the test documentation.
4. **Fixed-Seed Regression Tests (`[DOC_REPRO]`):** Fixed-seed tests verify local regression and reproducibility only, not statistical correctness. Because golden values may change across NumPy releases or CPU SIMD vectorizations, golden reference values must be recorded alongside exact library versions and platform metadata.

---

### 3.2 Stochastic Calculus: Itô Discrepancy & Quadratic Variation

#### Mathematical Convention:
Stochastic integrals are defined under the **Itô convention** with **left-endpoint evaluation**:
$$I_{\text{discrete}} = \sum_{k=0}^{M-1} W_{t_k} (W_{t_{k+1}} - W_{t_k})$$

#### Deterministic Algebraic Invariant (`[MATH_INVARIANT]`):
By the algebraic telescoping identity on discrete increments:
$$W_T^2 = \sum_{k=0}^{M-1} (W_{t_{k+1}}^2 - W_{t_k}^2) = 2 I_{\text{discrete}} + \sum_{k=0}^{M-1} (\Delta W_k)^2$$
Rearranging gives the exact, pathwise identity:
$$I_{\text{discrete}} - \frac{1}{2}(W_T^2 - T) \equiv \frac{1}{2}\left( T - \sum_{k=0}^{M-1} (\Delta W_k)^2 \right)$$
- *Acceptance Criterion (`[MATH_INVARIANT]`):* For every simulated path, the identity must hold within a deterministic floating-point tolerance:
  $$\left| \left(I_{\text{discrete}} - \frac{1}{2}(W_T^2 - T)\right) - \frac{1}{2}\left(T - \sum_{k=0}^{M-1} (\Delta W_k)^2\right) \right| \le 10 \times \epsilon_{\text{mach}} \times M \times \max(1, W_T^2)$$
  Where $\epsilon_{\text{mach}} \approx 2.22 \times 10^{-16}$.

#### Quadratic Variation & $L^2$ RMS Convergence (`[NUM_TOLERANCE]`):
$\sum_{k=0}^{M-1} (\Delta W_k)^2$ does not converge to $T$ in the sense of pathwise total variation (Brownian motion paths have infinite total variation almost surely). Instead, $\sum (\Delta W_k)^2$ converges to $T$ in $L^2$ (mean-square norm) and in probability (and almost surely along dyadic subdivisions).
Since $\text{Var}\left(\sum (\Delta W_k)^2\right) = 2 T \Delta t$, the theoretical RMS error is:
$$\left\| I_{\text{discrete}} - \frac{1}{2}(W_T^2 - T) \right\|_{L^2} = \sqrt{\frac{T \Delta t}{2}} = \mathcal{O}(\sqrt{\Delta t})$$
- *Acceptance Criterion (`[NUM_TOLERANCE]`):*
  - Evaluate across a geometric step-size grid $M \in \{64, 128, 256, 512, 1024\}$ using $B = 10$ batches of $P = 1,000$ paths each.
  - Compute the linear regression slope of $\ln(\text{RMS Error})$ against $\ln(\Delta t)$ across the batches, with standard error $\text{SE}$ estimated from the inter-batch variation of the slope.
  - The theoretical slope of $0.5$ must fall inside the budget-derived interval:
    $$0.5 \in [\bar{\beta} - t_{\text{crit}} \times \text{SE}(\bar{\beta}), \, \bar{\beta} + t_{\text{crit}} \times \text{SE}(\bar{\beta})]$$
    where $t_{\text{crit}} = t_{0.99975, \, \text{df}=9} \approx 5.290654 \approx 5.29$ for $B = 10$ batches ($df = 9$) under the 20-decision suite budget $\alpha_{\text{check}} = 5.0 \times 10^{-4}$ (or $t_{\text{crit}} = t_{0.9995, \, \text{df}=9} \approx 4.780913 \approx 4.78$ under legacy $\alpha_{\text{check}} = 10^{-3}$).
    - *Methodological Derivation:* Because the sample variance of the batch slopes is estimated from $B = 10$ batches ($df = B - 1 = 9$), the pivot statistic follows Student-$t$ with 9 degrees of freedom. Mistakenly using the normal multiplier $z \approx 3.48$ (or $3.29$) would result in an actual false-failure probability of $6.93 \times 10^{-3}$ (or $9.37 \times 10^{-3}$), violating the budget by up to $14\times$. Therefore, Student-$t$ critical values are required for small-sample batch estimation. (Alternatively, if all $B \times P$ paths are pooled directly into an asymptotic normal regression, $z \approx 3.48$ may be used).
  - **Informativeness Condition (Stated Separately):** $\text{SE}(\bar{\beta}) \le 0.05$ ensures sufficient statistical power to distinguish order $0.5$ from order $1.0$.

---

### 3.3 SDE Solvers: Strong & Weak Convergence

For the 1D autonomous stochastic differential equation $dX_t = a(X_t) dt + b(X_t) dW_t$:

#### Regularity Assumptions:
1. **Euler-Maruyama:** Requires global Lipschitz continuity and linear growth bounds on drift $a(x)$ and diffusion $b(x)$. Theoretical orders: Strong $\gamma = 0.5$, Weak $\beta = 1.0$.
2. **Milstein:** Requires $b(x) \in C^1$ with Lipschitz derivative $b'(x)$. In multi-dimensional noise, commutativity is required to avoid Lévy area simulation; for 1D scalar noise, commutativity is trivially satisfied. Theoretical orders: Strong $\gamma = 1.0$, Weak $\beta = 1.0$.

#### Exact Closed-Form Squared Strong Error for GBM (`[MATH_INVARIANT]` / Analytical Ground Truth):
For 1D Geometric Brownian Motion ($a(X) = \mu X, b(X) = \sigma X$) driven by the same Brownian increments on $[0, T]$:
$$\mathbb{E}[(X_T - X_N)^2] = \mathbb{E}[X_T^2] - 2 \mathbb{E}[X_T X_N] + \mathbb{E}[X_N^2]$$
where:
- $\mathbb{E}[X_T^2] = x_0^2 \exp((2\mu + \sigma^2)T)$
- $\mathbb{E}[X_N^2] = x_0^2 [f_2(h)]^N$
- Euler discrete factor: $f_2^{\text{Euler}}(h) = 1 + (2\mu + \sigma^2)h + \mu^2 h^2$
- Milstein discrete factor: $f_2^{\text{Milstein}}(h) = c^2 + \sigma^2 h + c\sigma^2 h + 0.75 \sigma^4 h^2$, with $c = 1 + \mu h - 0.5 \sigma^2 h$
- Cross-Term Derivations:
  - Let $D_k = W_{t_{k+1}} - W_{t_k} \sim \mathcal{N}(0, h)$. The continuous step increment is $X_{t_{k+1}} = X_{t_k} \exp((\mu - \frac{1}{2}\sigma^2)h + \sigma D_k)$.
  - Using the moment generating function results for standard Gaussian increments:
    $$\mathbb{E}[e^{\sigma D}] = e^{\frac{1}{2}\sigma^2 h}, \quad \mathbb{E}[D e^{\sigma D}] = \sigma h e^{\frac{1}{2}\sigma^2 h}, \quad \mathbb{E}[D^2 e^{\sigma D}] = (h + \sigma^2 h^2) e^{\frac{1}{2}\sigma^2 h}$$
  - **Euler Cross Term:**
    $$X_{k+1} = X_k (1 + \mu h + \sigma D)$$
    $$\mathbb{E}[X_{t_{k+1}} X_{k+1} \mid X_{t_k}, X_k] = X_{t_k} X_k e^{(\mu - \frac{1}{2}\sigma^2)h} \left[ (1 + \mu h) \mathbb{E}[e^{\sigma D}] + \sigma \mathbb{E}[D e^{\sigma D}] \right]$$
    $$= X_{t_k} X_k e^{(\mu - \frac{1}{2}\sigma^2)h} \left[ (1 + \mu h) e^{\frac{1}{2}\sigma^2 h} + \sigma^2 h e^{\frac{1}{2}\sigma^2 h} \right] = X_{t_k} X_k e^{\mu h} (1 + \mu h + \sigma^2 h)$$
    By independence of increments across all $N$ steps:
    $$\mathbb{E}[X_T X_N^{\text{Euler}}] = x_0^2 \left[ e^{\mu h} (1 + \mu h + \sigma^2 h) \right]^N$$
  - **Milstein Cross Term:**
    $$X_{k+1} = X_k \left[ 1 + \mu h + \sigma D + \frac{1}{2}\sigma^2(D^2 - h) \right] = X_k \left[ (1 + \mu h - \frac{1}{2}\sigma^2 h) + \sigma D + \frac{1}{2}\sigma^2 D^2 \right]$$
    Let $c = 1 + \mu h - \frac{1}{2}\sigma^2 h$. The conditional expectation is:
    $$\mathbb{E}[X_{t_{k+1}} X_{k+1} \mid X_{t_k}, X_k] = X_{t_k} X_k e^{(\mu - \frac{1}{2}\sigma^2)h} \left[ c \mathbb{E}[e^{\sigma D}] + \sigma \mathbb{E}[D e^{\sigma D}] + \frac{1}{2}\sigma^2 \mathbb{E}[D^2 e^{\sigma D}] \right]$$
    $$= X_{t_k} X_k e^{(\mu - \frac{1}{2}\sigma^2)h} e^{\frac{1}{2}\sigma^2 h} \left[ c + \sigma(\sigma h) + \frac{1}{2}\sigma^2 (h + \sigma^2 h^2) \right]$$
    $$= X_{t_k} X_k e^{\mu h} \left[ 1 + \mu h - \frac{1}{2}\sigma^2 h + \sigma^2 h + \frac{1}{2}\sigma^2 h + \frac{1}{2}\sigma^4 h^2 \right] = X_{t_k} X_k e^{\mu h} \left( 1 + \mu h + \sigma^2 h + \frac{1}{2}\sigma^4 h^2 \right)$$
    By independence across all $N$ steps:
    $$\mathbb{E}[X_T X_N^{\text{Milstein}}] = x_0^2 \left[ e^{\mu h} \left( 1 + \mu h + \sigma^2 h + \frac{1}{2}\sigma^4 h^2 \right) \right]^N$$
  - *Verification Check:* This derived Milstein cross-moment factor has been numerically confirmed against double precision Gaussian numerical quadrature to within $3.48 \times 10^{-14}$.

#### Deterministic SDE Single-Step Reference Check (`M1-SDE-06`, `[MATH_INVARIANT]` / `[NUM_TOLERANCE]`):
*(Critical Correctness Test: Discloses wrong drift, wrong diffusion, or Euler/Milstein implementation mix-ups deterministically to machine precision).*
1. **Mathematical Model & Explicit Assumptions:**
   - **Continuous Model:** 1D autonomous Geometric Brownian Motion (GBM) under the Itô stochastic calculus convention:
     $$dX_t = a(X_t) dt + b(X_t) dW_t = \mu X_t dt + \sigma X_t dW_t, \quad X_0 = x_0$$
   - **Diffusion Spatial Derivative:** $b(x) = \sigma x$ is continuously differentiable ($b \in C^1$) with spatial derivative $b'(x) = \sigma$. The second-order Itô-Taylor expansion coefficient is therefore $\frac{1}{2} b(x) b'(x) = \frac{1}{2} \sigma^2 x$.
   - **Evaluation Convention:** Drift $a(X_0) = \mu x_0$, diffusion $b(X_0) = \sigma x_0$, and diffusion derivative $b(X_0)b'(X_0) = \sigma^2 x_0$ are evaluated strictly at the left-endpoint of the time interval $[0, h]$.
   - **Commutativity:** In dimension $d=1$ (single scalar Wiener process $W_t$), commutativity of diffusion vector fields is trivially satisfied; double stochastic integrals reduce algebraically to $\frac{1}{2}((\Delta W)^2 - h)$ without Lévy area simulation.
   - **Fixed Deterministic Increments:** Integration is performed over a single deterministic step $h = 0.01$ starting from $x_0 = 100.0$, with parameters $\mu = 0.05$ and $\sigma = 0.2$. The Brownian increment is assigned the fixed, deterministic value:
     $$\Delta W = 2\sqrt{h} = 2\sqrt{0.01} = 0.2$$
   - **Non-Zero Increment Criterion:** The increment is specifically selected such that $(\Delta W)^2 - h = (0.2)^2 - 0.01 = 0.04 - 0.01 = 0.03 \ne 0$. This ensures that the Milstein second-order correction term is strictly non-zero, creating an intentional, deterministic separation between Milstein and Euler outputs.
2. **Analytical Closed-Form Step Values:**
   - **Euler-Maruyama Step:**
     $$X_1^{\text{Euler}} = x_0 + a(x_0) h + b(x_0) \Delta W = x_0 (1 + \mu h + \sigma \Delta W) = 100.0 \times (1 + 0.05(0.01) + 0.2(0.2)) = 100.0 \times (1 + 0.0005 + 0.04) = 104.0500000000$$
   - **Milstein Step:**
     $$X_1^{\text{Milstein}} = X_1^{\text{Euler}} + \frac{1}{2} b(x_0) b'(x_0) \left( (\Delta W)^2 - h \right) = 104.05 + \frac{1}{2} (100.0)(0.2)^2 (0.04 - 0.01) = 104.05 + 0.5(100.0)(0.04)(0.03) = 104.05 + 0.06 = 104.1100000000$$
   - **Exact Step Difference:**
     $$X_1^{\text{Milstein}} - X_1^{\text{Euler}} \equiv +0.060000000000$$
3. **Acceptance Rule (`[MATH_INVARIANT]`):**
   When invoked on the deterministic single-step fixture, the solver implementation outputs must match the exact analytical values within relative floating-point tolerance $10^{-12}$:
   $$|X_1^{\text{solver, Euler}} - 104.05| < 10^{-12}, \quad |X_1^{\text{solver, Milstein}} - 104.11| < 10^{-12}$$
   - *Failure Detection:* If an Euler implementation is mistakenly returned for Milstein or vice versa, the error is $|0.06| \gg 10^{-12}$, triggering an immediate, deterministic test failure without Monte Carlo sampling ambiguity.

#### Strong Convergence Acceptance Protocol (`[NUM_TOLERANCE]`):
1. **Simulation Grid:** Step counts $N \in \{16, 32, 64, 128, 256\}$ for $T = 1.0$ ($h = T/N \in \{1/16, 1/32, 1/64, 1/128, 1/256\}$). Coarse increments are obtained by summing fine Brownian increments from the finest grid $N = 256$.
2. **Independent Batch-Level Estimation:** $B = 10$ independent batches of $P = 1,000$ paths each.
   - For each batch $b \in \{1, \dots, B\}$, fit the log-log slope $\hat{\beta}_b$ of strong error versus $h$.
   - Mean fitted slope: $\bar{\beta} = \frac{1}{B} \sum_{b=1}^B \hat{\beta}_b$.
   - Empirical standard error: $\text{SE}(\bar{\beta}) = \frac{S_\beta}{\sqrt{B}}$, where $S_\beta = \sqrt{\frac{1}{B-1}\sum_{b=1}^B (\hat{\beta}_b - \bar{\beta})^2}$.
3. **Acceptance Rule (Student-$t$ with $df = 9$):**
   - Because the sample variance is estimated from $B = 10$ batches ($df = 9$), the pivot statistic follows Student-$t$ with 9 degrees of freedom.
   - For per-decision budget $\alpha_{\text{check}} = 5.0 \times 10^{-4}$, the critical multiplier is $t_{\text{crit}} = t_{0.99975, \, \text{df}=9} \approx 5.290654 \approx 5.29$.
   - The fitted Monte Carlo mean slope $\bar{\beta}$ must agree with the exact theoretical slope $\beta_{\text{exact}}$ computed from the exact closed-form strong error over the **identical grid** within $t_{\text{crit}} \times \text{SE}(\bar{\beta})$:
     $$|\bar{\beta} - \beta_{\text{exact}}| \le t_{\text{crit}} \times \text{SE}(\bar{\beta}), \quad t_{\text{crit}} \approx 5.29$$
4. **Diagnostic Reporting (No Pass/Fail Gate):** Separately report how far $\beta_{\text{exact}}$ is from the asymptotic order ($0.5$ for Euler, $1.0$ for Milstein) to transparently quantify pre-asymptotic curvature.
5. **Informativeness Condition (Diagnostic Precondition):** $\text{SE}(\bar{\beta}) \le 0.05$ is an experimental design diagnostic. If $\text{SE} > 0.05$, the test harness emits an "INFORMATIVENESS WARNING: Insufficient precision to resolve slope; increase batch count or path count" rather than failing the solver code.
6. **Negative Control Requirement:** The test harness must verify that claiming an incorrect order (e.g. order 1.0 for Euler-Maruyama) is rejected.

#### Weak Error Validation (Two Separate Tests):
1. **Formula-Level Analytical Reference Check (`[MATH_INVARIANT]` / `[NUM_TOLERANCE]`):**
   *(Note: This verifies the mathematical recursion formula itself; it does NOT exercise the simulation solver code).*
   - Euler second-moment recursion: $\mathbb{E}[X_N^2] = x_0^2 (1 + (2\mu + \sigma^2)h + \mu^2 h^2)^N$
   - Milstein second-moment recursion: $\mathbb{E}[X_N^2] = x_0^2 (c^2 + \sigma^2 h + c\sigma^2 h + 0.75 \sigma^4 h^2)^N$
   - Exact continuous limit: $\mathbb{E}[X_T^2] = x_0^2 \exp((2\mu + \sigma^2)T)$
   - Acceptance: Deterministic weak error evaluated across $h$; log-log slope must lie in $[0.90, 1.10]$.
2. **Solver Implementation Weak-Moment Acceptance Check (`M1-SDE-05`, `[STAT_VALIDATION]`):**
   *(Exercises the solver implementation on $P=20,000$ paths).*
   - For fixed step count $N$ and $P = 20,000$ paths generated by the solver implementation, compute empirical sample mean $\overline{X_N^2} = \frac{1}{P} \sum_{i=1}^P (X_N^{(i)})^2$ and standard error $\text{SE} = S(X_N^2)/\sqrt{P}$.
   - The sample mean must agree with the exact discrete-scheme moment $x_0^2 [f(h)]^N$ within budget-derived normal tolerance ($z \approx 3.48$ for $\alpha_{\text{check}} = 5 \times 10^{-4}$):
     $$\left| \overline{X_N^2} - x_0^2 [f(h)]^N \right| \le z \times \frac{S(X_N^2)}{\sqrt{P}}, \quad z \approx 3.48$$
   - **Status:** `NOT STARTED`. Added to Milestone 1 checklist and `PROJECT_STATE.md`.

---

### 3.4 Variance Reduction Evaluation (`[STAT_VALIDATION]`)

The Variance Reduction Factor (VRF) is defined as the ratio of sample variances per equal number of function evaluations:
$$\text{VRF} = \frac{\widehat{\text{Var}}(\hat{Y}_{\text{crude}}) \times \text{Cost}_{\text{crude}}}{\widehat{\text{Var}}(\hat{Y}_{\text{reduced}}) \times \text{Cost}_{\text{reduced}}}$$
- **Protocol:**
  - For a ratio of two independent sample variances computed from $N_a$ and $N_b$ samples under normality, the relative standard error is approximately:
    $$\frac{\text{SE}(\widehat{\text{VRF}})}{\widehat{\text{VRF}}} \approx \sqrt{\frac{2}{N_a - 1} + \frac{2}{N_b - 1}}$$
  - For heavy-tailed or non-normal payoffs, the normal formula understates the standard error. Therefore, tests must compute an **empirical standard error** from repeated independent batches ($B \ge 10$).
- **No Unjustified Hardcoded Thresholds:** VRF depends on correlation $\rho(Y, X)$, moneyness, and volatility. Hardcoded thresholds (such as $\text{VRF}_{\text{Antithetic}} \ge 1.2$ or $\text{VRF}_{\text{Control}} \ge 2.5$) are removed.
- **Pilot Requirement:** Thresholds are to be set from a recorded pilot measurement (recording seed, $N$, estimate, and uncertainty); not yet measured.

---

### 3.5 Heavy Tails, Hill Estimator & Extreme Value Theory

#### Tail Index Estimation on Synthetic Pareto (`[STAT_VALIDATION]`):
For an exact Pareto sample with tail index $\alpha$, the Hill estimator of $1/\alpha$ on the top $k$ order statistics has asymptotic relative standard deviation:
$$\text{Relative SD} = \frac{1}{\sqrt{k}}$$
- *Acceptance Criterion (`[STAT_VALIDATION]`):*
  On a synthetic Pareto sample of size $n = 50,000$ with $\alpha = 2.5$ and threshold index $k = 1,000$:
  The relative estimation error must not exceed $z$ asymptotic standard deviations:
  - Under the 20-decision suite budget $\alpha_{\text{check}} = 5.0 \times 10^{-4}$ ($z = \Phi^{-1}(0.99975) \approx 3.480756 \approx 3.48$):
    $$\frac{|\hat{\alpha} - \alpha_{\text{true}}|}{\alpha_{\text{true}}} \le \frac{z}{\sqrt{k}} \approx \frac{3.480756}{\sqrt{1000}} \approx 0.11007 \approx 11.0\%$$
  - Under the legacy budget $\alpha_{\text{check}} = 10^{-3}$ ($z = \Phi^{-1}(0.9995) \approx 3.290527 \approx 3.29$):
    $$\frac{|\hat{\alpha} - \alpha_{\text{true}}|}{\alpha_{\text{true}}} \le \frac{z}{\sqrt{k}} \approx \frac{3.290527}{\sqrt{1000}} \approx 0.10405 \approx 10.4\%$$
  *(Note: Reallocated to Phase 4 Quantitative Research Protocol per dependency order).*

#### EVT Peaks-Over-Threshold & Crash Backtesting (`[EMP_HYPOTHESIS]`):
- **Independent Fixture Requirement (`[STAT_VALIDATION]`):** Kupiec POF Likelihood Ratio and Christoffersen independence test statistics must be produced independently of the implementation under test (either hand-derived values with the exact formula and inputs written out in the document, or values from an independent reference implementation whose name and version are recorded). Numerical implementation must match fixtures within relative tolerance $10^{-8}$.
- **Empirical Crash Evaluation (`[EMP_HYPOTHESIS]`):** Unconditional POT and conditional GARCH-EVT are evaluated on historical crash periods (2008 GFC, March 2020). Test statistics, $p$-values, exception clusters, and limitations are reported. Unconditional models failing independence during high-clustering periods are documented as empirical findings, not software bugs.

---

### 3.6 Convex Optimization & KKT Conditions

#### Analytical 2-Asset Minimum Variance Formula (`[MATH_INVARIANT]`):
For 2 assets with covariance matrix $\Sigma = \begin{pmatrix} \sigma_1^2 & \sigma_{12} \\ \sigma_{12} & \sigma_2^2 \end{pmatrix}$, the unconstrained minimum variance weight is:
$$w_1^* = \frac{\sigma_2^2 - \sigma_{12}}{\sigma_1^2 + \sigma_2^2 - 2\sigma_{12}}, \quad w_2^* = 1 - w_1^*$$
- **Boundary Conditions:**
  - If $\sigma_{12} > \sigma_2^2$, unconstrained $w_1^* < 0$; the constrained long-only solution is on the boundary: $w_1 = 0, w_2 = 1$.
  - If $\sigma_{12} > \sigma_1^2$, unconstrained $w_1^* > 1$; the constrained long-only solution is: $w_1 = 1, w_2 = 0$.
- *Acceptance Criterion (`[MATH_INVARIANT]` / `[NUM_TOLERANCE]`):* Numerical CVXPY solver matches analytical weights within absolute tolerance $10^{-7}$.

#### Solver Residuals & Status Audit (`[NUM_TOLERANCE]`):
Instead of the ambiguous term "primal gap", tests must audit standard optimization quantities:
1. **Solver Status:** Reject "solved inaccurate", "optimal inaccurate", or "user limit" as passes; solver must return `OPTIMAL`.
2. **Primal Residual:** $\|\mathbf{1}^T w - 1\|_\infty \le \epsilon_{\text{primal}}$ and $\min(w) \ge -\epsilon_{\text{primal}}$.
3. **Dual Residual & Duality Gap:** Audited against configured solver tolerances (for OSQP: `eps_abs = 1e-7, eps_rel = 1e-7`, requiring residuals $\le 10^{-6}$).

---

### 3.7 Gaussian Process Uncertainty & Continuous Calibration

- **Synthetic Prior Validation (`[STAT_VALIDATION]`):** On synthetic datasets drawn from the GP prior with known covariance kernel and noise, empirical coverage of $95\%$ credible intervals is evaluated across independent datasets using exact binomial confidence bands.
- **Continuous Calibration (`[EMP_HYPOTHESIS]`):** Replaced Expected Calibration Error (ECE, designed for discrete classification) with continuous regression calibration tools:
  - Probability Integral Transform (PIT) uniformity histogram.
  - Coverage-versus-nominal calibration curves.
  - Continuous Ranked Probability Score (CRPS).
- **Noise-Free Interpolation (`[NUM_TOLERANCE]`):** Interpolation error on smooth target functions $< 10^{-4}$ with explicit diagonal jitter ($\epsilon = 10^{-8}$) to ensure Cholesky stability.

---

### 3.8 Random Number Generation & Reproducibility Policy (`[DOC_REPRO]`)

1. **RNG Architecture:** Standardized on NumPy's modern `Generator` using `PCG64`, initialized from an explicit `SeedSequence`.
2. **Parallel & Batch Spawning:** Independent replications and parallel test workers must spawn independent streams via `SeedSequence.spawn()`; no shared global state or reliance on `np.random.seed()`.
3. **Numeric Comparison Policy:**
   - Float64 array equality is asserted using combined relative and absolute tolerances:
     $$\text{assert } \text{np.allclose}(a, b, \, \text{rtol}=10^{-9}, \, \text{atol}=10^{-12})$$
   - Exact bitwise identity is asserted **only** on the same machine, same OS, and identical NumPy library version. Cross-platform assertions use standard numerical tolerances.
