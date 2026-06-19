# Evaluating Systematic Momentum Strategies Against Passive Index Investing

An open-source quantitative backtesting framework built in Python to evaluate the risk-adjusted performance of trend-following and mean-reversion trading strategies against a passive buy-and-hold index control group. 
This project analyzes 20 years of historical market data across five broad, diversified ETFs to study how these strategies affect risk mitigation, capital preservation, and drawdown management.

---

## 📊 Performance Overview (20-Year Portfolio Average)

The framework backtests and benchmarks six distinct asset allocation strategies. Performance metrics are aggregated as an equally weighted portfolio across five core market indices (**SPY, QQQ, DIA, IWM, VTI**) over a 20-year timeline.

| Strategy | Total Return | Max Drawdown | Sharpe Ratio | Win Rate (%) | Exp. Return / Trade | Std. Dev. / Trade | Portfolio Trades |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **200-Day MA** | 3.47 | -24.2% | **0.627** | 52.92% | 0.0280 | 0.0900 | 302 |
| **Buy & Hold** | **7.91** | -54.9% | 0.619 | 100.00% | 7.9099 | 0.0000 | 5 |
| **Confluence (MA+RSI)** | 3.91 | -34.4% | 0.586 | **89.33%** | **0.3816** | 0.3830 | 27 |
| **50-Day MA** | 2.40 | -24.4% | 0.547 | 50.41% | 0.0202 | 0.0746 | 312 |
| **100-Day MA** | 2.03 | -26.1% | 0.504 | 48.62% | 0.0219 | 0.0830 | 301 |
| **20-Day MA** | 2.19 | -32.8% | 0.490 | 48.57% | 0.0149 | 0.0678 | 421 |

> 💡 **Core Insight:** While John Bogle’s classic passive buy-and-hold thesis remains the absolute leader in raw capital growth ($7.91\times$), it exposes investors to a brutal 54.9% max drawdown. Systematic risk management (such as the 200-Day MA with an adaptive ATR stop) successfully caps downward drawdowns to ~24.2% while yielding superior risk-adjusted efficiency (0.627 Sharpe Ratio).

---

## 🛠️ Core Algorithmic Strategies

1. **Baseline Buy & Hold:** Purely passive benchmark. Buys all five indices on Day 1 and holds them uninterrupted through all market cycles.
2. **Trend Following (Moving Averages):** Evaluates macro (200-day), intermediate (50-day, 100-day), and short-term (20-day) baselines.
3. **Noise Reduction via Percentage Buffers:** Implements an $\epsilon = 0.02$ (2%) buffer threshold to eliminate "jitter" trades during sideways, flat market trends.
4. **Adaptive Risk Management (ATR Trailing Stop):** Integrates the Average True Range (ATR) with a dynamic 4.0x volatility multiplier, allowing winning trends "room to breathe" while safely scaling exits up during volatile drawdowns.
5. **The Confluence Engine (MA + RSI):** A synthesis strategy requiring a macro uptrend (Price > 200-Day MA) aligned with a short-term oversold pullback (14-Day RSI < 30). Yields an elite 89.33% win rate.

---

## 💻 Repository Structure

```text
├── momentum_trading.ipynb       # Fully documented Python backtesting pipeline & charts
├── README.md                    # Project documentation and performance summary
└── requirements.txt             # Python dependencies

```

### Key Jupyter Notebook Implementations:

* **Continuous Equity Curve Generation:** Resolves "cash drag" evaluation errors by translating trade logs into a continuous daily time-series matrix.
* **Geometric Compounding Engine:** Fixes visualization arithmetic errors by applying cumulative vector multiplication (`cumprod`) across daily portfolio asset values.
* **Mandatory 200-Day Data Warm-Up:** Eliminates data alignment biases, guaranteeing all algorithms start trading on the exact same historical time horizon.

---

## 🚀 Getting Started & Installation

### Prerequisites

Ensure you have Python 3.8+ installed on your local machine.

### Installation & Setup

1. **Clone the repository:**
```bash
git clone https://github.com/yourusername/momentum-trading-analysis.git
cd momentum-trading-analysis

```


2. **Install the required packages:**
```bash
pip install -r requirements.txt

```


*(Alternatively, manually install dependencies: `pip install yfinance pandas numpy matplotlib`)*
3. **Run the Analysis Pipeline:**
Launch Jupyter Notebook or JupyterLab to execute the file:
```bash
jupyter notebook momentum_trading.ipynb

```


*You can also directly upload `momentum_trading.ipynb` to [Google Colab](https://colab.research.google.com/) to execute the code in a cloud environment without any local setup.*

---

## ⚠️ Framework Limitations

Before using this framework in live market allocations, note the following built-in backtesting assumptions:

* **Frictionless Trading:** The engine assumes execution zero slip, zero brokerage commissions, and zero regulatory fees. High-churn rules (e.g., 20-Day MA) will experience real-world return erosion.
* **Tax Drag Blindspot:** Active capital reallocation triggers recurring short-term capital gains taxes, unlike the deferred compounding advantages of passive holding.
* **Long-Only Optimization:** The current framework cannot short-sell assets. Bear market strategies are limited to moving capital entirely into a 0% yield cash position.

---

## ✒️ Author

* **Tejas Manojav Patil** – [tejas.manojav@gmail.com](https://www.google.com/search?q=mailto%3Atejas.manojav%40gmail.com)

---
