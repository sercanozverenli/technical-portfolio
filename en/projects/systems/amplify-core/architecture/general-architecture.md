# General System Architecture
![Data Routing Architecture](../../../../../docs/diagrams/amplify-core/figure-1.png)

## What does this system do?

Amplify Core is not a prediction model — it's a **safety layer that sits in front of** the prediction model. Its purpose is to ask one question before any machine learning model gets involved:

> "Can I produce a reliable decision with the data I currently have?"

In the real world, data rarely arrives perfect: sensors fail, networks drop, records go missing or contradict each other. Most systems are forced to produce an output regardless — this creates the risk of "false confidence": the system appears certain but is wrong. Amplify Core instead evaluates the data first, then routes it down one of four different paths.

## Four regimes, four different behaviors

The diagram above shows the system's end-to-end flow. Raw data first passes through the **DRS Layer** (Data Reliability Score), which produces a single number between 0 and 1. The **Routing Engine** looks at this score and directs the data into one of four regimes:

| Regime | DRS range | What happens |
|---|---|---|
| **Clean** | ≥ 0.80 | Data is reliable, the main model produces a prediction directly |
| **Noisy** | 0.50 – 0.79 | Data has correctable degradation; the Stabilization Layer steps in to improve it, then it's re-evaluated |
| **Corrupted** | 0.25 – 0.49 | Degradation is severe, the main model is disabled; a simpler, more conservative Fallback Model is used instead |
| **Information Collapse** | < 0.25 | Data no longer carries enough statistical information to support a reliable decision; the system deliberately stops producing predictions (Abstention / Safe Standby) |

The critical point: the last row is not a failure — it's a design decision. The system is capable of saying "I don't know," because in some cases producing a wrong prediction is more costly than producing no prediction at all (example: a wrong diagnosis, a wrong financial transaction, a wrong production decision).

## Why this architecture matters — in short

- **Model-agnostic:** The DRS Layer and Routing Engine work with the same logic regardless of which prediction model runs behind them (regression, XGBoost, a simple rule engine). Swapping the model doesn't affect these two layers.
- **Domain-adaptive:** The techniques inside the Stabilization Layer change depending on data type (numerical time series, tabular data, financial data, text) — but the decision logic (DRS thresholds, the four regimes) always stays the same.
- **Transparent:** Every decision is reported along with which indicator (missingness, noise ratio, etc.) crossed its threshold — not a black box.

## For engineers: the technical role of each component

1. **DRS Layer** — produces a single score through seven statistical indicators (missingness, signal-to-noise ratio, autocorrelation, outlier density, variance stability, Shannon entropy, drift), combined via weighted summation and a multiplicative veto mechanism. It is model-agnostic; it only looks at the statistical properties of the raw data. *(Full formulation: [DRS Layer](drs-layer.md))*
2. **Routing Engine** — compares the DRS score against thresholds and makes a deterministic (no training required) routing decision. Implemented via the Strategy pattern.
3. **Stabilization Layer** — active only in the Noisy regime. Uses data-type-specific techniques (rolling median + CUSUM, interpolation + winsorizing, EWMA + bootstrap, edit-distance denoising). After stabilization, the DRS score is capped at 0.75 (imputation penalty) — meaning stabilized data can never re-enter the Clean regime.
4. **Fallback Model** — a low-complexity, wide-confidence-interval, conservative predictor that runs in the Corrupted regime.
5. **Abstention / Safe Standby** — activates in the Information Collapse regime; logs the data, moves on to the next record without stopping the system, and triggers a human-intervention alert if collapse occurs repeatedly in succession.

## Two diagrams, two levels

The diagram on this page shows the system's **macro flow** — "where does the data go." To see the DRS Layer's **internal mathematics** (Z-score normalization, weighting, multiplicative veto, final score calculation), go to the next page:

→ [DRS Layer — Data Reliability Scoring](drs-layer.md)
