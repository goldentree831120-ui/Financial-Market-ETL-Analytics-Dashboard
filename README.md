# Financial Market ETL & Analytics Dashboard

A end-to-end data pipeline that extracts historical stock market data, transforms it into analysis-ready metrics, loads it into a relational database, and produces statistical analysis and visualizations — built for a quantitative research / data analyst workflow.

## Overview

This project analyzes 6 NSE-listed large-cap stocks across different sectors, benchmarked against the Nifty 50 index (`^NSEI`), covering January 2021 to June 2026.

| Ticker | Sector |
|---|---|
| RELIANCE.NS | Energy / Conglomerate |
| TCS.NS | IT Services |
| INFY.NS | IT Services |
| HDFCBANK.NS | Banking |
| HINDUNILVR.NS | FMCG |
| BHARTIARTL.NS | Telecom |
| ^NSEI | Benchmark Index |

## Pipeline Architecture

```
Extract (yfinance API)
      ↓
Transform (cleaning, returns, volatility, moving averages)
      ↓
Load (SQLite relational database)
      ↓
Analyze (SQL queries, linear regression, correlation)
      ↓
Visualize & Report (matplotlib dashboard, auto-generated summary)
```

### 1. Extract
Pulls daily OHLCV (Open/High/Low/Close/Volume) data via the `yfinance` API for all 7 tickers and saves raw CSVs.

### 2. Transform
- **Data cleaning**: parses dates, removes duplicates, drops rows with missing/invalid values, validates High ≥ Low
- **Daily & log returns**
- **Rolling volatility** (20-day and 60-day, annualized) — used as the dispersion metric
- **Moving averages** (SMA 20/50/200)
- **Cumulative returns**

### 3. Load
Combines all processed data into a single SQLite table (`market_data`) with an index on `(Ticker, Date)` for efficient querying — demonstrating relational database design and usage.

### 4. Analyze
- **Correlation matrix** of daily returns across all tickers
- **Linear regression (Beta)**: each stock's return regressed against the Nifty 50 to estimate systematic risk, with R² and p-values reported for statistical rigor
- **Dispersion ranking**: average annualized volatility per stock

### 5. Visualize & Report
A 4-panel dashboard (cumulative returns, correlation heatmap, beta comparison, rolling volatility) plus an auto-generated text summary highlighting key findings.

## Key Findings

- **Best performer**: BHARTIARTL.NS (+274.7% total return) — a telecom re-rating story, dramatically outperforming the benchmark
- **Worst performer**: TCS.NS (-19.9% total return) — IT services sector underperformance over the period
- **Highest beta**: RELIANCE.NS (1.087) — most sensitive to market moves
- **Lowest beta**: HINDUNILVR.NS (0.516) — defensive, FMCG behavior as expected
- **Highest volatility**: INFY.NS (23.8% annualized)
- **Best diversification pair**: BHARTIARTL.NS & HINDUNILVR.NS (correlation of only 0.14) — combining telecom and FMCG offers the most diversification benefit in this basket
- **Sector clustering confirmed**: TCS and INFY (both IT) show the highest pairwise correlation (0.72)

## Tech Stack

- **Python**: pandas, numpy, matplotlib, scipy
- **Data source**: yfinance (Yahoo Finance API)
- **Database**: SQLite
- **Environment**: Google Colab

## How to Run

1. Install dependencies:
   ```bash
   pip install yfinance pandas numpy matplotlib scipy
   ```
2. Run the pipeline in order: Extract → Transform → Load → Analyze → Visualize
3. Outputs: `db/market_data.db`, `dashboard_overview.png`, `summary_report.txt`

## Limitations & Future Work

- Single-factor regression model (market beta only) — R² values (0.13–0.51) suggest other factors (sector, size, momentum) could improve explanatory power
- No transaction costs or survivorship bias adjustments
- Could extend with a multi-factor model (Fama-French style) or a larger, sector-balanced universe of stocks
