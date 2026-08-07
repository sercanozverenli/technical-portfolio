# DRS Layer — Data Reliability Scoring

## What does this layer do?

"Garbage in, garbage out" is the most fundamental problem in data science. Before sending incoming data to any prediction model, the DRS Layer runs it through a "health check" and produces a single number between 0.0 and 1.0: the **Data Reliability Score (DRS)**.

This score lets the system tell the model "trust this data" or "don't trust this data" — the model is never left alone with bad data. The DRS Layer never looks at any prediction model; it only looks at the data's own statistical properties. This is why it is **model-agnostic** — even if the main prediction model changes tomorrow, this layer stays exactly the same.

## Seven indicators — what are we actually measuring?

Seven different angles are continuously measured to determine data reliability. Each one is chosen to catch a different type of degradation:

| Indicator | What it measures | Why it matters |
|---|---|---|
| **Missingness** | Proportion of empty/invalid fields | If missingness is too high, the model starts working off its own assumptions instead of real data |
| **Signal-to-Noise Ratio (SNR)** | Ratio of meaningful signal to noise | Low SNR means the real information in the data has been lost |
| **Temporal Consistency (Autocorrelation)** | Whether consecutive data points support one another | A broken continuity is a sign of malfunction |
| **Outlier Density** | Frequency of values outside the expected range | A single outlier is normal; a cluster of them points to a structural fault |
| **Variance Stability** | Whether volatility changes over time (Levene test) | A sudden variance shift signals a problem at the data source |
| **Information Disorder (Shannon Entropy)** | Amount of "new information" in the data | When entropy approaches maximum, the data has turned into random noise |
| **Drift** | Deviation of the mean from baseline (CUSUM/EWMA) | The data may have silently changed character |

These seven indicators are activated according to a predefined configuration based on data type. For example, in tabular data made up of independent records (like Olist), Autocorrelation is meaningless and gets masked; the remaining six indicators continue operating with their weights re-normalized to sum to 1. In time-series data (UJIIndoorLoc, Yahoo Finance), all seven indicators stay active.

## Calculation flow: from raw data to final score

The diagram below shows the path from the moment data enters the system to the final DRS score, through two parallel processes: on the left, the **Warm-up Phase** where the system learns its own baseline from the first 100 observations; on the right, the **Real-Time Processing Pipeline** that runs for every new data point.

![DRS Layer Calculation Stages](../../../../../docs/diagrams/amplify-core/figure-2.png)

Three stages run in sequence:

1. **Z-Score Normalization** — The seven indicators arrive in different units (SNR in decibels, missingness as a percentage, entropy in bits, etc.). Each is first standardized via Z-score, then scaled to a [0,1] range via min-max scaling; the result is denoted Nᵢ. Without this step, unit differences would distort the score. Nᵢ expresses magnitude only — it does not capture whether the indicator moves in the same direction as data quality or the opposite. For this reason, Nᵢ passes through a **Direction Transformation** before entering the weighted sum: three of the seven indicators (SNR, Autocorrelation, Variance Stability) are positive-direction, so Sᵢ = Nᵢ; the remaining four (Missingness, Outlier Density, Shannon Entropy, Drift) are negative-direction, so Sᵢ = 1 − Nᵢ.
2. **Weighted Sum** — The direction-corrected seven indicators (Sᵢ) are multiplied by their weights and summed. Initially, each indicator receives equal weight (1/7 ≈ 14.3%). A mechanism running periodically in the background (the Entropy Weighting Method — EWM) automatically updates these weights based on the amount of information (information entropy) each indicator carries — the method measures and weights the indicators' information content, not their variance.
3. **Multiplicative Veto Filter** — This is the system's safety barrier. If any critical indicator (for example, missingness exceeding 50%) crosses into an unacceptable level, the final score is instantly zeroed out — even if every other indicator is perfect. This way, one "well-behaved" indicator can never mask a critical failure.

**Final formula:**

$$DRS = \left( \sum_{i=1}^{7} w'_i \cdot S_i \right) \times \prod_{j=1}^{7} V_j$$

- $N_i$: the indicator value normalized to [0,1] via Z-score followed by min-max scaling (its state before Direction Transformation)
- $S_i$: $N_i$ after Direction Transformation; the actual component score entering the final formula. For positive-direction indicators, $S_i = N_i$; for negative-direction indicators, $S_i = 1 - N_i$.
- $w'_i$: the weight normalized according to the domain configuration
- $V_j$: the veto value — 0 if the threshold is crossed, 1 otherwise

## Why are the "first 100 observations" special?

When the system first starts, it doesn't yet know the data's normal range — this is the "cold-start" problem. The solution: the first 100 observations are reserved as a **Warm-up Phase**. During this period, the system automatically behaves conservatively (routing to the Fallback Model, producing no autonomous decisions) while it learns the dataset's own statistical baseline.

Once the warm-up phase ends, the learned parameters ($\mu$, $\sigma$, $w_i$) are **frozen** — this is called the **Data Leakage Barrier**. This lock ensures that new data arriving during real-time processing cannot reach back and corrupt the calibration; the system stays both stable and testable.

## Why this design was chosen

- **Why a multiplicative veto instead of a simple average?** In additive scoring systems, one indicator being very high can mask a critical collapse in another (a Simpson's Paradox risk). For example, data with perfect SNR but 90% missingness could still score high under an additive formula. The multiplicative veto prevents this: no indicator can ever cover up another's failure.
- **Why dynamic weighting (EWM) instead of fixed, human-assigned weights?** Rather than setting weights manually or through subjective methods like AHP, a data-driven approach is used — this lets indicators such as Drift in financial data or SNR in IoT data rise to prominence without human intervention.
- **How is the post-stabilization score bounded?** Instead of a fixed cap, an exponential discount function is used that accounts for both the data's starting quality and the intensity of the intervention applied (Intervention Strength — IS): $Recovered = Stabilized \times e^{-(1-Stabilized) \times IS}$. This approach penalizes data that started at high raw quality less, and data that started weaker more; if the Recovered score exceeds 0.80, the data can re-enter the Clean regime.

---

Once the DRS score is produced, it becomes the input to the **Routing Engine**, which decides which of the four regimes the data belongs to:

→ [Routing Engine](en/projects/systems/amplify-core/architecture/routing-engine.md)
