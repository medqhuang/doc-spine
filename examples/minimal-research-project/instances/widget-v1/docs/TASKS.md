# widget-v1 · TASKS

Task spec only. Current state / event log -> [`../STATE.md`](../STATE.md).

## Task graph (static)

| Task | One-line spec | Depends on |
|---|---|---|
| T1 | Baseline widget properties (Method A) | — |
| T2 | Stress response under perturbation (Method A) | T1 |
| T3 | Linear-response model fit + write-up | T2 |

## T1 · Baseline widget properties

**Input**: widget sample, Method A apparatus

**Steps**:
1. Calibrate apparatus against reference.
2. Run baseline acquisition.
3. Extract widget property `P` with uncertainty.

**Output**: baseline value `P ± σ`, calibration report

**Completion criteria**:
- `P` within literature range (40-45 units)
- σ < 1.0 units
- Calibration drift documented if observed

**Known risks**:
- Detector channel drift over integration: mitigate with mid-run cross-check.
- Calibration reference may degrade: use fresh reference per session.

**Locked decisions**: Method A, per STATE event 2026-01-05 (decision).

## T2 · Stress response under perturbation

**Input**: same widget as T1, perturbation source up to ±10% strain

**Steps**:
1. Apply controlled perturbation at five strain values: -10%, -5%, 0, +5%, +10%.
2. Run brief acquisition at each strain.
3. Extract response `R(strain)`.

**Output**: `R(strain)` table with five data points and uncertainties

**Completion criteria**:
- Five strain points completed.
- Nonlinearity check passes: deviations from linear fit <20%, per ROADMAP red line.
- Detector channel usage: channels 1-6 only, per STATE event 2026-02-03.

**Known risks**:
- Perturbation source instability: use closed-loop control.
- T1-noted detector drift: restrict to channels 1-6.

**Locked decisions**:
- Method A, per STATE event 2026-01-05 (decision).
- Channels 1-6 only, per STATE event 2026-02-03.

## T3 · Linear-response model fit + write-up

**Input**: T1 baseline + T2 response data

**Steps**:
1. Fit linear model `R = R0 + k * strain`.
2. Compute fit quality and residuals.
3. Compare residuals to the ROADMAP red line.
4. Write one-page note.

**Output**: one-page note with `R0`, `k`, fit quality, and discussion

**Completion criteria**:
- Fit quality is acceptable.
- If nonlinear deviations exceed 20%, stop and update STATE instead of writing the final note.

## Known sub-instances

| Path | Role |
|---|---|
| `../01_measure/widget_v1_runA_2026-02-01/` | T1 baseline + T2 stress setup |

Status -> [`../STATE.md`](../STATE.md) §sub-instance roster + event log.
