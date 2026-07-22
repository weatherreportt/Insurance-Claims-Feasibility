# Yupa Insurance — Revenue Diagnostics & Student Insurance Feasibility

**Technical Documentation**

---

## 1. Overview

This repository contains the analytical work product for a case-competition exercise commissioned by **Yupa Insurance**, a healthcare insurer in India. The engagement has two independent workstreams that share a common claims dataset:

1. **Root Cause Analysis** — diagnose why Yupa's revenue dropped over the last two quarters while peer insurers, total insured lives, and pricing remained stable.
2. **Student Insurance Feasibility** — model the three-year financial viability of a proposed subsidized student health insurance pilot in Delhi (₹7,000 annual premium, split 50% government / 25% university / 25% student).

The codebase is exploratory/analytical (Jupyter notebooks), not a production application. This document describes the data, the analytical methodology, the derived findings, the financial model mechanics, and how to reproduce every result.

---

## 2. Repository Structure

| File | Type | Purpose |
|---|---|---|
| `yupa_insurance_case_somp.pdf` | Case brief | Original problem statement and task list issued for the exercise |
| `claims_yupa.csv` | Data | Raw claims ledger used for both workstreams |
| `yupa_insurance.ipynb` | Notebook | Root-cause / exploratory data analysis (EDA) on the claims ledger |
| `student_insurance_feasibility.ipynb` | Notebook | Three-year financial projection and ROI model for the student insurance pilot |

There is no application code, API, or deployment artifact in this project — it is a self-contained data analysis submitted as a slide-ready report (max 4 slides per the case brief).

---

## 3. Data Dictionary — `claims_yupa.csv`

| Column | Type (raw) | Type (post-cleaning) | Description |
|---|---|---|---|
| `Claim ID` | string | string | Unique identifier per claim row (e.g. `C001`) |
| `Hospital Name` | string | string (categorical) | Provider hospital/clinic. Three distinct values in the dataset: `CityCare Hosp.`, `Metro Hosp.`, `Sunrise Clinic` |
| `Patient ID` | string | string (categorical) | Insured member identifier (e.g. `P123`) |
| `Procedure` | string | string (categorical) | Medical procedure billed. Seven distinct values: Knee Surgery, Appendectomy, Cataract Surgery, Heart Stent, Tonsillectomy, Hip Replacement, Hernia Repair |
| `Claim Amount` | string, Indian-comma formatted (e.g. `"1,20,000"`) | `int64` (₹) | Billed claim value. **Requires cleaning** — commas must be stripped before numeric casting |
| `Date` | string (`YYYY-MM-DD`) | `datetime64` | Date of claim submission |
| `Status` | string | string (categorical) | Claim settlement status. Every row in the current extract is `Settled` |

**Dataset size:** 100 claim rows (99 data rows + header), spanning Jan–Apr 2025.

### 3.1 Cleaning steps applied (notebook `yupa_insurance.ipynb`, cells 2–4)

```python
df["Claim Amount"] = pd.to_numeric(
    df["Claim Amount"].astype(str).str.replace(",", "", regex=False),
    errors="coerce",
)
df["Date"] = pd.to_datetime(df["Date"])
df["Year"] = df["Date"].dt.year
df["Month"] = df["Date"].dt.month
df["Month_Name"] = df["Date"].dt.strftime("%b")
df["Quarter"] = df["Date"].dt.quarter
```

The Indian numbering convention (`1,20,000` = 120,000) is the main data-quality trap in this file — a naive `float()` cast without comma removal will silently fail or truncate.

---

## 4. Workstream 1 — Root Cause Analysis (`yupa_insurance.ipynb`)

### 4.1 Analytical approach

The notebook runs a standard descriptive EDA pipeline against the cleaned claims ledger:

