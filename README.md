# Insurance Analytics: Root Cause Diagnosis, Fraud & Risk Analysis, and Student Insurance Financial Feasibility

## Overview

This project was developed as part of the ** Case Competition**, where the objective was to investigate the decline in **Yupa Insurance's** revenue despite stable policy pricing and insured lives, and to evaluate the financial feasibility of launching a subsidized student health insurance scheme in Delhi.

The project combines **Exploratory Data Analysis (EDA), Root Cause Analysis (RCA), Fraud Detection, Risk Analysis, Insurance Analytics, and Financial Modeling** to generate actionable business recommendations.

---

# Problem Statement

Yupa Insurance experienced a significant decline in revenue over two consecutive quarters, while competing insurers remained unaffected. Since premium pricing and the number of insured members remained constant, management suspected that operational inefficiencies, billing irregularities, or contractual loopholes were responsible.

The consulting objectives were:

1. Diagnose the root cause of the revenue decline.
2. Identify fraudulent or abnormal claim patterns.
3. Assess insurance risk.
4. Recommend mitigation strategies.
5. Evaluate the financial viability of entering the student insurance market in Delhi through a three-year financial projection.

---

# Business Objectives

## Part I – Root Cause Analysis

- Analyze historical claims data.
- Identify the primary drivers behind declining profitability.
- Detect abnormal hospital billing behavior.
- Investigate repeated claim patterns.
- Examine contractual loopholes being exploited.
- Recommend operational and policy changes.

---

## Part II – Student Insurance Feasibility

Evaluate whether a subsidized student insurance scheme would be financially sustainable under the following assumptions:

- Annual Premium: **₹7,000 per student**
- Government Subsidy: **50%**
- University Contribution: **25%**
- Student Contribution: **25%**
- Initial Enrollment: **2,000 students**
- Annual Growth Rate: **20%**
- Claim Frequency: **10%**
- Average Claim Amount: **₹92,989.13**

---

# Dataset

The insurance claims dataset contains:

- Claim ID
- Hospital Name
- Patient ID
- Medical Procedure
- Claim Amount
- Claim Date
- Claim Status

---

# Project Workflow

```
Data Collection
        │
        ▼
Data Cleaning
        │
        ▼
Exploratory Data Analysis
        │
        ▼
Fraud & Risk Analysis
        │
        ▼
Root Cause Analysis
        │
        ▼
Financial Projection
        │
        ▼
Business Recommendations
```

---

# Exploratory Data Analysis (EDA)

EDA was performed to understand the characteristics of the insurance claims and identify unusual patterns affecting profitability.

## Data Cleaning

- Removed formatting inconsistencies
- Converted claim amounts into numeric values
- Checked missing values
- Checked duplicate claims
- Validated data types

---

## Descriptive Statistics

Generated summary statistics including:

- Mean claim amount
- Median claim amount
- Standard deviation
- Maximum claim value
- Minimum claim value
- Claim distribution

---

## Hospital Billing Analysis

Hospital-wise analysis included:

- Number of claims
- Total billed amount
- Average claim amount
- Revenue concentration

The analysis revealed that **CityCare Hospital accounted for the highest cumulative claim amount**, indicating that a significant portion of payouts was concentrated within a single provider.

---

## Procedure Analysis

Studied:

- Most frequently claimed procedures
- Average cost by procedure
- Procedure-wise expenditure

This helps identify expensive treatments contributing disproportionately to claim costs.

---

## Patient Claim Analysis

Examined:

- Claims per patient
- Repeat hospital visits
- Multiple claims within short intervals

The analysis showed repeated claims associated with the same patient at the same hospital, suggesting potential overutilization or abuse of the reimbursement process.

---

# Root Cause Analysis (RCA)

## Problem

Despite stable premiums and policyholders, Yupa Insurance experienced declining revenue.

Since premium income remained constant, the decline had to originate from **higher claims expenditure** rather than reduced sales.

---

## Key Findings

The claims data revealed several anomalies:

### 1. Repeated Claims for the Same Patient

One patient (e.g., **P123**) submitted multiple high-value claims within a very short period, all from the same hospital.

This pattern indicates either:

- duplicate billing,
- claim splitting,
- repeated reimbursement for the same episode of care.

---

### 2. Hospital Billing Concentration

A disproportionate share of total claims originated from **CityCare Hospital**, making it a significant driver of claims expenditure.

Heavy dependence on a single provider increases financial exposure and reduces cost control.

---

### 3. High-Cost Procedures

Certain procedures (such as **Knee Surgery**) accounted for a large proportion of total claims, increasing the insurer's payout obligations.

---

### 4. Possible Contractual Loophole

The observed claim patterns suggest that hospitals may be exploiting weaknesses in reimbursement policies by:

