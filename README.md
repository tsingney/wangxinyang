# S&P 500 Sector Performance Dashboard

> **ACC102 Mini Assignment – Track 2 | Xi'an Jiaotong-Liverpool University | 2024-25 Semester 2**

---

## 1. Problem & User

**Analytical Question:** Which S&P 500 sectors delivered the best returns, lowest volatility, and strongest risk-adjusted performance from 2020 to 2024?

**Target User:** Finance students, individual investors, and anyone seeking a data-driven overview of U.S. equity sector dynamics without needing access to a professional database.

---

## 2. Data

| Item | Detail |
|---|---|
| **Source** | Yahoo Finance via the `yfinance` Python library |
| **Instrument** | SPDR S&P 500 Sector ETFs (XLK, XLF, XLV, XLY, XLI, XLE, XLU, XLRE, XLB, XLC, XLP) |
| **Coverage** | 11 GICS sectors, daily adjusted closing prices |
| **Period** | 2020-01-01 → 2024-12-31 |
| **Access date** | April 2026 |
| **Key fields** | Adjusted close price → daily returns → cumulative returns, annualised volatility, Sharpe ratio, max drawdown |

No data files are stored in this repository. The notebook downloads data directly from Yahoo Finance at runtime.

---

## 3. Methods (Python Steps)

1. **Parameterised data acquisition** – dates and tickers stored as variables (f-string style), passed to a reusable `fetch_sector_prices()` function. The function includes two defensive checks: it warns on tickers that return no data (e.g. if a symbol is delisted or mistyped) and reports any sector whose history starts later than the requested `START_DATE` (e.g. XLC began trading in June 2018).
2. **Data cleaning** – `.dtypes` inspection, then a *conditional* forward-fill: missing values are reported first, and `.ffill()` is only applied if any are present. This avoids silently masking data-quality issues when the data is already clean.
3. **Return computation** – `.pct_change()` for daily returns; `(1 + r).cumprod()` for a cumulative index rebased to 100.
4. **Summary statistics** – annualised return, annualised volatility, Sharpe ratio, max drawdown, total return — wrapped in `compute_summary_stats()`. Metrics are computed in direct annualised form so each line of code maps 1:1 to its textbook definition:

   ```
   ann_return = mean(daily) * 252
   ann_vol    = std(daily)  * sqrt(252)
   sharpe     = (ann_return - rf_annual) / ann_vol
   ```

   `max_drawdown()` is defined as a separate top-level helper for reuse.
5. **Annual return pivot** – `.groupby(year)` → sector rotation heatmap.
6. **Visualisation** – 6 charts: cumulative return lines, risk-return scatter, Sharpe bar chart, rolling 30-day volatility, correlation heatmap, annual rotation heatmap.

All Python operations mirror the workflow taught in ACC102 Weeks 4–6 (pandas DataFrame manipulation, reusable functions, f-string parameterisation).

---

## 4. Key Findings

*(These are illustrative; actual values depend on the live download)*

- **Technology** typically delivers the highest Sharpe ratio and total return over the 2020–2024 window, driven by post-COVID digital acceleration
- **Energy** shows the highest volatility and the widest annual return swings — from worst in 2020 to best in 2022 — reflecting oil price cycles
- **Utilities** and **Consumer Staples** consistently show the lowest volatility, making them defensive anchors in a portfolio
- **Correlation** between most sectors exceeds 0.7, with Energy and Utilities showing the weakest co-movement with Technology — useful for diversification
- **COVID-19 (Q1 2020)** caused a simultaneous spike in rolling volatility across all sectors, visible clearly in Chart 4

---

## 5. How to Run

```bash
# 1. Clone the repository
git clone https://github.com/YOUR_USERNAME/acc102-sector-dashboard.git
cd acc102-sector-dashboard

# 2. Install dependencies
pip install -r requirements.txt

# 3. Create figures folder (the notebook also does this automatically)
mkdir -p figures

# 4. Open and run the notebook
jupyter notebook acc102_notebook.ipynb
```

**Important:** run all cells top-to-bottom via **Kernel → Restart & Run All**. The imports live in the first cell, so skipping to the middle will fail with `NameError: name 'pd' is not defined`.

Charts are saved automatically to `/figures/`.

To change the analysis period, edit only the `START_DATE`, `END_DATE`, and `RISK_FREE` variables in the **Parameters** cell (Section 1) — all downstream cells update automatically.

---

## 6. Demo Video

📹 [Watch the 1–3 minute demo](YOUR_VIDEO_LINK_HERE)

---

## 7. Repository Structure

```
acc102-sector-dashboard/
├── acc102_notebook.ipynb   ← main analysis notebook
├── README.md               ← this file
├── requirements.txt        ← Python dependencies
├── reflection_report.md    ← 500–800 word reflection
└── figures/                ← auto-generated charts
    ├── chart1_cumulative_returns.png
    ├── chart2_risk_return.png
    ├── chart3_sharpe_ratio.png
    ├── chart4_rolling_volatility.png
    ├── chart5_correlation_heatmap.png
    └── chart6_annual_rotation.png
```

---

## 8. Limitations & Next Steps

- **ETF proxy bias** — SPDR ETFs carry small tracking error and expense ratios vs. the pure GICS indices
- **Fixed risk-free rate** — held constant at 4.5%; real U.S. rates moved from ~0% (2020–21) to >5% (2023–24)
- **Simple-rate rf approximation** — the Sharpe calculation treats rf as a simple annual rate rather than its compounded-daily equivalent `(1 + rf)**(1/252) - 1`; the difference at rf ≈ 4.5% is under 1 bp/day and does not change the cross-sector ranking
- **COVID-19 distortion** — the March 2020 crash inflates long-run volatility estimates
- **Survivorship bias** — current ETF constituents reflect today's index composition, not historical composition
- **No macro context** — sector performance shown in isolation, without linking to interest rate cycles or GDP

**Next steps:** add Fama-French factor decomposition, rolling Sharpe, and an economic cycle overlay (NBER recession bands, rate cycle regimes).

---

## Dependencies

See `requirements.txt`. Core libraries: `yfinance`, `pandas`, `matplotlib`, `seaborn`, `numpy`.

---

*Module: ACC102 Artificial Intelligence-Driven Data Analytics | IBSS, Xi'an Jiaotong-Liverpool University*
