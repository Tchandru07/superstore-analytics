# Superstore Analytics & Reporting Automation

An end-to-end data analytics project built around a realistic messy retail export — covering
exploratory data analysis, data quality diagnosis, Excel/VBA automation, and time-series
forecasting. Built to demonstrate the workflow of a data analyst role spanning Python, Excel/VBA,
and BI reporting.

## Project Summary

The raw source file (`data/superstore.csv`) is a 10,800-row retail sales export that looks clean
at a glance but actually **concatenates three separate tables** — Orders, a People lookup, and a
Returns lookup — into one flat sheet with no delimiter, just stray header rows dropped in
mid-file. Diagnosing that (instead of naively deduping the whole file) was the core data-quality
finding of this project.

## Key Findings

- **True Orders table: 9,994 rows** (verified against the well-known Sample Superstore dataset
  size). The other 806 rows are a People table and a Returns table mistakenly appended below it.
- **Zero blank rows and zero true duplicates** once the foreign tables are correctly stripped out
  — a naive "remove duplicate rows" pass would have wrongly flagged 504 rows from the Returns
  table as duplicate orders.
- **17.3% of orders are loss-making**, a combined loss of ~$156K.
- **Tables and Bookcases are the only sub-categories losing money** (-$17.7K and -$3.5K
  respectively), despite healthy sales volume — driven by heavy discounting.
- **Discount correlates negatively with profit** (r = -0.22).
- **Central region is the weakest performer**; West is the strongest.
- Clear **seasonal sales spike from September to November** each year.

## Repository Structure

```
superstore-analytics/
├── data/
│   └── superstore.csv              # Raw export (as received — includes the concatenated tables)
├── scripts/
│   ├── eda.py                      # Exploratory data analysis + data quality checks
│   └── forecast.py                 # Holt-Winters time-series forecast + backtest
├── excel/
│   ├── Superstore_Cleaning_Workbook.xlsx   # Raw data + README/instructions tab
│   └── CleanAndSummarize.bas               # VBA macro: isolates real Orders table, rebuilds KPI summary
└── docs/
    └── findings.md                 # Full write-up of results
```

## How to Run

**Python EDA / forecasting**
```bash
pip install pandas numpy statsmodels scikit-learn
python scripts/eda.py
python scripts/forecast.py
```

**Excel macro**
1. Open `excel/Superstore_Cleaning_Workbook.xlsx`
2. Press `Alt+F11` → File → Import File → select `excel/CleanAndSummarize.bas`
3. Press `Alt+F8`, select `CleanAndSummarize`, click Run
4. Expected result: 9,994 valid rows, 806 foreign rows removed, 0 true duplicates, and a rebuilt
   `Summary` sheet with live `SUMIFS` KPIs by category

## Forecasting Model

Holt-Winters exponential smoothing (additive trend + seasonal, 12-month cycle) on monthly sales.

Backtested against the last 6 known months:

| Metric | Value |
|---|---|
| MAE | ~12,932 |
| MAPE | ~15.6% |
| RMSE | ~17,770 |

The model captures the seasonal Sep/Dec spikes well but underpredicts one-off promotional months
(Aug, Oct) — a known limitation of pure seasonal smoothing without an explicit promotion signal.

## Tech Stack

Python (pandas, numpy, statsmodels, scikit-learn) · Excel (formulas, VBA/macros) · Tableau/Power BI
(dashboard layer, in progress)

## Status

- [x] Exploratory data analysis + data quality diagnosis
- [x] Time-series forecasting model
- [x] Excel/VBA cleaning & reporting automation
- [ ] Tableau/Power BI dashboard
