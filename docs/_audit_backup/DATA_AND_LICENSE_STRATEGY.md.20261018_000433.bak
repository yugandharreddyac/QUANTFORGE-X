# DATA PROVENANCE, INTEGRITY & LICENSE STRATEGY

**QUANTFORGE X — Open-Source Financial Data Architecture & Governance Policy**

---

## 1. Core Principles of Data Governance & Policy

Scientific integrity in quantitative finance depends strictly on data realism, provenance transparency, and uncompromising leakage prevention. QUANTFORGE X enforces four strict governance policies:

1. **Synthetic-First Mathematical Verification:** All mathematical correctness tests, numerical convergence benchmarks (SDE solvers, Itô calculus, Monte Carlo rates), and baseline validations must execute exclusively on synthetic data where ground-truth parameters are known by construction.
2. **Strict Terms & Licensing Verification:** Real market data may be ingested only after its terms of service and usage rights have been formally verified and recorded with URL, access date, and specific license clauses. Any source not yet inspected in this session is classified as `TERMS UNVERIFIED` ("terms not yet inspected").
3. **No Redistribution of Raw Proprietary/Third-Party Data:** Raw third-party market datasets will **NOT** be committed to the repository unless the upstream license explicitly permits public redistribution. Local storage uses cached Parquet files accompanied by immutable provenance manifests.
4. **Point-in-Time Causality & Zero Leakage:** Features engineered at time $t$ must only utilize information known strictly at or before time $t$. Any future timestamp leakage triggers an immediate hard exception.

---

## 2. Data Source Inventory & Terms Classification

| Source Identifier | Source Provider & API | Sampling Frequency | Ingested Fields | Terms Classification | Specific Risks & Biases | QUANTFORGE X Role |
|:---|:---|:---:|:---|:---:|:---|:---|
| **DS-SYNTH-SDE** | Internal Mathematical Path Generator | Micro-step / Discrete $\Delta t$ | Simulated asset paths $S_t$, variance $V_t$, increments $\Delta W_t$ | `TERMS VERIFIED` (Internal code; fully open-source) | Synthetic paths reflect mathematical models (GBM, OU, Heston) rather than human market microstructure. | Mathematical verification of SDE solvers, Itô calculus, Monte Carlo convergence, variance reduction. |
| **DS-EQUITY-DAILY** | Yahoo Finance (via `yfinance` / Stooq) | Daily (EOD) | Open, High, Low, Close, Adjusted Close, Volume | `TERMS UNVERIFIED` ("terms not yet inspected") | **Survivorship Bias:** Delisted firms omitted; **Corporate Actions:** Split adjustments and dividend timing vary; **API Instability:** Unofficial endpoint changes. | Historical factor research, walk-forward validation, empirical portfolio backtesting. |
| **DS-MACRO-RATES** | Federal Reserve Economic Data (FRED API) | Daily / Monthly | 3-Month T-Bill, 10-Year Treasury Yield, CPI, VIX | `TERMS UNVERIFIED` ("terms not yet inspected") | **Macro Revisions:** CPI and GDP are revised retroactively; point-in-time vintages differ from latest figures; publication lags. | Risk-free rate baseline ($r$), discount curves, yield curve interpolation, macroeconomic regime auditing. |
| **DS-AGENCY-MORTGAGE** | Fannie Mae / Freddie Mac Single-Family Loan Samples | Monthly Loan Level | UPB, Note Rate, Delinquency Status, Prepayment, Zero Balance | `TERMS UNVERIFIED` ("terms not yet inspected") | **Redistribution Prohibited:** Public registration requires non-redistribution agreement; static historical cohort. | Educational Securitized Products module (Phase 2 STRETCH): Collateral loan pool calibration and waterfall testing. |
| **DS-DIFFUSION-SYNTH** | Generative Scenario Engine (`quantforge.deeplearn.diffusion`) | Daily synthetic paths | Simulated multi-day log-return vectors | `TERMS VERIFIED` (Internally generated model output) | Mode collapse risk, distributional drift, failure to capture tail dependencies if unconstrained. | Stress-testing scenario generation, tail-risk simulations, out-of-distribution regime auditing. |

