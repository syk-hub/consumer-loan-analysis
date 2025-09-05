# Consumer Credit Analytics

This repository contains notebooks and datasets for analyzing consumer loan data. The current version includes **loan_data_generation.ipynb**, which handles data preparation and generation.

I built an end-to-end analysis of a **synthetic portfolio of 10,000 consumer loans** designed to resemble a **regional consumer bank**. Data is generated in **Jupyter (Python)**, exported to **CSV (Excel)**, loaded into **MySQL** for analysis, and visualized in **Power BI**.

---

## Objectives
- Quantify portfolio size, growth, and mix over time.
- Measure credit quality and default dynamics by month and by term.
- Surface a capacity/affordability signal using `income_to_loan` (Annual_Income ÷ Loan_Amount).
- Provide BI-ready SQL views that minimize downstream modeling.

---

## Dataset

**Fields**  
`Loan_ID` (PK), `Loan_Amount`, `Loan_Term`, `Annual_Income`, `Credit_Score`,  
`Employment_Length` (years), `Loan_Status` (Approved/Default), `Loan_Issue_Date` (DATE)

**Provenance**  
Synthetic data generated in **Jupyter Notebook** (`loan_data_generation.ipynb`) and exported to CSV for loading into MySQL. Distributions are calibrated to a typical regional consumer lender and span multiple years for time-series analysis.

---

## Results — Current Run (10,000 loans)

| Metric | Value |
|---|---:|
| Portfolio size | **10,000** loans |
| Status mix | **Approved: 8,457** • **Default: 1,543** |
| Overall default rate | **15.4%** |

> Figures update if the dataset is regenerated or reloaded.

---

## What the SQL Produces

**Coverage & Sanity**
- Row counts, issuance window (min/max `Loan_Issue_Date`), Approved vs Default distribution.

**Time Series**
- Monthly loans and funded amount by `issue_ym`.
- Yearly totals with YoY deltas and YoY % changes.
- Three-month moving averages for volume and amount.

**Portfolio Mix**
- Credit-band composition (Subprime, Near-Prime, Prime, Prime+) by year: counts, amounts, and % share.

**Credit Quality & Risk**
- Default rate by month (trend).
- Default rate by term (e.g., 36 vs 60 months).
- Top funding months by total amount.

**Capacity / Affordability**
- `income_to_loan` summarized by Loan_Status and by credit_band.

**Descriptive Statistics & Data Quality**
- Means, standard deviations, min/max for key measures.
- Median loan amount by credit band.
- Missingness snapshot for base and derived fields.

**BI-Ready Sources**
- `vw_loans_enriched` — primary modeling surface (adds year/quarter/month, `issue_ym`, credit/income/tenure bands, and `income_to_loan`).
- `loan_v_descriptive_stats` — compact KPI snapshot (counts, means, ranges).

---

## Power BI Report Pages

1. **Portfolio Overview** — KPIs (rows, default rate, avg credit score), loans & amount by `issue_ym` (optional MA3 overlay).  
2. **Credit Mix** — Stacked columns of credit-band share by year (count/amount), band-level stats.  
3. **Risk & Quality** — Default rate by month; default rate by term; `Loan_Term × Loan_Status` matrix.  
4. **Affordability Signals** — `income_to_loan` by status and by credit band; optional scatter (Loan_Amount vs Annual_Income).  
5. **Detail Explorer** — Tabular view from `vw_loans_enriched` with slicers (`issue_year`, `credit_band`, `Loan_Term`).  

---

## Runbook

1. **Create table & schema** — run `sql/00_schema_setup.sql`.  
2. **Load CSV** — `LOAD DATA LOCAL INFILE` from `data/loan_data.csv` (adjust path; match `STR_TO_DATE` to your date format).  
3. **Build views** — run `sql/10_views.sql` to create `vw_loans_enriched` and `loan_v_descriptive_stats`.  
4. **Execute analysis blocks** — run `sql/20_analysis_blocks.sql` block-by-block (cell markers `-- %% [NN]`).  
5. **Open Power BI** — connect to MySQL; import the two views; refresh visuals.

---

## Repository Structure


