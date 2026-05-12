# widget-v1 · TASKS

Task spec only. **Current state / event log → [`../STATE.md`](../STATE.md)** (single source).

## Task graph (static)

| Task | One-line spec | Depends on |
|---|---|---|
| T1 | Baseline widget properties (Method A) | — |
| T2 | Stress response under perturbation (Method A) | T1 |
| T3 | Linear-response model fit + write-up | T2 |

---

## T1 · Baseline widget properties

**Input**: widget sample (1 unit), Method A apparatus

**Steps**:
1. Calibrate apparatus against reference
2. Run baseline acquisition (1 week of integration)
3. Extract widget property `P` with uncertainty

**Output**: baseline value `P ± σ`, calibration report

**Completion criteria**:
- `P` within literature range (40-45 units)
- σ < 1.0 units
- Calibration drift documented if observed

**Known risks**:
- Detector channel drift over 1-week integration → mitigate by mid-run cross-check
- Calibration reference may degrade → use fresh reference per session

**Locked decisions**: Method A (per ROADMAP §locked decisions)

---

## T2 · Stress response under perturbation

**Input**: widget (same physical unit as T1), perturbation source up to ±10% strain

**Steps**:
1. Apply controlled perturbation (5 strain values: −10%, −5%, 0, +5%, +10%)
2. At each strain, run brief acquisition (1 day each)
3. Extract response `R(strain)`

**Output**: `R(strain)` table with 5 data points + uncertainties

**Completion criteria**:
- 5 strain points completed
- Nonlinearity check passes (deviations from linear fit < 20% — see ROADMAP red line)
- Detector channel usage: channels 1-6 only (per T1 calibration caveat, STATE event 2026-02-03)

**Known risks**:
- Perturbation source instability → use closed-loop control
- T1-noted detector drift → restrict to channels 1-6

**Locked decisions**:
- Method A continues for T2 (re-evaluated per STATE event 2026-02-08; remains valid in ±10% strain range)
- Channels 1-6 only (per STATE event 2026-02-03 calibration caveat)

---

## T3 · Linear-response model fit + write-up

**Input**: T1 baseline + T2 response data

**Steps**:
1. Fit linear model `R = R₀ + k·strain` to T2 data
2. Compute χ², residual analysis
3. Compare to nonlinear-extension prediction (not fit; just plausibility check)
4. Write 1-page note

**Output**: 1-page note with `R₀`, `k`, fit quality, and discussion

**Completion criteria**:
- Linear model fit quality: χ²/dof < 1.5
- If nonlinear deviations > 20% appear → ROADMAP red-line triggered, do not proceed with write-up; report to user

**Locked decisions**: linear response model only (nonlinear deferred to future)

---

## Known sub-instances

| Path | Role |
|---|---|
| `../01_measure/widget_v1_runA_2026-02-01/` | T1 baseline + T2 stress (shared apparatus setup) |

(Status → [`../STATE.md`](../STATE.md) §sub-instance roster + event log)