1. **Summary statistics** — count of claims, unique hospitals, unique patients, unique procedures, total/mean/median/min/max claim amount.
2. **Univariate distributions** — histogram of claim amounts (mean vs. median overlay) to check for skew.
3. **Hospital-level breakdown** — claim counts and claim value by hospital (bar charts + donut chart of value share).
4. **Procedure-level breakdown** — claim counts, average claim value, and value share by procedure.
5. **Cross-tabulation** — hospital × procedure heatmaps for claim count, total value, and average value, to surface unusual concentrations.
6. **Time-series view** — monthly claim count and monthly claim value trend across the observation window.
7. **Patient-level frequency analysis** — claims per patient, with the top 10 most claim-frequent patients isolated.
8. **Targeted drill-down** — once a repeat-billing outlier patient is identified from the frequency analysis, all of that patient's individual claim rows are printed and inspected line-by-line.
9. **Sensitivity check** — dataset-wide summary statistics are recomputed with the outlier patient excluded, to quantify how much that single patient distorts the aggregate numbers.

### 4.2 Key finding: duplicate/repeat billing loophole

The claim-frequency analysis (cell 15, "Top 10 Patients by Number of Claims") surfaces a stark outlier:

| Patient ID | Number of Claims | Typical patient |
|---|---|---|
| **P123** | **8** | 1 |

Drilling into `Patient ID == "P123"` (cell 19) shows all 8 claims are for the **identical procedure** (Knee Surgery), at the **identical claim amount** (₹1,20,000 each), submitted by the **same hospital** (CityCare Hosp.), on eight different dates spread across January–April 2025 rather than as a single episode of care.

**Quantified impact:**
- P123 alone accounts for **₹9,60,000** in claims — roughly **10% of total claims value** (₹95,15,000) across the entire 100-row sample — from a single procedure billed repeatedly.
- CityCare Hosp. is the highest-billing provider in the dataset (₹46,40,000 total, vs. ₹24,60,000 for Metro Hosp. and ₹24,15,000 for Sunrise Clinic), and P123's repeat claims are concentrated entirely at this one hospital.
- Excluding P123, the average claim amount, median, and standard deviation all shift materially (see cell 24 output), confirming this single patient/hospital pair is distorting the overall claims profile rather than reflecting genuine utilization growth.

### 4.3 Root cause interpretation

A single genuine knee surgery does not recur at the same cost eight times within roughly three months. The pattern (same patient, same hospital, same procedure code, same billed amount, staggered submission dates, all marked `Settled`) is consistent with:

- **Duplicate/re-submission of a single procedure** as though it were multiple independent episodes, and/or
- **Split billing** — a single treatment episode broken into multiple claim submissions to multiply reimbursement, and/or
- **A contract/adjudication loophole** where the payer's auto-settlement rules do not flag repeat claims for the same patient/procedure/provider combination within a short window.

This is consistent with the case brief's hint to focus on "hospital billing patterns and contract terms," and explains why the revenue drop is isolated to Yupa specifically (a claims-adjudication/contract control weakness with one provider) rather than a market-wide phenomenon (which would show up across peer insurers too).

### 4.4 Recommended mitigations

1. **Duplicate-claim detection rule**: flag/hold any claim where `(Patient ID, Procedure, Hospital Name)` repeats within a defined clinical cooldown window (e.g. 90 days) for manual adjudication before auto-settlement.
2. **Provider-level audit**: initiate a targeted billing audit of CityCare Hosp., given its outsized share of both claim count and claim value.
3. **Contract renegotiation**: tighten contractual claim-submission terms with hospitals to require unique procedure/episode identifiers and pre-authorization for repeat high-cost procedures.
4. **Ongoing monitoring**: institutionalize the "claims per patient" and "hospital vs. procedure" heatmap views built in this notebook as a recurring monthly control report, rather than a one-off diagnostic.

---

## 5. Workstream 2 — Student Insurance Feasibility (`student_insurance_feasibility.ipynb`)

### 5.1 Purpose

