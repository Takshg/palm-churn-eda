# Palm Club Manager Performance Dashboard

You can view this yourself! At: [https://takshg.github.io/palm-club-dashboard/](https://takshg.github.io/palm-club-dashboard/)

No backend, no dependencies to install beyond a one-line local server.

---

## Repo structure

```
palm-club-dashboard/
├── index.html              ← The dashboard (single file, all logic inside)
├── README.md               ← This file
└── data/                   ← CSVs go here
    ├── employee.csv
    ├── applicant.csv
    ├── employee_changes.csv
    ├── employee_performance.csv
    └── branch.csv
```

---

## Setup

### 1 — Data

```
data/employee.csv
data/applicant.csv
data/employee_changes.csv
data/employee_performance.csv
data/branch.csv
```

### 2 — Run a local server

You must use a local server — browsers block CSV loading when you open `index.html` as a file:// URL.

```bash
# From the palm-club-dashboard/ folder:
python3 -m http.server 8080
```

Then open http://localhost:8080 in your browser.

Other options:

- npx serve .  (if you have Node)
- VS Code right-click index.html -> "Open with Live Server"

---

## Column mapping

The dashboard uses the exact column names from the Palm Club data dictionary:

| File                     | Columns used                                                                                          |
| ------------------------ | ----------------------------------------------------------------------------------------------------- |
| employee.csv             | EmployeeID, ApplicantID, HiredOn, Branch#, Current status, Position, Role, AvgWorkingHours/Week, Wage |
| applicant.csv            | ApplicantID, HighestEducationLevel, YearOfBirth, YearsOfExperience                                    |
| employee_changes.csv     | EmployeeID, NewRole, DateChanged, ReasonForLeaving                                                    |
| employee_performance.csv | EmployeeID, PerformanceScore, DateReviewed                                                            |
| branch.csv               | BranchNo, BranchName, Stars, DatePosted                                                               |

---

## Configuration

Open index.html and find the CFG block near the top of the script section:

  refDate:  new Date('2026-03-14')   // "today" for tenure calculations
  hpThresh: 75                        // avg PerformanceScore for High Performer
  reviewMo: 9                         // months before mandatory promo review triggered
  dataPath: 'data/'                   // path to CSV folder

Add any values from the actual data if they differ.

---

## What each view shows

Overview    — KPI cards, HP vs NHP churn, tenure at exit, branch churn, wage vs retention
Employees   — Filterable table (branch / position / HP tier / search), click any row for
              profile + recommendation, flag for promotion review
Branches    — HP churn per branch vs Google star rating, promotion rates, comparison table
Promotions  — Standardized criteria, HP vs NHP median promo time, retention impact,
              overdue review queue

---

## Troubleshooting

"Could not load CSV files"
  -> You are opening index.html directly. Use python3 -m http.server 8080 instead.

Charts empty / KPIs show dashes
  -> Open browser DevTools (F12 -> Console) to see the specific error.
  -> Check that NewRole values in employee_changes.csv match the exit detection words.

Branch filter shows IDs instead of names
  -> Your branch.csv may be missing a BranchName column. The dashboard falls back to BranchNo.

Employee names show as "Employee 123"
  -> The data dictionary confirms applicant.csv has no name column. This is expected.
     If your file does have names, find the name assignment line in the process() function
     and update it to read the correct column.
