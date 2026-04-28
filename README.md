# Portfolio Risk Analysis Framework

A Python tool that pulls historical price data for any equity/ETF portfolio and produces a full quantitative risk report — metrics, stress tests, and a visual dashboard — in a single function call.

---

## What it does

You pass in a list of tickers with allocations and an investment horizon. The framework handles everything else:

- Downloads adjusted close prices via `yfinance` (default 5-year lookback)
- Computes risk and return metrics for each asset and the portfolio as a whole
- Runs a Monte Carlo simulation (10,000 paths) and fits a GARCH(1,1) model for volatility forecasting
- Stress-tests the portfolio against 2008 and COVID-19 using a 3-factor model (SPY, TLT, GLD)
- Generates a composite risk score (1–10) from volatility, drawdown, and VaR
- Prints a formatted terminal report and saves a 5-panel visual dashboard as PNG

---

## Metrics computed

**Per asset:** annualized return, annualized volatility, Sharpe ratio, Sortino ratio, Beta vs SPY, projected total return at horizon (expected + 95% downside)

**Portfolio level:** annualized return and volatility, Sharpe, Sortino, Max Drawdown, Historical VaR (95%), Monte Carlo VaR (95%), Historical CVaR, Monte Carlo CVaR, GARCH(1,1) 21-day vol forecast, composite risk score

---

## Dashboard

Five panels saved as `portfolio_risk_dashboard.png`:

1. Cumulative return of each asset vs SPY
2. Rolling 30-day volatility
3. Portfolio return distribution with VaR and CVaR markers (historical vs MC)
4. Correlation heatmap
5. GARCH(1,1) conditional volatility forecast (21 trading days)

---

## Usage

```python
portfolio = [
    {"ticker": "SCHD",  "allocation": 0.30},
    {"ticker": "VGT",   "allocation": 0.25},
    {"ticker": "AVUV",  "allocation": 0.20},
    {"ticker": "DGRO",  "allocation": 0.15},
    {"ticker": "CASH",  "allocation": 0.10},
]

result = analyze_portfolio(portfolio, horizon_years=5)
```

`CASH` is treated as a zero-return, zero-volatility position and excluded from yfinance downloads. Allocations that don't sum to exactly 1.0 are normalized automatically.

---

## Install dependencies

```bash
pip install yfinance pandas numpy scipy arch tabulate matplotlib seaborn pandas-datareader
```

---

## Output structure

`analyze_portfolio()` returns a dict with keys: `metrics`, `stress`, `var_mc`, `garch`, `prices`, `macro`

---

## Notes

- Assets with a listing date inside the lookback window are included from their first available date. Prior days are excluded from calculations rather than zero-filled, so returns and volatility are not distorted.
- Beta is estimated via OLS regression against SPY daily returns.
- Sortino uses only negative return days in the denominator.
- The risk score weights: volatility 40%, max drawdown 40%, VaR 20%. Capped at 10.

---

## Stack

Python · yfinance · NumPy · pandas · SciPy · arch · Matplotlib · Seaborn

---

## Dashboard
<img width="3766" height="4258" alt="portfolio_risk_dashboard" src="https://github.com/user-attachments/assets/3a40ce7a-6722-49be-bded-e54fe28bb6c1" />
