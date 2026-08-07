# Abstention Mechanism 

![Abstention Mechanism Flow](../../../../../docs/diagrams/amplify-core/abstention.png)

## What does this mechanism do?

The Abstention mechanism is a two-stage safety net that prevents the system from making incorrect decisions under uncertainty. It can be triggered at two distinct points:

1. **Pre-inference Veto:** When the Routing Engine routes data to the Information Collapse regime (DRS < 0.25), no prediction model is executed, and the system directly enters Safe Standby mode.
2. **Post-inference Veto:** Even if the Fallback Model generates a prediction, if the confidence signal falls below the threshold during the Post-inference Confidence Gate ($\tau_{Fallback}$) check, the system rejects the prediction and directly enters Safe Standby mode without executing SCS calculation.

This is not a malfunction — it is a safety behavior by design. When data reliability or prediction confidence weakens, the system deliberately refrains from taking action and effectively states "I cannot make a safe decision with this data."

This is not a malfunction — it is a safety behavior by design. The data's statistical foundation has weakened to the point that any prediction produced at this stage would be no different from a random outcome rather than genuine information. The system recognizes this and effectively says "I cannot make a safe decision with this data."

## Does the system stop, or does it keep going?

A critical distinction here: **Safe Standby does not mean the entire system halts** — it only means decision-making stops for that specific data record. The system follows these steps:

1. Information Collapse is detected, and the system enters Safe Standby.
2. The corresponding data record is written to a **Dead Letter Log** in JSONL format — including the DRS score, which indicators failed, and a timestamp.
3. At the same time, a **counter** tracking how many consecutive Information Collapse events have occurred is incremented.
4. The system does not stop the stream; it moves on to the next data record.
5. When new data arrives, DRS is automatically recalculated.

In other words, the system never enters a "frozen" waiting state — it continues evaluating new data at all times, simply refraining from producing a decision until a reliable signal is found.

## What happens if the data recovers, and what if it doesn't?

If the DRS score calculated for the new data exceeds the 0.25 threshold, the data automatically returns to its corresponding regime (Clean, Noisy, or Corrupted) and normal operation resumes.

If the threshold is not exceeded, the system checks the counter value. If the counter exceeds a certain threshold (example value: 10 consecutive collapses), the system triggers a human-intervention alert **asynchronously**. Being asynchronous matters here: this alert is not an operation that blocks the main data stream — the system continues processing data while a notification is sent in the background. If the counter threshold has not been exceeded, the system continues monitoring silently; no action is triggered.

The counter threshold (shown as N=10 in the diagram above) is currently an indicative starting value — how many consecutive collapses actually signal a genuine anomaly, for which data environment, will be calibrated as the project progresses.

## Abstention modes: Record-level abstention vs. System-level abstention (Circuit Breaker)

In the Amplify Core architecture, Abstention is designed under two operational modes:

- **Record-Level Abstention (PoC Default):** The corrupted record is logged and rejected, and the system immediately processes the next record without halting. This is the optimal approach for independent record streams (e-commerce orders, invoice processing, financial streams).
- **System-Level Abstention (Circuit Breaker Mode — Future Scope):** Interprets severe data corruption as systemic blindness. The system transitions to a "Halted" state and immediately rejects all new incoming requests without blocking, requiring a manual human reset. Designed for safety-critical industrial environments such as production line sensors and medical dosage systems.

## Why is the stream never stopped in Record-Level Abstention?

In the Record-Level Abstention mode implemented for the PoC, the stream is not halted for the following reasons:

- **A single bad record should not block the entire system.** Information Collapse is often a transient condition (for example, a momentary sensor outage); halting the stream would also delay the processing of the next, healthy data.
- **Human intervention should only be triggered when it's genuinely needed.** The counter mechanism is designed to catch a persistent pattern (N consecutive occurrences), not a one-off collapse. This prevents unnecessary alert fatigue.
- **Logging preserves traceability without stopping anything.** Thanks to the Dead Letter Log, no collapse event is ever lost — even though the system doesn't stop, every event can be reviewed retroactively.

## Next layer

Abstention ensures a safe standby state whenever triggered by Pre-inference (DRS < 0.25) or Post-inference (Confidence Gate $\tau$ rejection). For all predictions that bypass abstention and successfully pass the Confidence Gate, a final confidence label is calculated to determine their degree of autonomy:

→ [Self-Confidence Score (SCS)](en/projects/systems/amplify-core/architecture/self-confidence-score.md)
