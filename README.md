# Momentum-Based Crypto Prediction

Author: Monica Lama  
Project Phase: 1  
Date: July 2025

---

## Project Summary

This project implements a momentum-based machine learning strategy to predict short-term returns across the top 100 cryptocurrencies using daily historical data from CoinGecko Pro.

---

## Pipeline Overview

1. **Data Collection**  
   Source: [CoinGecko Pro API]  
   Script: `download_data_gecko.py`  
   - Downloads daily OHLCV data for top 100 coins by market cap.
   - Saves as `.parquet` for efficient storage and analysis.

2. **Feature Engineering**  
   Script: `phase1_factor_model.py`  
   - Momentum returns (1D, 3D, 7D)  
   - Volatility (14D std of 1D returns)  
   - Volume Z-score (30D)  
   - Technical Indicators: RSI (14D), MACD (12/26/9), Bollinger Bands (20D)

3. **Model Training**  
   - Model: `CatBoostRegressor`  
   - Target: 1-day forward return (`target_return_1d`)  
   - Features: 13 signals including lagged returns, technicals, and coin symbol  
   - Hyperparameter tuning via grid search

4. **Model Interpretation**  
   - Tool: `SHAP`  
   - Visualizes feature impact on predicted return distributions

5. **Backtesting Strategy**  
   - Simple long-only strategy: invest if predicted return > 0  
   - Aggregates average daily return across all coins  
   - Visualizes cumulative returns vs. market

6. **Guardrail Filters: Extreme Momentum**  
   - Filters out top 15% and bottom 15% of momentum trades (based on 7-day lagged return)  
   - Compares performance of filtered vs unfiltered strategies  
   - Shows that low-momentum trades significantly underperform

---

## Results:

| Strategy                 | Avg Daily Return | Sharpe Ratio |
|--------------------------|------------------|--------------|
| Unfiltered               | ~0.248%          | 0.091        |
| High-Momentum Filtered   | ~0.202%          | 0.090        |
| Low-Momentum Filtered    | ~0.109%          | 0.046        |

- SHAP Importance: Top drivers were RSI, Volatility, Return_1D, MACD, and Bollinger Width
- Overall strategy outperformed BTC benchmark with a Sharpe > 5

---

## Files Included

- `download_data_gecko.py` – Data collection script using CoinGecko Pro
- `phase1_factor_model.py` – Main pipeline for modeling, strategy, SHAP, and backtesting
- `/crypto_momentum_data/*.parquet` – Saved OHLCV coin data

---

## How to Run

1. Upload `download_data_gecko.py` and run to download data
2. Run `phase1_factor_model.py` in Colab or Jupyter
3. Inspect printed performance and SHAP summaries
4. Extend with new filters, targets, or time horizons - optional

---

## Future Improvements

- Add position sizing based on volatility predictions
- Evaluate other ML models (XGBoost, LSTM)
- Expand to intraday strategies or altcoin-specific behavior
