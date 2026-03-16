# 1️⃣ Chart Layout Structure

Your chart should contain **five visual layers**:

| Layer              | Purpose             |
| ------------------ | ------------------- |
| Market Structure   | VWAP + EMA          |
| Volatility Context | Expected Move       |
| Positioning        | OI levels           |
| Entry Trigger      | Liquidity sweeps    |
| Trade Management   | spread / exit zones |

Think of the chart like this:

```text
Expected Move High
Call OI Resistance
------------------------------

Price Action
(VWAP + EMA)

------------------------------
Put OI Support
Expected Move Low
```

This view instantly tells you:

* where price is
* where liquidity is
* where spreads should target

---

# 2️⃣ Indicators to Add

Use only **minimal indicators**.

| Indicator | Purpose               |
| --------- | --------------------- |
| VWAP      | institutional bias    |
| EMA 20    | momentum              |
| EMA 50    | trend                 |
| Volume    | breakout confirmation |

Avoid clutter.

Rule:

```text
Less indicators → better decision clarity
```

---

# 3️⃣ Mark These Levels Every Morning

Before trading, draw these lines.

### Expected Move

From ATM straddle.

Example:

```text
ATM CE = 120
ATM PE = 110
Expected Move = 230
```

Range:

```text
22335 – 22565
```

---

### OI Levels

From option chain.

| Level           | Meaning    |
| --------------- | ---------- |
| Highest Put OI  | Support    |
| Highest Call OI | Resistance |

Example:

```text
22300 support
22600 resistance
```

---

# 4️⃣ Mark Liquidity Zones

Mark these areas:

| Level             | Reason             |
| ----------------- | ------------------ |
| Previous day high | breakout liquidity |
| Previous day low  | stop liquidity     |
| Swing highs       | stop clusters      |
| Swing lows        | stop clusters      |

Example:

```text
Prev High = 22510
Prev Low = 22380
```

These are **sweep zones**.

---

# 5️⃣ Identify Market Phase on Chart

Use price behaviour.

### Expansion

Signs:

* strong candles
* price far from VWAP
* volume increase

Action:

```text
Hold naked option
```

---

### Pullback

Signs:

* price returns to EMA20
* small candles

Action:

```text
Hold trade
```

Possible **liquidity sweep**.

---

### Compression

Signs:

* tight range
* flat EMAs
* VWAP cross

Action:

```text
Convert to spread
```

---

### Transition

Signs:

* VWAP break
* strong reversal candle
* resistance sweep

Action:

```text
Exit or reverse trade
```

---

# 6️⃣ Example Full Chart Scenario

Morning:

```text
NIFTY = 22450
```

Levels:

```text
Support = 22380
Resistance = 22600
Expected Move High = 22565
```

Price action:

```text
22450 → 22410
Support sweep
VWAP reclaim
```

Trade:

```text
Buy 22450 CE
```

Plan already written in Excel:

```text
Spread plan = 22450 / 22600
```

---

# 7️⃣ Visual Spread Zone

Always mark **spread target zone**.

Example:

```text
Entry = 22450
Target zone = 22580–22600
```

Spread:

```text
Buy 22450 CE
Sell 22600 CE
```

---

# 8️⃣ End-of-Day Chart Check

Before closing the terminal, check:

```text
What phase is the market in?
```

Decision:

| Phase       | Action     |
| ----------- | ---------- |
| Expansion   | hold       |
| Pullback    | hold small |
| Compression | exit       |
| Transition  | exit       |

---

# 9️⃣ Final Chart Template

Your final chart should visually look like this:

```text
Expected Move High
Call OI Resistance
Prev High Liquidity

Price
VWAP
EMA20
EMA50

Prev Low Liquidity
Put OI Support
Expected Move Low
```

When you see this layout you can instantly answer:

```text
Where is price?
Where is liquidity?
Where is the target?
```

---

# 🔟 How This Helps Your System

This template makes the playbook **very easy to execute**.

You quickly see:

* liquidity sweeps
* market phase
* spread zones
* exit levels

Which reduces:

* emotional trading
* overtrading
* confusion
