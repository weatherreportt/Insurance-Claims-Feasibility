# Insurance-Claims-Feasibility


## Overview
This project presents a three-year financial projection and sustainability analysis for a proposed **Student Health Insurance Scheme**. The objective is to evaluate whether the insurance model can remain financially viable under projected enrollment growth, expected healthcare utilization, and claim costs.

The analysis estimates annual premium revenue, claims expenditure, administrative expenses, profit/loss, return on investment (ROI), and loss ratio. Visualizations are included to illustrate financial trends over the projection period.

## Objectives
The primary objectives of this project are:
* **Forecast** the financial performance of a student health insurance program over three years.
* **Estimate** annual premium revenue based on projected student enrollment.
* **Calculate** expected healthcare claims using claim frequency and average claim cost.
* **Evaluate** the financial sustainability of the insurance model.
* **Visualize** revenue, claims, profit, ROI, and loss ratio using informative graphs.
* **Identify** key financial risks and provide recommendations for improving sustainability.


## Project Assumptions

The financial model is based on the following actuarial and operational assumptions:

| Parameter | Value | Details |
| :--- | ---: | :--- |
| **Initial Student Enrollment** | 2,000 students | Pilot baseline |
| **Annual Enrollment Growth** | 20% | Expected expansion rate |
| **Annual Premium per Student** | ₹7,000 | Assigned premium amount |
| **Expected Claim Rate** | 10% | Proportion of students filing claims |
| **Average Claim Amount** | ₹92,989.13 | Calculated from EDA dataset |
| **Administrative Expenses** | 15% | Percentage of total premium revenue |
| **Projection Period** | 3 Years | 2027 – 2029 |


## Financial Model & Mathematical Formulations

For each projected year $n$, the following calculations are performed:

### 1. Student Enrollment
Student enrollment increases annually by 20%:
$$\text{Students}_n = \text{Students}_{n-1} \times (1 + \text{Growth Rate})$$

### 2. Premium Revenue
$$\text{Revenue} = \text{Number of Students} \times \text{Premium per Student}$$

### 3. Expected Claimants
The expected number of students making insurance claims:
$$\text{Expected Claimants} = \text{Students} \times \text{Claim Rate}$$

### 4. Claims Cost
Total annual claims expenditure:
$$\text{Claims Cost} = \text{Expected Claimants} \times \text{Average Claim Amount}$$

### 5. Administrative Expenses
Administrative overhead is assumed to be 15% of total revenue:
$$\text{Administrative Cost} = \text{Revenue} \times 15\%$$

### 6. Profit / Loss
$$\text{Profit} = \text{Revenue} - \text{Claims Cost} - \text{Administrative Cost}$$

### 7. Return on Investment (ROI)
$$\text{ROI} = \left( \frac{\text{Profit}}{\text{Claims Cost} + \text{Administrative Cost}} \right) \times 100$$

### 8. Loss Ratio
$$\text{Loss Ratio} = \left( \frac{\text{Claims Cost}}{\text{Premium Revenue}} \right) \times 100$$

> **Loss Ratio Interpretation:**
> * **$< 100\%$** $\rightarrow$ Financially healthy / profitable.
> * **$= 100\%$** $\rightarrow$ Break-even (claims equal premium).
> * **$> 100\%$** $\rightarrow$ Underwriting loss (claims exceed premium revenue).


## Projection Results

### Financial Performance Overview

| Metric | Year 2027 | Year 2028 | Year 2029 |
| :--- | ---: | ---: | ---: |
| **Enrolled Students** | 2,000 | 2,400 | 2,880 |
| **Total Revenue (₹)** | ₹14,000,000.00 | ₹16,800,000.00 | ₹20,160,000.00 |
| **Claims Cost (₹)** | ₹18,597,826.00 | ₹22,317,391.20 | ₹26,780,869.44 |
| **Admin Cost (15%) (₹)** | ₹2,100,000.00 | ₹2,520,000.00 | ₹3,024,000.00 |
| **Net Profit / Loss (₹)** | **-₹6,697,826.00** | **-₹8,037,391.20** | **-₹9,644,869.44** |
| **ROI (%)** | **-32.37%** | **-32.37%** | **-32.37%** |
| **Loss Ratio (%)** | **132.84%** | **132.84%** | **132.84%** |


## Visualizations

The project generates several publication-grade charts located in the `graphs/` directory:

* `revenue_growth.png` – Multi-year student enrollment and revenue expansion.
* `claims_cost.png` – Claims expenditure vs. total revenue collected.
* `revenue_vs_profit.png` – Visualizing the growing deficit between revenue and losses.
* `financial_projection.png` – Comprehensive multi-panel financial dashboard.
* `loss_ratio.png` – Loss ratio trajectory benchmarked against the 100% break-even line.


## Technologies Used

* **Python 3**
* **Pandas** – Data manipulation & financial calculations
* **NumPy** – Numerical operations
* **Matplotlib / Seaborn** – Data visualization & chart formatting