- splitting one hospitalization into multiple claims,
- submitting repeated claims within short intervals,
- maximizing reimbursement without triggering automatic fraud detection.

Although these patterns are indicative of abuse, they should be treated as **potential fraud indicators requiring further investigation**, rather than confirmed fraud.

---

# Fraud Detection & Risk Analysis

Fraud analysis focused on identifying statistical anomalies rather than proving fraudulent intent.

## Indicators Evaluated

- Duplicate claims
- Repeated patient admissions
- High-value outlier claims
- Hospital billing concentration
- Multiple claims within short periods
- Procedure-specific cost anomalies

Methods used:

- Frequency analysis
- Outlier detection
- Hospital-wise aggregation
- Patient-wise aggregation
- Cost distribution analysis

---

# Business Risk Assessment

Major financial risks identified include:

- Excessive dependence on a few hospitals
- Weak claim verification process
- High-cost procedures driving payouts
- Lack of repeat-claim monitoring
- Limited fraud screening before settlement

---

# Recommended Mitigation Strategies

To reduce revenue leakage, Yupa Insurance should implement the following controls:

### Strengthen Contract Terms

- Limit reimbursement for repeated procedures.
- Introduce claim caps for selected treatments.
- Define minimum intervals between similar claims.

---

### Automated Fraud Detection

Implement machine learning or rule-based systems to flag:

- duplicate claims,
- repeated admissions,
- abnormal billing patterns,
- unusual provider behavior.

---

### Provider Audits

Conduct regular audits for hospitals exhibiting:

- unusually high claim volumes,
- excessive billing,
- abnormal reimbursement patterns.

---

### Pre-Authorization

Require pre-authorization for expensive procedures to verify medical necessity before claim approval.

---

### Continuous Risk Monitoring

Develop dashboards that continuously monitor:

- provider performance,
- patient claim history,
- abnormal billing behavior,
- claim trends.

---

# Student Insurance Financial Projection

A financial model was developed for a three-year pilot program.

## Assumptions

| Parameter | Value |
|------------|-------|
| Initial Students | 2,000 |
| Annual Growth | 20% |
| Premium | ₹7,000 |
| Claim Frequency | 10% |
| Average Claim Amount | ₹92,989.13 |

---

# Key Performance Indicators (KPIs)

The following insurance KPIs were calculated to evaluate the financial performance of the proposed student insurance scheme:

| KPI | Description |
|------|-------------|
| Total Premium Revenue | Annual premium income generated from enrolled students |
| Expected Claimants | Estimated number of students expected to file claims |
| Claims Cost | Total projected healthcare payout |
| Administrative Cost | Operational cost of managing the insurance program |
| Net Profit/Loss | Revenue after deducting claims and administrative expenses |
| Return on Investment (ROI) | Profit generated relative to total costs |
| Loss Ratio | Claims Cost ÷ Premium Revenue × 100 |
| Revenue Growth | Annual increase in premium revenue due to student enrollment growth |

---

# Financial Results

The three-year projection indicates:

- Premium revenue increases each year due to enrollment growth.
- Claims expenditure also increases proportionally.
- The projected **Loss Ratio remains 132.84%**.
- ROI remains negative throughout the projection period.
- Net losses increase annually.

These findings indicate that the proposed premium structure is insufficient to sustain the insurance scheme.

---

# Visualizations

The project includes:

- Hospital-wise claim distribution
- Procedure frequency
- Patient claim frequency
- High-value claim analysis
- Fraud indicator charts
- Revenue projection
- Claims cost projection
- Revenue vs Profit
- ROI trend
- Loss Ratio trend

---

# Technologies Used

- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Jupyter Notebook

---

# Skills Demonstrated

- Exploratory Data Analysis (EDA)
- Root Cause Analysis (RCA)
- Healthcare Analytics
- Insurance Analytics
- Fraud Detection
- Risk Assessment
- Business Intelligence
- Financial Forecasting
- KPI Development
- ROI Analysis
- Loss Ratio Analysis
- Data Visualization
- Strategic Business Recommendations

---

# Conclusion

The analysis indicates that Yupa Insurance's revenue decline is primarily driven by **increasing claims costs rather than reduced premium income**. Repeated high-value claims, concentration of claims from specific hospitals, and potential weaknesses in reimbursement policies suggest that contractual loopholes and insufficient claim monitoring may be contributing to revenue leakage.

Additionally, the financial feasibility assessment of the proposed student insurance scheme shows that, under the current assumptions, the program would generate a **Loss Ratio of 132.84%**, meaning claims exceed premium revenue before operational expenses are considered. Consequently, the scheme is not financially sustainable in its current form.

To improve profitability, Yupa Insurance should strengthen provider contracts, enhance fraud detection mechanisms, implement stricter claim verification processes, adopt risk-based pricing strategies, and introduce preventive healthcare initiatives to reduce long-term claim costs.
