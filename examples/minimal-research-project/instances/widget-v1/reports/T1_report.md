---
status: done
task: T1
completed: 2026-02-10
method: Method A
subinstance: 01_measure/widget_v1_runA_2026-02-01
final_result: "P = 42.1 ± 0.3 units"
---

# T1 Report · Widget baseline characterization

## 1. Setup

| Item | Value |
|---|---|
| Apparatus | Method A |
| Calibration reference | Standard widget S-2024 |
| Integration time | 7 days |
| Detector channels | 1-8 |
| Job ID | 4990 |

## 2. Calibration

Calibration scan against S-2024 confirms apparatus is operating within spec:

- Channels 1-6: response within 1% of reference.
- Channel 7: response 2% high compared to reference; flagged for downstream caution.
- Channel 8: response within 1% of reference.

Downstream tasks use channels 1-6 only. The live task status and decision trace are in [`../STATE.md`](../STATE.md).

## 3. Acquisition

| Day | Hourly mean response | Std dev |
|---|---|---|
| 1 | 42.05 | 0.21 |
| 2 | 42.18 | 0.19 |
| 3 | 42.07 | 0.20 |
| 4 | 42.11 | 0.22 |
| 5 | 42.13 | 0.18 |
| 6 | 42.09 | 0.21 |
| 7 | 42.14 | 0.20 |

Day-to-day variation is consistent with statistical fluctuation.

## 4. Result

Baseline widget property: `P = 42.1 ± 0.3 units`

- Within literature range (40-45 units).
- Calibration caveat from §2 applies downstream.

## 5. Cross-check

| Subset | P value | Delta vs full |
|---|---|---|
| Channels 1-8 | 42.1 ± 0.3 | — |
| Channels 1-6 | 42.0 ± 0.3 | -0.1 |

Conclusion: channel 7 drift does not bias the baseline result.

## 6. Downstream interface

| Task | Interface |
|---|---|
| T2 | Reuse the apparatus setup; use channels 1-6 only; use `P = 42.1` as baseline. |
| T3 | Consume T2 response data plus this baseline. |

## 7. Reproducibility

| Item | Path / id |
|---|---|
| Sub-instance | `01_measure/widget_v1_runA_2026-02-01/` |
| Apparatus config | `01_measure/widget_v1_runA_2026-02-01/inputs/setup.toml` |
| Raw data | kept off-repo per size rules |
| Analysis scripts | `01_measure/widget_v1_runA_2026-02-01/scripts/extract_P.py` |
| Job ID | 4990 |
