# How to Use Trailing Stop Loss in Different Trade Types

## 1. Intraday Trades

**Purpose:**  
Lock in quick profits as price moves rapidly, while allowing room for minor pullbacks.

**Best Methods:**
- **Fixed Points/Percentage:**  
  - Set a trailing stop of 0.5–1% or a defined number of points below the highest price reached after entry.
- **VWAP/Short EMA:**  
  - Trail the stop just below the VWAP or the 9/20 EMA on a 5-min or 15-min chart.
- **Swing High/Low:**  
  - Move the stop just below the most recent minor swing low (for long) or above the swing high (for short), updating every 1–2 candles.

**How to Apply in Practice:**  
- As soon as the trade moves favorably by your initial risk (1R), move your stop to break-even.
- For strong trends, trail the stop up as new swing lows form, or as price closes above the short EMA.
- Avoid setting the stop too tight to prevent being whipsawed by normal intraday volatility.

---

## 2. Swing Trades

**Purpose:**  
Capture multi-day moves while systematically protecting profits as the trend develops.

**Best Methods:**
- **ATR-Based Trailing:**  
  - Use 1–2x ATR(14) below the highest close since entry.
- **EMA/MA Trailing:**  
  - Trail the stop under the 20 EMA or 50 EMA on the 1-hour or 4-hour chart.
- **Structure-Based:**  
  - Move the stop below the last significant swing low (for long) or swing high (for short).

**How to Apply in Practice:**  
- Start with stop below the entry candle’s low (for long).
- As price makes new highs and closes, recalculate the ATR and raise your stop accordingly.
- When price closes below your chosen EMA or forms a reversal candle, tighten the stop to just under the most recent support.
- Book partial profits at major resistance levels and let the rest trail.

---

## 3. Positional Trades

**Purpose:**  
Give the trade enough room to develop over weeks/months, while locking in gains as the trend matures.

**Best Methods:**
- **Weekly Swing Lows/Highs:**  
  - Trail the stop under the most recent weekly swing low (for long) or high (for short).
- **Long-Term Moving Averages:**  
  - Use the 50 or 100 DMA/EMA as a trailing stop reference.
- **ATR on Daily/Weekly:**  
  - Set the stop at 2x ATR(14) under the highest close since entry.

**How to Apply in Practice:**  
- Begin with a wide stop under a key support on the daily/weekly chart.
- As price advances, wait for a new weekly swing low to form, then ratchet your stop up to just below it.
- Alternatively, adjust your stop to follow the slowly rising 50 DMA/EMA.
- Consider scaling out as price hits long-term targets, but let a portion run with the trailing stop.

---

## General Tips for All Types

- **Update trailing stops at candle closes, not intrabar, to avoid noise.**
- **Never move a stop away from your entry—only towards profit.**
- **Test your trailing method on historical trades to determine optimal distance and frequency.**
- **Combine with partial profits: take some off at fixed targets, let the remainder trail.**

---

## Example Table

| Trade Type   | Trailing Method         | Calculation/Indicator           | Update Frequency   |
|--------------|------------------------|---------------------------------|--------------------|
| Intraday     | Fixed % or EMA         | 0.5–1% or 9/20 EMA (5m/15m)     | Every 5–15 min     |
| Swing        | ATR or EMA             | 1–2x ATR(14) or 20 EMA (1h/4h)  | Every 4h/daily     |
| Positional   | Weekly Swing or 50 EMA | Last weekly swing low or 50 EMA | Weekly/daily close |

---

## Excel Formula Example (for Long Trade Trailing Stop)

```excel
=MAX([Previous Trailing SL], [Current High] - [Trailing Distance])
```
Where:
- [Trailing Distance] = e.g., 1.5 * ATR(14) or Entry * 1%
- [Current High] = Highest price since entry

---

**Summary:**  
Adapt your trailing stop method to the time frame and volatility of your trade. Tighter for intraday, wider and more patient for swing/positional trades. This helps you lock in profits and participate in larger trends without getting stopped out too early.
