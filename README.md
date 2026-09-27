# Can Trading Strategies Beat Buy & Hold?

John Bogle's buy-and-hold idea (own the whole market and don't touch it) makes intuitive sense to me. But I kept hearing about simple trading strategies that are supposed to beat the market, or at least avoid the big crashes. So I backtested them.

This notebook tests moving-average, RSI, and my own "Confluence" strategy on 5 index ETFs (**SPY, QQQ, DIA, IWM, VTI**) from 2004 to 2024, and compares everything to just buying and holding.

**Short answer:** No strategy beat buy & hold on total return, on any of the 5 ETFs. What the strategies do give you is smaller crashes, and you pay for that with a lot less money at the end.

---

## Results (mean across the 5 ETFs, 2004–2024; QQQ pulls the averages up—see the per-ETF table in Section 3)

| Strategy | Total Return | Max Drawdown | Sharpe Ratio | Win Rate | Avg Return / Trade | Trades (all 5 ETFs) |
| --- | --- | --- | --- | --- | --- | --- |
| **Buy & Hold** | **791%** | -54.9% | 0.62 | — | — | 5 |
| **Confluence (200-day MA + RSI)** | 548% | -34.3% | **0.66** | 87.6% (only 32 trades) | 37.1% | 32 |
| **200-Day MA** | 354% | **-23.8%** | 0.64 | 52.5% | 2.9% | 307 |
| **50-Day MA** | 253% | -24.4% | 0.56 | 51.2% | 2.2% | 315 |
| **100-Day MA** | 225% | -26.0% | 0.53 | 51.0% | 2.3% | 305 |
| **RSI (30/70)** | 215% | -45.0% | 0.43 | 81.3% | 2.5% | 270 |
| **20-Day MA** | 199% | -31.7% | 0.48 | 49.5% | 1.5% | 422 |

*All strategies start on the same day (after a 200-day warm-up), trade at the next day's close, and use compounded daily returns. Each MA strategy uses a 2% buffer and a 5% trailing stop. Sharpe uses a 0% risk-free rate.*

## What I found

- **Nothing beats buy & hold on return.** That's true on every ETF. QQQ had the biggest returns for every strategy except RSI (which did best on IWM) and pulls the 5-ETF averages up. IWM (small caps) was usually the smallest.
- **The strategies do cut the crashes.** The 200-day MA's worst drop was about -24%, vs. -55% for buy & hold. But it ended with less than half the money.
- **Risk-adjusted, it's close to a tie.** Confluence and the 200-day MA have slightly higher Sharpe ratios than buy & hold (0.66 and 0.64 vs. 0.62), but not by much.
- **More trading = worse.** The 20-day MA trades the most and does the worst. A tight ATR stop (2x ATR, only ~2.7% below the price) made 4x as many trades as the fixed 5% stop and lost to it.
- **A high win rate doesn't mean high returns.** RSI wins 81% of its trades (and Confluence wins 87.6%, though across only 32 trades in 20 years), yet both made less than buy & hold. RSI sells too early and misses the big runs.
- **My Confluence strategy mostly turned into buy & hold.** Its sell rule hardly ever fires (~6 trades per ETF in 20 years). It was out of the market in 2008, which helped. But it rode the COVID crash down, and it sat in cash for part of 2022–23 and missed the start of the rebound.
- **Combining trend + RSI mostly helped.** Confluence beat the 200-day MA on all 5 ETFs, and beat plain RSI on 4 of 5. The exception was IWM, where plain RSI did better (344% vs. 281%).

## Did I just get lucky? (robustness checks)

**Resampling trades.** For each strategy, I randomly re-picked trades from its own list of trades (repeats allowed) and re-compounded, 2,000 times:

| Strategy | Actual | Resampled Median | Beats Buy & Hold (% of runs) |
| --- | --- | --- | --- |
| Confluence | 548% (only 32 trades) | 622% | 30% |
| 200-Day MA | 354% | 423% | 8% |
| All others | 190–234% | 230–273% | < 1% |

*Why the resampled median is higher than "Actual" for every strategy: each run resamples trades within each ETF separately, compounds each ETF, and averages across the 5 ETFs before taking the median. A single ETF's resampled median is right around its actual return, but because compounded returns are right-skewed (a lucky streak on any ETF—especially QQQ—blows that ETF's return up while losses can't pass -100%), averaging 5 ETFs in every run pulls the portfolio median up toward the mean. (Also, for "All others", "Actual" here is the trade-by-trade return, which includes trades during the first 200 warm-up days that the main table skips; see Appendix B.)*