Model whether a subsidized student health insurance product piloted in Delhi is financially viable over a 3-year horizon, and issue a go/no-go recommendation for scaling.

### 5.2 Assumptions

| Parameter | Assumption | Source/Rationale |
|---|---|---|
| Annual Premium | ₹7,000 per student | Given in case brief |
| Government Share | 50% | Given |
| University Share | 25% | Given |
| Student Share | 25% | Given |
| Initial Enrollment | 2,000 students | Pilot assumption |
| Enrollment Growth | 20% per year | Assumed post-pilot expansion |
| Claim Rate | 10% of students file ≥1 claim | Historical estimate (assumption, not derivable from the 100-row claims sample) |
| Claim Frequency | 1.25 claims per claimant | Accounts for repeat claims per claimant |
| Average Claim Amount | Derived from claims dataset EDA | From Workstream 1 |
| Admin Cost | 15% of annual premium revenue | Assumed — actual admin cost not provided in the case brief |

> **Note on the premium split:** the 50/25/25 government/university/student split determines *who pays* the ₹7,000 premium, not the total premium revenue collected by Yupa. All revenue/claims/profit calculations below use the **full ₹7,000 per student** as the premium actually received by the insurer; the split only matters for downstream stakeholder-level cost allocation (e.g., how much the government or students collectively contribute), which is not separately computed in the current notebook.

### 5.3 Model formulas

| Metric | Formula |
|---|---|
| Revenue | `Students × ₹7,000` |
| Expected Claimants | `Students × Claim Rate` |
| Projected Total Claims (volume) | `Students × Claim Rate × Claim Frequency` |
| Claims Cost | `Expected Claimants × Average Claim Amount` |
| Administrative Cost | `Revenue × 15%` |
| Profit | `Revenue − Claims Cost − Administrative Cost` |
| ROI | `(Profit ÷ (Claims Cost + Administrative Cost)) × 100` |
| Loss Ratio | `(Claims Cost ÷ Premium Revenue) × 100` |

### 5.4 Year-by-year projection

**Enrollment & Revenue**

| Year | Enrolled Students | Revenue (₹) | Revenue (₹ Cr) |
|---|---:|---:|---:|
| Year 1 | 2,000 | 14,000,000 | 1.40 |
| Year 2 | 2,400 | 16,800,000 | 1.68 |
| Year 3 | 2,880 | 20,160,000 | 2.02 |

**Administrative Cost (15% of revenue)**

| Year | Revenue (₹) | Admin Cost (₹) |
|---|---:|---:|
| Year 1 | 14,000,000.00 | 2,100,000.00 |
| Year 2 | 16,800,000.00 | 2,520,000.00 |
| Year 3 | 20,160,000.00 | 3,024,000.00 |

**Claims Cost, Profit, ROI, and Loss Ratio**

| Year | Expected Claimants | Revenue (₹) | Claims Cost (₹) | Admin Cost (₹) | Profit (₹) | ROI (%) | Loss Ratio (%) |
|---|---:|---:|---:|---:|---:|---:|---:|
| Year 1 | 200 | 14,000,000.00 | 18,597,826.00 | 2,100,000.00 | -6,697,826.00 | -32.37% | 132.84% |
| Year 2 | 240 | 16,800,000.00 | 22,317,391.20 | 2,520,000.00 | -8,037,391.20 | -32.37% | 132.84% |
| Year 3 | 288 | 20,160,000.00 | 26,780,869.44 | 3,024,000.00 | -9,644,869.44 | -32.37% | 132.84% |

Claims cost, profit, and admin cost all scale linearly with enrollment (20% YoY), which is why ROI and loss ratio remain **flat at -32.37% and 132.84% respectively across all three years** — the model has no mechanism (yet) by which per-student unit economics improve with scale.

### 5.5 Interpretation

