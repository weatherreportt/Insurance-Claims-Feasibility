# Root Cause Analysis

## 1. Key driver(s) of the revenue drop

The claims data suggests that Yupa Insurance’s revenue drop is primarily driven by **higher claim payouts**, not by a fall in premiums or number of insured customers.

### Main drivers
- **High concentration of claims at CityCare Hospital**  
  CityCare Hospital contributes a disproportionately large share of claims, making it the biggest cost driver.

- **Repeated high-cost procedure claims**  
  **Knee Surgery** appears frequently and is associated with a high claim amount of **₹120,000**, which increases total payouts.

- **Repeated claims by the same patient**  
  Patient **P123** appears multiple times in the dataset, which suggests unusually frequent claims and possible duplicate or repeated billing patterns.

### Interpretation
These patterns indicate that Yupa is paying out more in claims than expected, which reduces retained revenue and profitability.

---

## 2. What process or contractual loophole may be exploited?

The data suggests that the current claims process may have weak controls in one or more of the following areas:

- **Duplicate claim detection may be weak**  
  The same patient appears repeatedly with the same hospital, procedure, and claim amount.

- **High-value procedures may not require enough verification**  
  Expensive procedures like Knee Surgery may be reimbursed without strong pre-authorization checks.

- **Provider-level monitoring may be limited**  
  One hospital contributing a very large share of claims suggests that hospital billing patterns may not be closely monitored.

- **Claim validation may be too automatic**  
  If claims are settled without checking supporting documents carefully, repeated or inflated billing can slip through.

### Likely loophole
The most likely loophole is that **repeat claims for the same patient and procedure are not being flagged early enough**, allowing repeated reimbursements.

---

## 3. How can Yupa mitigate this risk?

### Immediate actions
- Introduce **duplicate claim detection** for the same patient, hospital, procedure, and amount.
- Require **pre-authorization** for high-cost procedures such as Knee Surgery.
- Flag patients with unusually high claim frequency for manual review.
- Audit hospitals with unusually high claim volumes, especially CityCare Hospital.

### Longer-term controls
- Build a **claims analytics dashboard** to monitor hospital- and patient-level patterns.
- Use **fraud detection rules** for repeated or identical claims.
- Tighten contract terms for hospitals with abnormal billing behavior.
- Require stronger supporting documentation for expensive and repeated procedures.

---

## 4. Final conclusion

Yupa Insurance’s revenue drop is most likely caused by **excessive claim payouts driven by repeated high-value claims**, especially from **CityCare Hospital** and **Patient P123**. The pattern suggests a possible loophole in claim verification and duplicate-billing control. Strengthening claim validation, monitoring hospital behavior, and flagging repeated claims will help reduce leakage and improve profitability.