# Yupa Insurance — Revenue Diagnostics & Student Insurance Feasibility

A case-competition data analysis project for **Yupa Insurance**, a healthcare insurer in India, covering two independent workstreams built on a shared claims dataset:

1. **Root Cause Analysis** — why did Yupa's revenue drop over the last two quarters when peer insurers, total insured lives, and pricing all stayed flat?
2. **Student Insurance Feasibility** — is a proposed subsidized student health insurance pilot in Delhi (₹7,000/year premium, split 50% government / 25% university / 25% student) financially viable over 3 years?

> 📄 For the full methodology, formulas, data dictionary, and caveats, see [`TECHNICAL_DOCUMENTATION.md`](./TECHNICAL_DOCUMENTATION.md). This README is a guided tour of the project and its findings.

---

## Table of Contents

- [Repository Structure](#repository-structure)
- [Problem Statement](#problem-statement)
- [Dataset](#dataset)
- [Workstream 1 — Root Cause Analysis](#workstream-1--root-cause-analysis)
- [Risk Analysis Report](#risk-analysis-report)
- [Workstream 2 — Student Insurance Feasibility](#workstream-2--student-insurance-feasibility)
- [Getting Started](#getting-started)
- [Notebook Walkthrough](#notebook-walkthrough)
- [Assumptions](#assumptions)
- [Limitations](#limitations)
- [Deliverable Format](#deliverable-format)

---

## Repository Structure

```
.
├── claims_yupa.csv                       # Raw claims dataset (100 rows)
├── yupa insurance.ipynb                  # Root-cause EDA notebook
├── student insurance feasibility.ipynb   # 3-year financial feasibility model
├── risk analysis.md                      # Narrative root-cause & risk mitigation report
├── TECHNICAL_DOCUMENTATION.md            # Full technical write-up
└── README.md                             # This file
```

| File | Type | Purpose |
|---|---|---|
| `claims_yupa.csv` | Data | Raw claims ledger, shared by both notebooks |
| `yupa insurance.ipynb` | Notebook | Exploratory data analysis (EDA) driving the root-cause diagnosis |
| `student insurance feasibility.ipynb` | Notebook | Revenue, claims-cost, profit, ROI, and loss-ratio projections for the pilot |
| `risk analysis.md` | Report | Narrative write-up of the root-cause drivers, the likely contractual/process loophole, and recommended mitigations |
| `TECHNICAL_DOCUMENTATION.md` | Docs | Data dictionary, formulas, detailed findings, reproduction steps, limitations |

There is no application code, API, or deployment target here — this is an exploratory analysis project intended to be distilled into a slide report (see [Deliverable Format](#deliverable-format)).

---

## Problem Statement

Yupa Insurance has seen a significant revenue drop over the past two quarters, **unique to Yupa** — peer insurers are unaffected, and the number of insured lives and Yupa's pricing strategy have both remained stable. Management wants the root cause diagnosed and mitigations recommended.

Separately, Yupa is considering a new **student insurance product** in Delhi, co-developed with schools/universities, ahead of upcoming regulatory norms:

- Annual premium: **₹7,000/student**
- Paid by: **50% government, 25% university, 25% student**
- Scope: pilot in Delhi, with a 3-year financial outlook required before any decision to scale

**Case tasks:**
1. Identify the key driver(s) of the revenue drop from the claims data; identify the process/contractual loophole being exploited and how to mitigate it.
2. Project 3-year profit/loss for the student insurance pilot (premium splits, claims, admin costs) and issue a go/no-go recommendation.
3. Provide actionable recommendations for both issues.

---

## Dataset

`claims_yupa.csv` — **100 claim records**, Jan–Apr 2025.

| Column | Description |
|---|---|
| `Claim ID` | Unique claim identifier (e.g. `C001`) |
| `Hospital Name` | Provider — one of `CityCare Hosp.`, `Metro Hosp.`, `Sunrise Clinic` |
| `Patient ID` | Insured member identifier (e.g. `P123`) |
| `Procedure` | One of 7 procedures: Knee Surgery, Appendectomy, Cataract Surgery, Heart Stent, Tonsillectomy, Hip Replacement, Hernia Repair |
| `Claim Amount` | Billed value, in Indian-comma format (e.g. `"1,20,000"` = ₹120,000) — **must be cleaned before numeric use** |
| `Date` | Claim submission date |
| `Status` | All records are `Settled` in this extract |

The Indian numbering convention in `Claim Amount` is the main data-quality trap — stripping commas before casting to numeric is required, or values will be misread or dropped. Full data dictionary and cleaning code: see [`TECHNICAL_DOCUMENTATION.md § 3`](./TECHNICAL_DOCUMENTATION.md#3-data-dictionary--claims_yupacsv).

---

## Workstream 1 — Root Cause Analysis

**Notebook:** `yupa insurance.ipynb` · **Report:** `risk analysis.md`

### Approach

1. Clean and type-cast the claims ledger (dates, comma-formatted currency).
2. Compute summary statistics (claim count, unique hospitals/patients/procedures, total/mean/median/min/max claim value).
3. Visualize distributions: claim-amount histogram, claims by hospital, claim value by hospital, claim-amount boxplots by hospital.
4. Visualize procedure-level patterns: claim frequency, share-of-value donut chart, average claim amount per procedure.
5. Cross-tabulate hospital × procedure (claim count, total value, average value) as heatmaps to surface concentration.
6. Analyze monthly trends in claim count and claim value.
7. Rank patients by claim frequency (top 10) to surface repeat-billing outliers.
8. Drill into the flagged outlier patient's full claim history, row by row.
9. Recompute summary statistics with the outlier excluded, to quantify how much it distorts the aggregate picture.

### Finding

Patient **P123** filed **8 claims** — all for the same procedure (Knee Surgery), the same amount (₹1,20,000 each), at the same hospital (**CityCare Hosp.**), submitted on different dates across a 3-month span rather than as one episode of care.

- **~10% of total claims value** (₹9,60,000 of ₹95,15,000) traces to this single patient/hospital/procedure combination.
- CityCare Hosp. is the highest-billing provider overall (₹46.4L vs. ₹24.6L and ₹24.15L for the other two), and P123's repeat claims sit entirely within it.
- Excluding P123 materially shifts the dataset's average, median, and standard deviation — confirming this one case is distorting the aggregate claims profile.

**Interpretation:** a genuine knee surgery does not recur at identical cost eight times in three months. The pattern (same patient/procedure/provider/amount, staggered dates, all auto-settled) points to **duplicate or split billing exploiting a gap in claims adjudication or contract terms** with one provider — explaining why the revenue impact is isolated to Yupa rather than market-wide.

Full detail: [`TECHNICAL_DOCUMENTATION.md § 4`](./TECHNICAL_DOCUMENTATION.md#4-workstream-1--root-cause-analysis-yupa_insuranceipynb).

---

## Risk Analysis Report

**File:** [`risk analysis.md`](./risk%20analysis.md)

This is the narrative companion to the EDA notebook — it translates the data patterns above into a structured risk write-up covering drivers, the likely loophole, and mitigations.

### Key drivers of the revenue drop
- **High concentration of claims at CityCare Hospital** — it contributes a disproportionately large share of claims, making it the biggest cost driver.
- **Repeated high-cost procedure claims** — Knee Surgery appears frequently at a high claim amount (₹1,20,000), inflating total payouts.
- **Repeated claims by the same patient** — Patient P123 appears multiple times, suggesting unusually frequent claims and possible duplicate/repeated billing.

**Interpretation:** these patterns indicate Yupa is paying out more in claims than expected, which reduces retained revenue and profitability — consistent with the notebook findings above.

### The likely contractual/process loophole
The claims process likely has weak controls in one or more of these areas:
- **Weak duplicate-claim detection** — the same patient repeats with the same hospital, procedure, and claim amount.
- **Insufficient verification for high-value procedures** — expensive procedures like Knee Surgery may be reimbursed without strong pre-authorization checks.
- **Limited provider-level monitoring** — one hospital driving an outsized share of claims suggests billing patterns aren't closely monitored.
- **Overly automatic claim validation** — claims settled without careful document checks let repeated or inflated billing slip through.

**Most likely loophole:** repeat claims for the same patient and procedure are not being flagged early enough, allowing repeated reimbursements.

### Recommended mitigations

**Immediate actions**
- Introduce duplicate-claim detection for the same patient, hospital, procedure, and amount.
- Require pre-authorization for high-cost procedures such as Knee Surgery.
- Flag patients with unusually high claim frequency for manual review.
- Audit hospitals with unusually high claim volumes, especially CityCare Hospital.

**Longer-term controls**
- Build a claims analytics dashboard to monitor hospital- and patient-level patterns.
- Apply fraud-detection rules for repeated or identical claims.
- Tighten contract terms for hospitals with abnormal billing behavior.
- Require stronger supporting documentation for expensive and repeated procedures.

### Conclusion
Yupa Insurance's revenue drop is most likely caused by excessive claim payouts driven by repeated high-value claims, especially from CityCare Hospital and Patient P123. The pattern suggests a possible loophole in claim verification and duplicate-billing control. Strengthening claim validation, monitoring hospital behavior, and flagging repeated claims will help reduce leakage and improve profitability.

---

## Workstream 2 — Student Insurance Feasibility

**Notebook:** `student insurance feasibility.ipynb`

### Model formulas

| Metric | Formula |
|---|---|
| Revenue | `Students × ₹7,000` |
| Expected Claimants | `Students × Claim Rate` |
| Claims Cost | `Expected Claimants × Average Claim Amount` |
| Administrative Cost | `Revenue × 15%` |
| Profit | `Revenue − Claims Cost − Administrative Cost` |
| ROI | `(Profit ÷ (Claims Cost + Administrative Cost)) × 100` |
| Loss Ratio | `(Claims Cost ÷ Premium Revenue) × 100` |

### 3-Year Projection

| Year | Enrolled Students | Revenue | Claims Cost | Admin Cost | Profit | ROI | Loss Ratio |
|---|---:|---:|---:|---:|---:|---:|---:|
| Year 1 | 2,000 | ₹1.40 Cr | ₹1.86 Cr | ₹21.0 L | -₹67.0 L | -32.37% | 132.84% |
| Year 2 | 2,400 | ₹1.68 Cr | ₹2.23 Cr | ₹25.2 L | -₹80.4 L | -32.37% | 132.84% |
| Year 3 | 2,880 | ₹2.02 Cr | ₹2.68 Cr | ₹30.2 L | -₹96.4 L | -32.37% | 132.84% |

*(Cr = crore = ₹10 million; L = lakh = ₹100,000)*

### Interpretation

- **Loss Ratio of 132.84%** — every ₹100 of premium collected pays out ₹132.84 in claims, before administrative costs are even applied. The scheme is underwater at the underwriting level.
- **Profit worsens in absolute terms every year** (-₹67.0L → -₹80.4L → -₹96.4L) because claims cost scales with the same 20% annual enrollment growth that grows revenue — growth doesn't fix a structurally underpriced product.
- **ROI stays flat and negative (-32.37%)** across all 3 years, confirming this is a **rate-design problem**, not a scale problem — enrolling more students doesn't change the per-student unit economics.

### Recommendation: **No-Go** (under current assumptions)

The pilot is not financially sustainable at a ₹7,000 premium against the modeled claim rate/frequency and dataset-derived average claim size. To reach viability before scaling:

1. **Reprice** the premium (or redesign benefits) so the loss ratio comes under 100%.
2. **Negotiate capped/package rates** with network hospitals for common procedures.
3. **Apply the duplicate-claim controls from the risk analysis report** to the student book from day one.
4. **Introduce preventive-care programs** to reduce claim rate/frequency rather than relying solely on premium increases.
5. **Re-run the model with real pilot data** — claim rate (10%) and claim frequency (1.25) are stated assumptions here, not empirically fitted; a live pilot cohort should calibrate these before any scale-up decision.

Full detail: [`TECHNICAL_DOCUMENTATION.md § 5`](./TECHNICAL_DOCUMENTATION.md#5-workstream-2--student-insurance-feasibility-student_insurance_feasibilityipynb).

---

## Getting Started

### Requirements

- Python 3.8+
- `pandas`, `numpy`, `matplotlib`, `seaborn`, `jupyter`

```bash
pip install pandas numpy matplotlib seaborn jupyter
```

### Run the analysis

Clone/download the repo and run from the project root (both notebooks expect `claims_yupa.csv` to be in the working directory):

```bash
# Root cause / EDA
jupyter nbconvert --to notebook --execute "yupa insurance.ipynb"

# Student insurance feasibility model
jupyter nbconvert --to notebook --execute "student insurance feasibility.ipynb"
```

Or open either `.ipynb` file directly in Jupyter Lab / Jupyter Notebook / VS Code and run all cells top to bottom — no additional configuration, API keys, or environment variables are required.

`student insurance feasibility.ipynb` is defensively written: it tries to reference a `financial_projection` DataFrame and falls back to hardcoded arrays if that variable isn't in scope, so it can be executed standalone without first running the EDA notebook.

---

## Notebook Walkthrough

### `yupa insurance.ipynb` (25 cells)
1. Imports and plotting style setup
2. Load `claims_yupa.csv`
3. Initial shape/info/describe check
4. Date parsing → Year/Month/Quarter columns
5. Dataset summary statistics (with currency cleaning)
6. Claim amount distribution histogram
7. Claims by hospital (bar chart)
8. Total claim value by hospital (bar chart)
9. Claim amount distribution by hospital (boxplot)
10. Procedure frequency (bar chart)
11. Hospital share of total claim value (donut chart)
12. Procedure share of total claim value (donut chart)
13. Monthly claim count trend
14. Monthly claim value trend
15. Top 10 patients by claim count ← **outlier surfaces here**
16. Hospital × procedure heatmap (count)
17. Hospital × procedure heatmap (total value)
18. Hospital × procedure heatmap (average value)
19. Full claim history for the flagged patient (P123)
20. Monthly claims bar chart with peak-month highlight
21. Top 10 most expensive individual claims
22. Average claim amount by procedure
23. Average claims per patient (dataset-wide rate)
24. Summary statistics recomputed excluding the outlier patient

### `student insurance feasibility.ipynb` (12 cells)
1. Title
2. Assumptions table
3. Revenue projection (formula + 3-year table)
4. Claims estimation methodology (formulas + worked example)
5. Admin cost, profit, ROI tables (formulas + results)
6. Profit and ROI trend charts
7. Revenue/Claims/Profit grouped bar chart
8. Revenue/Claims/Profit trend line chart with loss-gap shading
9. Loss ratio table and interpretation guide
10. Loss ratio trend chart with breakeven threshold line
11. Conclusion

---

## Assumptions

| Parameter | Assumption | Rationale |
|---|---:|---|
| Annual Premium | ₹7,000 | Given in case brief |
| Government Share | 50% | Given |
| University Share | 25% | Given |
| Student Share | 25% | Given |
| Initial Enrollment | 2,000 students | Pilot assumption |
| Enrollment Growth | 20%/year | Post-pilot expansion assumption |
| Claim Rate | 10% | Historical estimate (assumed, not fitted) |
| Claim Frequency | 1.25 claims/claimant | Accounts for repeat claims |
| Average Claim Amount | From `claims_yupa.csv` EDA | Calculated in Workstream 1 |
| Admin Cost | 15% of premium revenue | Assumed — not provided in case brief |

> **Note:** the 50/25/25 split determines who *pays* the ₹7,000 premium, not the total revenue Yupa receives. All revenue/claims/profit figures above use the full ₹7,000/student as premium revenue; the payer-level split is not separately modeled in the current notebook.

---

## Limitations

- **Small sample** — `claims_yupa.csv` has 100 rows across 3 hospitals and 7 procedures; treat findings (especially the P123 case) as directional and validate against full production data before taking provider action.
- **Single claim status** — every record is `Settled`; there's no visibility into rejected/pending claims, which a complete leakage analysis would normally include.
- **Unfitted feasibility parameters** — claim rate (10%) and claim frequency (1.25) are assumptions, not values fitted to a comparable population; the average claim amount is drawn from the same small, outlier-affected claims sample.
- **No stakeholder-level cash flow** — the government/university/student premium split isn't modeled separately; only aggregate insurer-side revenue is projected.
- **No sensitivity analysis** — the feasibility model presents point estimates only (e.g., no "premium at ₹9,000" or "claim rate at 7%" scenario table) in the current notebooks.

Full detail: [`TECHNICAL_DOCUMENTATION.md § 7`](./TECHNICAL_DOCUMENTATION.md#7-known-limitations).

---

## Deliverable Format

Per the case brief's submission guidelines, this analysis is intended to be condensed into a **maximum 4-slide report** covering:
1. Root cause diagnosis and mitigation
2. Student insurance feasibility and go/no-go recommendation
3. Consolidated recommendations, with all assumptions stated explicitly

---

## License / Attribution

This is a case-competition exercise submission. Data in `claims_yupa.csv` is a synthetic/illustrative sample provided for the exercise, not real patient or hospital data.
