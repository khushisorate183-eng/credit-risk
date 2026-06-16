# Credit Risk Assessment & Portfolio Analysis
### SQL · Python · Power BI · Basel III Methodology

A comprehensive consumer credit risk analysis
on 150,000 borrower records covering database
design, SQL-based portfolio analysis, Python EDA,
probability of default modelling, credit
scorecard construction, and Basel III stress
testing.

![PD Model CAP Curve](visuals/chart14_cap_gini_curve.png)

## The business problem

Banks need to know in advance which borrowers
are likely to default so they can decline
high-risk applications, set appropriate
interest rates, and monitor vulnerable accounts
before losses crystallise. This project builds
that system end-to-end — from raw data to a
credit committee report with a quantified
policy recommendation.

---

## Five headline findings

| Finding | Value |
|---------|-------|
| Total portfolio Expected Loss | **$273.46M** |
| PD model Gini coefficient | **0.6131** (exceeds 0.60 benchmark) |
| EL reduction at optimal threshold | **$164.2M (60%)** at 76.4% approval |
| Dominant default predictor | **Utilisation (IV=1.1143)** — 111× stronger than DTI |
| Stress test migrations (severe) | **22,079** borrowers upward under 35% income shock |

---

---

## Methodology

### Phase 1 — Database & SQL analysis (Days 1–10)

Built a PostgreSQL database with 150,000
consumer loan records. Implemented a
rules-based risk tier classification system
anchored to regulatory thresholds:

- **DTI threshold**: 43% (CFPB qualified
  mortgage standard)
- **Delinquency threshold**: 90-day past due
  (Basel III NPL classification)
- **Utilisation threshold**: 75% (FICO
  severe overextension)

Result: 18.1× tier separation (High 45.8%
vs Low 2.5% default rate) — significantly
exceeding the 3-5× industry benchmark.

Wrote 18 finance-framed SQL queries covering
portfolio concentration, vintage delinquency
analysis, Expected Loss calculation, risk
migration matrices, Pareto concentration, and
credit policy simulation.

Query optimisation via composite and
single-column indexes achieved **95.88%
execution time improvement** (210ms → 8.6ms).

### Phase 2 — Python EDA (Days 11–15)

Data quality treatment:
- DTI winsorised at 2.0 (31,045 rows affected,
  mean reduced from 353 to 0.68)
- Utilisation capped at 1.0 (3,321 rows)
- Income imputed by age-band median
  (29,731 missing values resolved)

Key findings from 15 charts:
- Revolving utilisation dominates: IV=1.1143
  (Very Strong) vs DTI IV=0.0253 (Weak)
- Defaulters are on average 7 years younger
  than non-defaulters (45.91 vs 52.69)
- Expected Loss revised to **$273.46M** on
  cleaned data (from $140.5M on raw SQL)
- Real inflection point: 50% utilisation
  (not FICO's standard 30% threshold)

### Phase 3 — PD modelling & scorecard (Days 16–20)

**Logistic regression** chosen deliberately
over black-box alternatives — Basel III IRB
models require auditable coefficients that
regulators can inspect variable by variable.

Model performance:
- Gini: **0.6131** (exceeds 0.60 retail benchmark)
- AUC: 0.8065
- KS: 0.4752

Threshold optimised to **0.55** using
F-beta scoring (beta=2, recall weighted 2×):
- Approval rate: 76.4%
- Defaults avoided: 6,773
- EL saved: **$164.2M (60% reduction)**

**FICO-style credit scorecard** using log-odds
scaling (Siddiqi, 2006):
- Scale: 468–686 (higher = lower risk)
- 5 bands: Very Poor → Excellent
- Monotonicity confirmed ✓
- 94% of score variance from 2 variables:
  90-day delinquency (139pts) + utilisation
  (66pts)

### Phase 4 — Stress testing (Days 21–25)

Basel III Pillar 2 ICAAP framework:

| Scenario | Income shock | Migrations | High tier |
|----------|-------------|------------|-----------|
| Baseline | 0% | — | 2.24% |
| Mild | -10% | 5,247 | 2.44% |
| Moderate | -20% | 11,528 | 2.67% |
| Severe | -35% | 22,079 | 3.04% |
| Combined | -20% + util +15ppt | 13,958 | 2.67% |

Combined scenario drives 2,430 additional
migrations vs income-only — confirming
utilisation as the marginal reclassification
trigger under adverse conditions.

---

## Key deliverables

| Deliverable | Location |
|------------|----------|
| Credit committee report | `/report/credit_committee_report_final.md` |
| Credit policy recommendation | `/report/credit_policy_recommendation.md` |
| Basel III alignment | `/report/basel3_alignment.md` |
| Decision log | `DECISIONS.md` |
| Full EDA notebook | `/notebooks/eda.ipynb` |
| SQL query library | `/sql/queries/` |

---

## How to run this project

### Prerequisites
- PostgreSQL 17
- Python 3.9+
- Jupyter Notebook

### Setup

**1. Clone the repository**
```bash
git clone https://github.com/[your-username]/credit-risk-assessment.git
cd credit-risk-assessment
```

**2. Install Python dependencies**
```bash
pip install pandas sqlalchemy psycopg2-binary
pip install matplotlib seaborn scikit-learn jupyter
```

**3. Set up the database**
```bash
# Create database in PostgreSQL
psql -U postgres -c "CREATE DATABASE credit_risk_db;"

# Run schema
psql -U postgres -d credit_risk_db -f sql/schema.sql
psql -U postgres -d credit_risk_db -f sql/indexes.sql
```

**4. Load the dataset**
Download `cs-training.csv` from Kaggle (see
`/data/README.md`) and load via psql:
```bash
psql -U postgres -d credit_risk_db
\copy borrowers (...) FROM 'path/to/cs-training.csv' DELIMITER ',' CSV HEADER NULL 'NA';
```

**5. Run the notebook**
```bash
cd notebooks
jupyter notebook eda.ipynb
```
Update the database password in Cell 1
before running.

---

## Policy output

Analysis produced a dual-layer credit approval
framework recommendation with quantified
financial outcomes:
- EL reduction: $273.46M → $109.3M (60%)
- Approval rate maintained: 76.4%
- Break-even analysis confirms financial
  self-justification at standard loan economics

Full recommendation: `/report/credit_policy_recommendation.md`

---

## Basel III alignment

This project is conceptually aligned with
Basel III Internal Ratings-Based (IRB)
approach across six dimensions:

1. Risk tiers → IRB rating grades
2. PD model → PD estimation (Gini validated)
3. EL = PD × LGD × EAD → Expected Loss
4. Scorecard → IRB scoring tool
5. Stress test → Pillar 2 ICAAP
6. Delinquency monitoring → Early warning

Full mapping in `/report/basel3_alignment.md`

---

## Tools & technologies

| Category | Tools |
|---------|-------|
| Database | PostgreSQL 17, pgAdmin 4 |
| Analysis | Python 3, pandas, NumPy |
| Visualisation | matplotlib, seaborn, Power BI |
| Modelling | scikit-learn (LogisticRegression) |
| Notebook | Jupyter |
| Version control | Git, GitHub |

---

## Author

**Khushi Sorate**
MSc Financial Technology · University of Bristol
www.linkedin.com/in/khushi-sorate
khushisorate183@gmail.com

---

*This project was built over 30 days as a
portfolio piece demonstrating credit risk
analysis skills for fintech and banking
roles. All findings are based on public
Kaggle data and do not represent advice
on any real lending portfolio.*

## Repository structure