- **Loss Ratio of 132.84%** means every ₹100 collected in premium results in ₹132.84 paid out in claims, before even accounting for the 15% administrative cost — the scheme fails at the underwriting level, not just after overhead.
- **Profit is negative and worsening in absolute terms** every year (₹6.70M → ₹8.04M → ₹9.64M loss), because losses scale with the same 20% enrollment growth that grows revenue — growth alone does not fix a structurally underpriced product.
- **ROI is negative and constant (-32.37%)** across all three years, confirming the shortfall is a rate-design problem, not a scale problem.

### 5.6 Recommendation: **No-Go** (under current assumptions)

The pilot is **not financially sustainable** at a ₹7,000 annual premium against the modeled 10% claim rate, 1.25 claim frequency, and dataset-derived average claim size. Suggested levers to reach viability before scaling beyond a pilot:

1. **Repricing** — raise the annual premium (modeling suggests a materially higher premium, or a lower-cost benefit design, is needed to bring the loss ratio under 100%).
2. **Claims cost control** — negotiate capped/package rates with network hospitals for common procedures, and apply the duplicate-claim controls recommended in Workstream 1 to the student book from day one.
3. **Preventive care programs** — campus health initiatives to reduce claim *rate* and claim *frequency* rather than relying on premium increases alone.
4. **Re-run pilot with real (not assumed) claim rate/frequency data** — the current claim rate (10%) and claim frequency (1.25) are stated assumptions, not empirically derived from the 100-row claims sample; a live pilot cohort should be used to calibrate these inputs before any scale decision.

---

## 6. Methodology & Tooling

### 6.1 Stack

- **Python 3** with `pandas`, `numpy` for data manipulation
- **matplotlib**, **seaborn** for visualization (styles used: `ggplot`, `seaborn-v0_8-whitegrid`)
- Standard library `datetime` for date handling
- Both notebooks are self-contained; `student_insurance_feasibility.ipynb` references a `financial_projection` DataFrame defensively (falls back to hardcoded `numpy` arrays via `try/except NameError` if that DataFrame isn't in scope), so it can be run independently of the EDA notebook.

### 6.2 Reproducing the analysis

```bash
pip install pandas numpy matplotlib seaborn jupyter

# Root cause / EDA — reads claims_yupa.csv from the working directory
jupyter nbconvert --to notebook --execute yupa_insurance.ipynb

# Financial feasibility model
jupyter nbconvert --to notebook --execute student_insurance_feasibility.ipynb
```

`claims_yupa.csv` must be in the same working directory as `yupa_insurance.ipynb` (loaded via a relative path: `pd.read_csv('claims_yupa.csv')`).

---

## 7. Known Limitations

1. **Sample size** — the claims ledger contains only 100 rows across 3 hospitals and 7 procedures; findings (especially the P123 anomaly) are illustrative of the *type* of control failure to look for, and should be validated against Yupa's full production claims data before any provider action is taken.
2. **Single-status dataset** — every claim in the extract is `Status = Settled`; there is no visibility into rejected/pending claims, which would normally be part of a full leakage analysis.
3. **Assumed (not fitted) feasibility parameters** — claim rate (10%) and claim frequency (1.25) in the student insurance model are stated assumptions rather than values fitted from a comparable population; the average claim amount is pulled from the same small claims sample used in Workstream 1, which itself contains the P123 outlier and may overstate true average severity.
4. **No stakeholder-level cash-flow split** — the 50/25/25 government/university/student premium split is not separately modeled; only aggregate premium revenue to the insurer is projected.
5. **No confidence intervals / sensitivity table** — the feasibility model presents point estimates only; no scenario or sensitivity analysis (e.g., premium at ₹9,000, claim rate at 7%) is included in the current notebooks.

---

## 8. Deliverable Format

Per the case brief's submission guidelines, findings from this analysis are intended to be condensed into a **maximum 4-slide report** covering: (1) root cause diagnosis and mitigation, (2) student insurance feasibility and go/no-go, and (3) consolidated recommendations — with all assumptions stated explicitly, as documented in Sections 4 and 5 above.