Confluence depends the most on luck: it has only 32 trades, so which trades you get matters a lot. Its results probably beat the 200-day MA's, but not definitely. Nothing reliably beats buy & hold.

**Other settings (Appendix A).** I started with the standard settings and tried a few nearby values by hand. Then I tested lots of values properly: stop type and size, buffer size, and Confluence's MA length and RSI levels.

- **The main story holds for every setting I tried.** Nothing beats buy & hold on return, and the MA strategies cut the crashes.
- **Wider stops earn a bit more.** The 200-day MA made 426% with a 10% stop, but that's still far below buy & hold. The stop barely changes the drawdown; the MA line is what gets you out of crashes.
- **Confluence only works with a long MA.** With RSI 30/70: 200-day MA 548%, 150-day 498%, 100-day 236%, and a 50-day MA just 4% (1 trade in 20 years). With a short MA, a dip big enough to push RSI under 30 usually also pushes price under the MA, so the buy rule almost never fires.

**Conclusion:** When I start investing, I'll go with buy & hold. If I couldn't handle a -55% drop, the 200-day MA is the one I'd consider. But I'd have to accept ending up with a lot less money.

---

## Strategies tested

1. **Buy & Hold:** buy each ETF on day 1 and never sell. This is the benchmark.
2. **Moving average (20 / 50 / 100 / 200-day):** buy when price is above the MA + 2%, sell when price is below the MA − 2% or falls 5% from its high since I bought (trailing stop). The 2% buffer stops tiny wiggles from triggering trades.
3. **ATR stop:** same MA rules, but the stop is a multiple of the Average True Range (how jumpy the price is) instead of a fixed 5%. I tested 2x to 5x ATR.
4. **RSI (30/70):** buy when the 14-day RSI drops below 30 (oversold), sell when it goes above 70 (overbought).
5. **Confluence (my strategy):** buy when price > 200-day MA + 2% **and** RSI < 30, i.e. buy dips only in an uptrend. Sell when price < 200-day MA − 2% **and** RSI > 70. It checks *states*: both conditions have to be true on the same day, not just the day a line is crossed.

## Notebook layout

1. Data + settings
2. The strategies
3. Main results (table, per-ETF results, portfolio chart)
4. Deep dives: stop-loss type, RSI settings, when Confluence buys, each ETF separately
5. Real edge or just luck? (trade resampling)
6. Conclusion
- Appendix A: Do other settings change the story? (settings sweeps)
- Appendix B: Sanity checks

## Repository

```text
├── momentum_trading.ipynb   # The full analysis: code, charts, and notes
└── README.md
```

## How to run

The easiest way is to upload `momentum_trading.ipynb` to [Google Colab](https://colab.research.google.com/) and choose **Runtime → Run all**. Appendix A takes a few minutes.

To run it locally (Python 3.9+):

```bash
git clone https://github.com/tejasmanojav/momentum_trading.git
cd momentum_trading
pip install yfinance pandas numpy matplotlib jupyter
jupyter notebook momentum_trading.ipynb
```

*Note: prices are downloaded from Yahoo Finance each time, and the data changes very slightly between downloads, so some numbers can move by a few percent from run to run.*

---

## Limitations

- **No trading costs or taxes.** Real trading has commissions, bid-ask spreads, and short-term capital gains taxes. These would hurt the high-trading strategies (like the 20-day MA) the most, and make the gap to buy & hold even bigger.
- **Long-only.** When a strategy sells, the money sits in cash earning 0%. There is no short selling, and no interest on cash.
- **One time period, 5 related ETFs.** 2004–2024 was mostly a strong bull market for US stocks. The results could look different in other periods or markets.
- **Settings picked with hindsight.** I started with standard settings and checked nearby values. Appendix A shows the main story doesn't depend on them, but Confluence does depend on using a long MA.

## Versions

- **v1 (spring 2026):** original analysis.
- **v2 (Sept 2026):** AI-assisted debugging and editing. Fixes changed some numbers, not the conclusion. See the report for details.

## Author

**Tejas Manojav Patil** – [@tejasmanojav](https://github.com/tejasmanojav)
