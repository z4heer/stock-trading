# NIFTY Options System — Backtesting Manual

This document explains **how to use `nifty_backtesting_worksheet.xlsx`** for manual backtesting using historical NIFTY charts.

The goal is to train pattern recognition for:

* Market Phases
* Liquidity Sweeps
* Trade Planning
* Risk Management

Backtesting should be completed **before trading real money**.

---

# 1. Required Tools

Use a charting platform such as:

* TradingView (recommended)
* Broker charting tools

Chart configuration:

| Setting           | Value       |
| ----------------- | ----------- |
| Instrument        | NIFTY Index |
| Primary timeframe | 15 minutes  |
| Entry timeframe   | 5 minutes   |

Indicators:

| Indicator | Purpose      |
| --------- | ------------ |
| VWAP      | Market bias  |
| EMA 20    | Momentum     |
| EMA 50    | Trend        |
| Volume    | Confirmation |

---

# 2. Daily Backtesting Workflow

Each backtested day follows the same process.

```
Select historical day
↓
Mark key levels
↓
Identify market phase
↓
Check liquidity sweep
↓
Create trade plan
↓
Simulate trade
↓
Record result in worksheet
```

---

# 3. Mark Key Price Levels

Before evaluating trades mark:

| Level             | Purpose            |
| ----------------- | ------------------ |
| Previous Day High | Breakout liquidity |
| Previous Day Low  | Stop liquidity     |
| Recent Swing High | Liquidity cluster  |
| Recent Swing Low  | Liquidity cluster  |

These zones are where **liquidity sweeps often occur**.

---

# 4. Identify Market Phase

Look at price behavior around **9:45 AM**.

Choose one phase.

| Phase       | Characteristics              |
| ----------- | ---------------------------- |
| Expansion   | Strong directional candles   |
| Pullback    | Retracement toward EMA20     |
| Compression | Small candles, tight range   |
| Transition  | Reversal signs or VWAP break |

Record in worksheet:

```
Market Phase = Pullback
```

---

# 5. Detect Liquidity Sweep

Check whether price **briefly breaks a key level then reverses**.

Example support sweep:

```
Support = 22400
Price dips to 22390
Price returns above 22420
```

Example resistance sweep:

```
Resistance = 22500
Price spikes to 22520
Price falls to 22470
```

Record setup:

```
Setup = Support Sweep
```

---

# 6. Determine Bias

Use VWAP as directional reference.

| Condition             | Bias    |
| --------------------- | ------- |
| Price above VWAP      | Bullish |
| Price below VWAP      | Bearish |
| Frequent VWAP crosses | Neutral |

Example:

```
Bias = Bullish
```

---

# 7. Create Trade Plan

Fill these fields in **Backtest_Log** sheet.

| Field        | Example       |
| ------------ | ------------- |
| Market Phase | Pullback      |
| Setup        | Support Sweep |
| Entry        | 22460         |
| Stop         | 22410         |
| Target       | 22580         |

The trade plan must be written **before revealing future candles**.

---

# 8. Simulate the Trade

Move the chart forward candle by candle.

Monitor:

| Condition                 | Action                 |
| ------------------------- | ---------------------- |
| Price moves +30–40 points | Lock profits or spread |
| Momentum weakens          | Exit                   |
| Target level hit          | Exit                   |

Record final result.

---

# 9. Record Trade Result

In the worksheet fill:

| Field      | Example |
| ---------- | ------- |
| Result     | Win     |
| R Multiple | +2R     |

Example:

```
Entry = 22460
Stop = 22410
Risk = 50 points
Target hit = +100 points

Result = +2R
```

---

# 10. End-of-Day Evaluation

At the **3:10 PM candle**, classify market again.

| Phase       | Typical Decision |
| ----------- | ---------------- |
| Expansion   | Hold overnight   |
| Pullback    | Partial hold     |
| Compression | Exit             |
| Transition  | Exit             |

Write notes in worksheet.

---

# 11. Performance Review

The **Performance_Summary** sheet calculates:

* Total Trades
* Win Rate
* Total R
* Average R per Trade

A profitable system typically shows:

```
Average R > 0
```

Example performance:

| Trades | Win Rate | Avg R |
| ------ | -------- | ----- |
| 100    | 48%      | +0.4R |

---

# 12. Recommended Backtesting Goal

Minimum practice target:

```
50–100 simulated trades
```

This builds recognition for:

* Market phases
* Liquidity sweeps
* Entry timing
* Exit discipline

---

# 13. Important Backtesting Rules

1. Do not reveal future candles early.
2. Always write trade plan before moving chart forward.
3. Use the same process for every trade.
4. Focus on **consistency**, not single trade results.

---

# 14. Completion Criteria

You are ready to start paper trading when:

* Market phase recognition becomes quick.
* Liquidity sweeps are easy to identify.
* Trade planning feels mechanical.
* Backtest results show positive expectancy.

---

# 15. Next Step After Backtesting

```
Backtesting
↓
Paper trading
↓
Small capital trading
↓
Scale gradually
```

Never skip the first two steps.
