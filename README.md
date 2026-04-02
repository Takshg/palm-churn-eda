# 🌴 palm-churn-eda

******2nd Place — BOLT BootCamp 2026******

Alpha Boltlytics Consulting — EDA repository for diagnosing high-performer attrition at Palm Club Restaurants (Metro Vancouver, 7 branches).

> Produced for BOLT BootCamp 2026 by Taksh Girdhar, Tanay Mahendru, Muskan Bhatia & Fiona Gandhi. 

---

## Context

Palm Club is a local restaurant chain with 7 locations across Metro Vancouver. Despite running quarterly performance reviews, the chain has a **75% overall employee turnover rate** and an  **~80% turnover rate specifically among high performers** . This repository contains the full exploratory data analysis pipeline used to diagnose root causes and support data-driven recommendations to Palm Club management.

**Key finding:** This is a process problem, not a talent problem. The data to act already existed, it just wasn't being acted on.

---

## Challenges Identified

**Challenge 1 — Promotion delays for high performers.** High performers wait a median of 7 months for their first promotion vs. 4 months for average performers, despite outscoring them. Over 75% of departing high performers cited better offers or lack of growth as their reason for leaving.

**Challenge 2 — Inconsistent promotion criteria within and across branches.** HP exit rates vary by 31 percentage points across branches (78.7% at UBC to 100% at Surrey and Downtown). Within the same branch, a lower-scoring employee (72.2) was promoted in 0.7 months while a higher scorer (92.7) waited 11.4 months.

**Challenge 3 — No process to convert performance data into action.** 33 high performers were never promoted despite having 3 or more performance reviews on file. 72% of part-time high performers were never promoted, compared to over 20% for full-time employees.

---

## Recommendations

**Recommendation 1 — Standardised Promotion Pipeline:** Any employee in the top quartile (Bayesian-adjusted score) for 2 consecutive quarterly reviews is automatically flagged. Managers have a 30-day window to promote, defer with written reason, or initiate a 90-day development plan. Silence escalates to a regional manager.

**Recommendation 2 — Manager Accountability & Training:** HP retention rate added as a scored KPI in every manager's quarterly review. Managers trained to use a standardized promotion scorecard (performance score, attendance, peer feedback) replacing gut-feel decisions.

**Target impact:** Reduce HP turnover from 79.85% (214/268 exits) to 70% (188 exits), retaining 26 additional high performers across 7 branches.

---

## Repository Structure

```
palm-churn-eda/
│
├── data/
│   ├── BOLT_Employees.csv
│   ├── BOLT_Applicants.csv
│   ├── BOLT_EmployeeChanges.csv
│   ├── BOLT_Performance.csv
│   └── BOLT_Branch.csv
│
├── Dataset_Profile_Reports/          # ydata-profiling HTML reports (one per table)
│
├── Outliers_Analysis/                # Outlier detection outputs and flagged records
│
├── palm-club-dashboard/              # Manager performance dashboard source code
│
├── load_data.ipynb                   # Data ingestion & initial inspection
├── Data_Profile_Generation.ipynb    # Automated dataset profiling via ydata-profiling
├── Outlier_Analysis.ipynb            # Outlier detection & flagging
├── Palm_Club_Emp_Turnover_EDA.ipynb # Main EDA notebook (9 guiding questions)
├── Checks.ipynb                      # SQL-based validation checks on employee_master
│
├── employee_master.csv               # Cleaned, merged master table (output of EDA pipeline)
├── index.html                        # Dashboard entry point
├── Dashboard README.md               # Dashboard setup and usage guide
└── README.md
```

---

## Notebook Execution Order

The notebooks are designed to be run in sequence:

1. **`load_data.ipynb`** — Ingests all five raw `BOLT_*.csv` tables and performs initial shape, dtype, and null inspection.
2. **`Data_Profile_Generation.ipynb`** — Runs `ydata-profiling` on each table and saves interactive HTML reports to `Dataset_Profile_Reports/`.
3. **`Outlier_Analysis.ipynb`** — Detects and flags statistical outliers; outputs saved to `Outliers_Analysis/`.
4. **`Palm_Club_Emp_Turnover_EDA.ipynb`** — Main analysis notebook covering all 9 guiding questions (see table below).
5. **`Checks.ipynb`** — Loads `employee_master.csv` into an in-memory SQLite database and runs SQL validation queries (e.g. education-level counts, churn cross-tabs).

`employee_master.csv` is the cleaned, merged output of the EDA pipeline. It can be loaded directly into any notebook to skip re-running the cleaning steps.

---

## Analysis Coverage

| Section | Topic                                                            | Guiding Question |
| ------- | ---------------------------------------------------------------- | ---------------- |
| 3       | Define "High Performer" (Bayesian-adjusted, top 25th percentile) | Q1a              |
| 4       | Churn rate: high performers vs. rest of workforce                | Q1b              |
| 5       | Tenure-at-exit: when in their career do HPs leave?               | Q2               |
| 6       | Time-in-role before first promotion                              | Q3 & Q4          |
| 7       | Does promotion improve retention?                                | Q5               |
| 8       | Branch-level HP churn differences                                | Q6               |
| 9       | Wage tier vs. performance alignment                              | Q7               |
| 10      | Working hours & exit risk                                        | Q8               |
| 11      | Full-time vs. part-time status & short-term intent               | Q9               |

---

## Key Definitions

**High Performer** — An employee whose Bayesian-adjusted average performance score places them in the top 25th percentile across the organization (threshold: Bayesian score > 82.97). Fired employees are excluded from churn analysis.

**Churn** — A record in `BOLT_EmployeeChanges` where `NewRole` indicates a voluntary exit or termination (fired employees excluded).

**Reference Date** — `2026-03-14`. All tenure calculations for currently active employees use this as their end date.

**Full-time vs. Part-time** — Used as the primary segmentation variable in place of student vs. non-student status, as employment type proved more analytically predictive of churn behaviour.

---

## Dashboard

The Manager Performance Dashboard provides real-time visibility into HP status, tenure stage, and promotion readiness across all 7 branches. Each dashboard feature maps directly to a challenge identified in the analysis:

| Dashboard Feature                                  | Challenge Addressed                   |
| -------------------------------------------------- | ------------------------------------- |
| HP at-risk flag (90+ days in role without review)  | Promotion delays                      |
| FT vs. PT performance parity view                  | Part-time employees overlooked        |
| Standardised promotion scorecard                   | No process to act on performance data |
| Manager KPI tracker (HP retention rate, quarterly) | Lack of manager accountability        |

🔗 **Live dashboard:** [https://takshg.github.io/palm-churn-eda/](https://takshg.github.io/palm-churn-eda/)

See `Dashboard README.md` for local setup instructions.

---

## Setup

```bash
pip install pandas numpy matplotlib seaborn ydata-profiling sqlalchemy
jupyter notebook
```

No external API keys or database connections required. All data is loaded from local CSV files in `data/`.

---

## Notes & Pitfalls

* **Employees with no performance records** will produce `NaN` for their Bayesian-adjusted score. Filter or handle separately before applying the high-performer flag.
* **`BOLT_EmployeeChanges` has multiple rows per employee.** Use `.groupby("EmployeeID").first()` or `.last()` deliberately and document the choice.
* **Tenure for active employees** is a lower bound capped at the reference date. Exclude active employees from tenure-at-exit charts; include them in retention analyses.
* **Fired employees are excluded** from all churn and retention analyses per the team's stated assumptions.
* **Wage is categorical** , not continuous. Encode before any correlation analysis.
