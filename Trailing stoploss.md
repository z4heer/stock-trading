# Trailing Stop Loss: Detailed Guide

## What is a Trailing Stop Loss?

A trailing stop loss is a dynamic risk management tool that automatically adjusts your stop loss level as the trade moves in your favor. Unlike a fixed stop loss, the trailing stop "trails" the market price at a set distance, locking in profits while allowing room for the trend to continue.

---

## When and Why to Use a Trailing Stop

- **Purpose:** Protect profits and let winners run.
- **Best For:** Trending markets, swing/positional trades, and momentum strategies.
- **Not Ideal For:** Highly volatile, choppy markets where frequent stop-outs might occur.

---

## How Trailing Stop Loss Works

- **Long Position:** The stop loss moves up as the price rises, but never moves down.
- **Short Position:** The stop loss moves down as the price falls, but never moves up.
- **Trigger:** When price reverses by a pre-defined amount (points, percentage, ATR, etc.), the trailing stop is hit, and the position is closed.

---

## Trailing Stop Techniques

### 1. Fixed Points or Percentage
- **Example:** Set trailing stop at 1% or 20 points below the highest price since entry.
- **How:** If price moves from 100 to 120, the stop trails up from 99 to 119.

### 2. ATR-Based Trailing Stop
- **Example:** Set trailing stop at 1x or 2x the Average True Range (ATR) below the highest price for long trades.
- **How:** ATR adjusts for volatility, keeping the stop at a logical distance.

### 3. Moving Average Trailing Stop
- **Example:** Stop loss trails below a short-term moving average (e.g., 9 EMA).
- **How:** As long as price stays above the MA, stay in the trade.

### 4. Swing High/Low Trailing
- **Example:** Trail the stop below/above the most recent swing low/high on the chart.
- **How:** Updates after each new swing is formed.

---

## Entry/Exit Signals Using Trailing Stops

- **Entry:** As per your main strategy (breakout, pullback, etc.).
- **Initial Stop:** Place the stop at logical structure (recent swing low/high, or fixed percent).
- **Trailing Signal:** Move the stop as per your chosen method when price moves in favor.
- **Exit:** Position is closed automatically when price hits the trailing stop.

---

## Indicators for Trailing Stops

- **ATR (Average True Range)**
- **Moving Averages (EMA/SMA)**
- **Parabolic SAR**
- **Manual (visual) adjustment based on swing points**

---

## Best Practices for Trailing Stops

- **Don’t trail too tight:** Allow room for normal price fluctuations, or you’ll get stopped out prematurely.
- **Adjust frequency:** Move your stop only after significant moves or at candle closes, not tick-by-tick.
- **Combine with partial exits:** Book partial profits at set RR, then let the rest trail.
- **Backtest:** Test different trailing methods on your strategy and market.
- **Discipline:** Never move stop loss further away from entry—only trail towards profit.

---

## Example Table for Excel Dashboard

| Trade | Entry Price | Highest Price | Trailing Stop Type | Distance (pts/%) | Current Trailing SL | Status  |
|-------|-------------|---------------|--------------------|------------------|---------------------|---------|
| INFY  | 1500        | 1540          | ATR (1.5x)         | 45               | 1495                | Open    |
| TCS   | 3600        | 3650          | Fixed (1%)         | 36               | 3614                | Open    |
| SBIN  | 600         | 630           | 20 EMA             | N/A              | 612 (20 EMA)        | Open    |

---

## Quick Formula (Example: Excel for Long Trade)

```
=MAX([Previous Trailing SL], [Current High] - [Trailing Distance])
```
Where:
- [Trailing Distance] = e.g. 1.5 * ATR(14) or Entry * 1%
- [Current High] = Highest price since entry

---

## Summary

Trailing stops are a powerful way to lock in profits automatically, ride trends, and reduce emotional decision-making. Choose the method and distance that fits your style and market, and always stick to your trailing rules for consistent results.