---

## 3. Explicit Data Scope Boundaries

To prevent unrealistic claims and project failure, QUANTFORGE X explicitly enforces the following boundaries:

```
+----------------------------------------------------------------------------------------------------+
|                                    DATA SCOPE BOUNDARY MATRIX                                      |
+----------------------------------------------------------------------------------------------------+
| IN-SCOPE (Validated & Open)                        | OUT-OF-SCOPE (Explicitly Excluded)             |
|----------------------------------------------------+------------------------------------------------|
| * Daily OHLCV adjusted prices                      | * Intraday Level-2 / Level-3 Limit Order Books |
| * Historical dividend & split adjustments          | * Millisecond exchange timestamps (SIP, direct)|
| * Daily Treasury yield curves (FRED)               | * Proprietary Bloomberg/Refinitiv raw tick feeds|
| * Public agency mortgage sample performance pools  | * Survivorship-free historical delisted universe|
| * Synthetic SDE paths with known ground truth      | * Proprietary AlphaGrep trading data or alpha  |
| * Mathematical Monte Carlo path ensembles          | * Real money brokerage execution APIs          |
+----------------------------------------------------------------------------------------------------+
```

---

## 4. Point-in-Time Correctness & Anti-Leakage Protocol

Financial feature engineering is exceptionally susceptible to subtle information leakage. QUANTFORGE X implements an architectural firewall:

```
                      Raw Price Time Series (t = 0, 1, ..., T)
                                      |
                                      v
                 [Temporal Barrier / Chronological Partition]
                                      |
              +-----------------------+-----------------------+
              |                                               |
              v                                               v
    Training Window: [0, t_train]                  Test Window: [t_test, T]
    (Purged: [t_train - embargo, t_train])         (t_test > t_train strictly)
              |                                               |
              v                                               v
    Fit Scaling & Imputation Parameters            Apply Pre-fitted Scalers
    (Mean, Std, PCA, Kernels)                      (Zero recalculation on Test)
              |                                               |
              +-----------------------+-----------------------+
                                      |
                                      v
                         [Leakage Guard Assertion]
           assert max(train_timestamps) < min(test_timestamps)
```

### Specific Guardrail Mechanisms:
1. **Target Shifting Invariant:** For return prediction at horizon $h$, the feature vector $X_t$ is constructed strictly from observations $\{p_\tau\}_{\tau \le t}$, while target $y_t$ is defined as $r_{t \to t+h} = \ln(p_{t+h}/p_t)$. In the feature matrix, target $y_t$ is strictly masked from the input tensor.
2. **Scaler Pre-Fitting:** Normalization parameters (z-score $\mu, \sigma$, min-max bounds, quantile transformers) are calculated exclusively on training partitions. Applying `fit_transform` across the entire time series is prohibited.
3. **Embargo & Purging:** Following de Prado's methodology, when overlapping return targets (e.g. 5-day cumulative returns) are predicted, a purge window equal to the return horizon is inserted between the training set and the test set to eliminate serial correlation leakage.

---

## 5. Storage Format & Provenance Manifests

All historical and generated datasets are stored in an immutable, high-performance local columnar structure:
- **Format:** Apache Parquet with Snappy compression.
- **Indexing:** Chronologically sorted UTC `DateTimeIndex` as the primary key.
- **Metadata Manifest:** Every Parquet file is accompanied by a companion JSON sidecar containing:
  - Source URI and retrieval timestamp.
  - SHA-256 data hash.
  - Column schema, null counts, and data types.
  - Licensing category tag (`TERMS VERIFIED` or `TERMS UNVERIFIED`).
