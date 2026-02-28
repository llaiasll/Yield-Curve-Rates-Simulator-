# Yield Curve Trade Simulator

A DV01 neutral yield curve trade backtester built in Python using Yahoo Finance data.

This project simulates steepener and flattener trades across US Treasury maturities using a duration based pricing approximation. The goal is to model how relative curve movements impact PnL while neutralizing parallel rate shifts.

---

## Overview

This simulator:

- Pulls US Treasury yield data from Yahoo Finance
- Constructs curve spreads such as 5s10s, 5s30s, 10s30s
- Sizes positions to be DV01 neutral
- Simulates daily PnL using modified duration approximation
- Computes risk metrics including Sharpe ratio and max drawdown
- Produces visualizations of yields, spreads, and cumulative PnL

The framework is designed to reflect how relative value curve trades are structured on a rates desk.

---

## Financial Intuition

A yield curve trade isolates exposure to changes in the slope of the curve.

Examples:

5s30s Steepener  
Long 30Y duration  
Short 5Y duration  
Profits if long end yields rise less than short end yields, or if the spread widens

5s30s Flattener  
Short 30Y duration  
Long 5Y duration  
Profits if long end yields rise more than short end yields, or if the spread compresses

To isolate curve exposure, the trade is sized to be DV01 neutral. This reduces sensitivity to parallel rate shifts.

---

## Methodology

### 1. Data

Treasury yield index proxies from Yahoo Finance:

| Label | Ticker |
|-------|--------|
| 5Y    | ^FVX   |
| 10Y   | ^TNX   |
| 30Y   | ^TYX   |

Yields are converted from percent format to decimal format.

---

### 2. Duration Model

Modified duration is computed assuming a par bond structure:

- Coupon rate equals yield
- Semiannual payments
- Standard discounted cash flow pricing

Price change approximation:

```
Delta P ≈ - ModifiedDuration × DeltaYield × Notional
```

This first order Taylor expansion is standard for risk estimation on fixed income desks.

---

### 3. DV01 Neutral Sizing

DV01 measures dollar price change per 1 basis point move in yield.

```
DV01 = Notional × ModifiedDuration × 0.0001
```

The simulator:

- Anchors DV01 on one leg
- Sizes the opposite leg so total DV01 is approximately zero

This isolates curve shape exposure.

---

### 4. PnL Calculation

Daily PnL:

```
PnL_long  = - Position_long  × Duration_long  × Notional_long  × DeltaYield_long
PnL_short = - Position_short × Duration_short × Notional_short × DeltaYield_short
```

Total PnL is the sum of both legs.

Optional carry can be included.

---

### 5. Risk Metrics

- Annualized Sharpe Ratio
- Average daily PnL
- Daily PnL volatility
- Maximum drawdown
- Rolling Sharpe

---

## Example Usage

### Install dependencies

```
pip install yfinance pandas numpy matplotlib
```

### Fetch data

```
y = fetch_yields(labels=("5y", "10y", "30y"), start="2016-01-01")
```

### Simulate trade

```
df, info = simulate_curve_trade(
    yields_df=y,
    short_leg="5y",
    long_leg="30y",
    trade="steepener",
    dv01_target=10000,
    include_carry=False
)
```

### View results

```
print_trade_summary(info)
plot_results(df, info)
```

---

## Customization

You can modify:

- Curve legs such as 10s30s or 5s10s
- Steepener or flattener
- DV01 size
- Carry inclusion
- Backtest start date

---

## Limitations

- Uses yield index proxies instead of tradable futures
- Duration approximation ignores convexity
- Does not model funding costs or repo
- Does not model cheapest to deliver effects
- Assumes par bond structure

This is a research level simulator designed for educational and interview demonstration purposes.

---

## Possible Extensions

- Convexity adjustment
- Treasury futures based DV01 modeling
- Rolling entry signals
- Macro regime filters
- Monte Carlo stress testing
- PCA based curve factor decomposition

---

## Relevance For Sales and Trading

This simulator demonstrates:

- Understanding of DV01 and duration risk
- Curve trade structuring logic
- Risk neutral sizing methodology
- Fixed income PnL mechanics
- Quantitative backtesting ability
- Clear financial interpretation of results

It reflects the type of analysis performed on a macro or rates desk when evaluating steepener and flattener trades.

---

## Author

Quantitative fixed income research project built using Python and Yahoo Finance data.
Jeffrey Liu, Feb. 2026
