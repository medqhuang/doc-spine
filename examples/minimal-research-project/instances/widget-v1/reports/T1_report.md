# T1 Report · Widget baseline characterization

> **Status**: T1 done (2026-02-10).
> **Method**: Method A (locked per ROADMAP)
> **Sub-instance**: `01_measure/widget_v1_runA_2026-02-01`
> **Final result**: P = 42.1 ± 0.3 units (within literature range 40-45)
> **Calibration caveat**: detector channel 7 drift observed (2%), use channels 1-6 only for downstream tasks
> **Event trace** → `../STATE.md` event log entries for 2026-01-27, 2026-02-03, 2026-02-08, 2026-02-10
> **Downstream interface** → §6 below

---

## 1. Setup

| Item | Value |
|---|---|
| Apparatus | Method A (model 7.0, locked per ROADMAP 2026-01-01) |
| Calibration reference | Standard widget S-2024 (degraded < 5% in last 6 months) |
| Integration time | 7 days (Jan 20 → Jan 27) |
| Detector channels | 1-8 (full set) |
| Job ID | 4990 |

## 2. Calibration

Calibration scan against S-2024 confirms apparatus is operating within spec:
- Channels 1-6: response within 1% of reference (within tolerance)
- Channel 7: response 2% high compared to reference — **drift flagged**
- Channel 8: response within 1% (within tolerance)

**Action**: Channel 7 flagged for caution. Downstream tasks (T2) restricted to channels 1-6 (see STATE event 2026-02-03 for the decision).

## 3. Acquisition

7-day continuous run. Acquisition log: `01_measure/widget_v1_runA_2026-02-01/logs/acquisition.log`.

| Day | Hourly mean response | std dev |
|---|---|---|
| 1 | 42.05 | 0.21 |
| 2 | 42.18 | 0.19 |
| 3 | 42.07 | 0.20 |
| 4 | 42.11 | 0.22 |
| 5 | 42.13 | 0.18 |
| 6 | 42.09 | 0.21 |
| 7 | 42.14 | 0.20 |

Day-to-day variation is consistent with statistical fluctuation (no systematic drift in baseline value).

## 4. Result

**Baseline widget property** `P = 42.1 ± 0.3 units`

- Mean across 7 days, statistical error
- Within literature range (40-45) ✅
- Calibration caveat from §2 applies (channel 7 flagged; channels 1-6 only for downstream)

## 5. Cross-check

Independent reanalysis using only channels 1-6 (excluding flagged channel 7):

| Subset | P value | Δ vs full |
|---|---|---|
| Channels 1-8 (default) | 42.1 ± 0.3 | — |
| Channels 1-6 (cleaned) | 42.0 ± 0.3 | −0.1 (within error) |

Conclusion: channel 7 drift does not bias the baseline result. Both full-detector and cleaned-detector values agree within uncertainty.

## 6. Downstream interface

| Task | Interface |
|---|---|
| **T2** | Reuse `01_measure/widget_v1_runA_2026-02-01/` apparatus setup; use channels 1-6 only (per §2/§5 caveat); use `P = 42.1` as baseline for response calculation |
| **T3** | Consume T2's `R(strain)` data + this `P` baseline |

## 7. Known caveats (reviewer view)

1. **Channel 7 drift**: 2% high vs reference, flagged in §2; downstream restricted to channels 1-6. Final baseline value unaffected (cross-check §5).
2. **Calibration reference age**: S-2024 has degraded ~5% over 6 months; absolute calibration uncertainty ~1% beyond the quoted statistical error.
3. **Method A range**: confirmed valid for T2 ±10% strain perturbation (re-evaluation per STATE event 2026-02-08).

## 8. Reproducibility

| Item | Path / id |
|---|---|
| Sub-instance | `01_measure/widget_v1_runA_2026-02-01/` |
| Apparatus config | `01_measure/widget_v1_runA_2026-02-01/inputs/setup.toml` |
| Raw data | `01_measure/widget_v1_runA_2026-02-01/data/raw_*.csv` (kept off-repo per .gitignore size rules) |
| Analysis scripts | `01_measure/widget_v1_runA_2026-02-01/scripts/extract_P.py` |
| Logs | `01_measure/widget_v1_runA_2026-02-01/logs/` |
| Job ID | 4990 |
