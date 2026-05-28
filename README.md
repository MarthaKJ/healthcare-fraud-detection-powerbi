# Healthcare Provider Fraud Detection — Power BI Report

An end-to-end Power BI analytics report that helps fraud investigators triage and
investigate healthcare providers at scale. It combines rule-based risk scoring with
machine-learning fraud probabilities to surface the highest-risk providers first,
then lets analysts drill into any individual provider to build a case.

> Built with Power BI Desktop, DAX, Power Query, and a star-schema data model.

---

## The Problem

Healthcare insurers process hundreds of thousands of claims across thousands of
providers. Manual review doesn't scale — investigators need a system that:

1. Shows the overall scale of fraud exposure at a glance
2. Ranks providers by risk so the worst offenders are reviewed first
3. Lets an analyst drill into a single provider's behavior to confirm or dismiss a case

This report delivers all three as a connected investigation workflow.

---

## The Dataset (high level)

- **~694,000 claims** across **~7,000 distinct providers**
- **~$689M** in total reimbursement
- Provider, beneficiary, diagnosis (ICD-9), and procedure detail
- A machine-learning scoring layer producing a fraud probability and risk band per provider

*(Data is synthetic / for demonstration — no real patient or provider information.)*

---

## The Report — a 3-Page Investigation Workflow

### 1. Overview — "What's the situation?"
Executive summary of the whole portfolio: total claims, total reimbursement, distinct
providers, ML high-risk count, percent of claims flagged, and ML-vs-baseline lift.
A claims-over-time trend shows volume patterns across the year.

### 2. Investigation Queue — "Who do I look at first?"
A risk-ranked, color-coded table of flagged providers with conditional formatting
(red = high risk). Slicers for dataset, potential-fraud status, and risk-score range
let analysts narrow the queue. This is the analyst's daily worklist.

### 3. Provider Deep-Dive — "Is this provider actually fraudulent?"
Reached via **drillthrough**: right-click any provider in the Queue and jump to this
page, automatically filtered to that one provider. Shows their risk score, claim
volume over time, weekend-billing ratio, top diagnoses by volume, and total
reimbursement — everything needed to investigate an individual.

---

## Key Features

- **Drillthrough workflow** — Queue → right-click provider → filtered Deep-Dive page,
  mirroring how a real fraud analyst investigates
- **Star-schema data model** — dimension tables (Provider, Date, Beneficiary) and fact
  tables (Claims, Claim-Diagnosis, Claim-Procedure) plus an ML scores table
- **Centralized measures table** — 25+ reusable DAX measures covering risk scoring,
  ranking, ratios, and ML outputs
- **ML + rule-based scoring** — compares a machine-learning fraud probability against a
  baseline rule-based risk score (ML-vs-baseline lift)
- **Conditional formatting** — risk heatmap draws the eye to the worst providers
- **Consistent multi-page design** — shared header, navigation states, and theme

---

## Fraud Signals the Report Surfaces

| Signal | Why it matters |
|--------|----------------|
| Composite Risk Score | Blended risk indicator per provider |
| ML Fraud Probability | Machine-learning estimate of fraud likelihood |
| Weekend Claim Ratio | Unusually high weekend billing can indicate fraud |
| Top Diagnosis Share | Over-concentration on one diagnosis is a red flag |
| Reimbursement vs Median | Providers billing far above peers stand out |

---

## Data Model

A star schema designed for clean filter propagation:

- **Dimensions:** DimProvider, DimDate, DimBeneficiary
- **Facts:** FactClaims, FactClaimDiagnosis, FactClaimProcedure
- **ML layer:** MLScores (fraud_probability, ml_risk_band)


One modeling note worth highlighting: because claims and diagnoses live in separate
fact tables, a "Total Claims" measure does not respond to a diagnosis filter. The
"Top Diagnoses" chart instead counts rows directly from the diagnosis fact table —
a deliberate choice driven by understanding filter context across fact tables.

---

## Sample DAX

<!-- Replace these with your actual measure definitions -->

```
Open Cases = DISTINCTCOUNT(DimProvider[Provider])
```

```
High Risk Count =
CALCULATE(
    DISTINCTCOUNT(DimProvider[Provider]),
    DimProvider[Risk Band] = "1-High"
)
```

```
$ at Risk =
CALCULATE([Total Reimbursement], DimProvider[Risk Band] = "1-High")
```

---

## Screenshots

| Page | Preview |
|------|---------|
| Overview | `screenshots/overview.png` |
| Investigation Queue | `screenshots/queue.png` |
| Provider Deep-Dive | `screenshots/deep-dive.png` |
| Data Model | `screenshots/data-model.png` |

**Drillthrough demo:** `screenshots/drillthrough-demo.gif`

---

## How to Open

1. Download `HealthcareFraudDetection.pbix`
2. Open it in [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free)
3. Use the navigation header to move between pages
4. On the Investigation Queue page, **right-click any provider → Drill through →
   Provider Deep-Dive** to see the filtered detail view

---

## Tech Used

- **Power BI Desktop** — report build and visuals
- **DAX** — measures, risk scoring, ranking
- **Power Query (M)** — data shaping
- **Star-schema modeling** — fact/dimension design

---


