# Long-Only Momentum & Composite Strategy

This repository contains Python code for a **long-only quantitative strategy** designed to achieve superior risk-adjusted returns by combining:

- **ITSM (Inverse Tangent Signal Model)**  
- **Composite Technical Score**  
- **Volatility Targeting**

The strategy maintains a **fully invested (long-only)** portfolio within a selected equity universe.

---

## Strategy Highlights

- **Core Logic:** stock selection based on a **composite score**:  
  - 40% ITSM / Momentum  
  - 40% Composite Score  
  - 20% Gap Quality Factor  
- **Risk Management:** uses **Volatility Targeting** to keep annualized volatility near a 15% target (`Target Vol = 0.15`)  
- **Regime Filter:** applies a **Risk-On / Risk-Off** filter based on the benchmark’s long-term EMA (reduces exposure during “Risk-Off” periods)  
- **Rebalancing:** portfolio is **rebalanced weekly (every Friday)**  

---

## Performance Summary (2015-01-01 → 2025-10-10)

The backtest results show a **clear outperformance** compared to the global benchmark (MSCI ACWI).

| Metric | Portfolio | Relative to ACWI | Benchmark (ACWI) |
| :--- | :--- | :--- | :--- |
| **CAGR (Annualized Return)** | **18.45%** | +7.69% | ~10.76% |
| **Sharpe Ratio** | **1.06** | +0.12 | ~0.94 |
| **Annualized Volatility** | 17.40% | — | (higher) |
| **Max Drawdown (MaxDD)** | -28.95% | -18.14% | (deeper) |
| **Total Return** | **548.25%** | — | (lower) |

---

### Equity Curve & Relative Performance

The following chart shows the evolution of the portfolio equity versus the benchmark, highlighting the **consistency of outperformance** and the **speed of recovery** after drawdowns.

![Equity Curve and Relative Performance of the Long-Only Strategy](./Long-only.png)

---

## Strategy Mechanics — Key Calculations

The final weight of each asset (`w_t`) is determined in multiple steps:  
**signal generation → score blending → regime filtering → volatility scaling**

---

### 1. Composite Score (range 0–6)

The **Composite Score** (`Comp`) combines six technical indicators:  
ROC, EMA, MACD, RSI, MFI, and OBV.

Comp_Score = Signal_ROC + Signal_EMA + Signal_MACD + Signal_RSI + Signal_MFI + Signal_OBV

yaml
Copia codice

> The score is filtered using an `ATRP_MAX` threshold and compared to the asset’s **Relative Strength (RS)** versus the benchmark before entering the final blend.

---

### 2. Final Continuous Score

The final continuous score (`Score_Cont`) is a **weighted average** of three components:

Score_Cont = (0.4 * Blend) + (0.4 * Comp_Norm) + (0.2 * Gap_Factor)

yaml
Copia codice

---

### 3. Volatility Targeting & Exposure

The strategy scales the position weights to maintain an **annualized volatility target** of approximately **15%**.

The **Scaling Factor (`Scale`)** depends on the realized portfolio volatility (`RV`):

Scale = min(1.0, max(0.5, TARGET_VOL / RV))

swift
Copia codice

The final weight for each asset is computed as:

w_final = w_raw * Scale * Expo

yaml
Copia codice

> After scaling, weights are normalized to maintain **100% gross exposure**, with a **maximum weight per asset** (`MAX_WEIGHT = 20%`).

