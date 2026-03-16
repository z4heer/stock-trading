Below is a **single consolidated Markdown manual** combining everything we built:

* PSA chart workflow
* Market dashboard logic
* Trade plan workflow
* Spread management
* Liquidity sweep playbook
* Risk rules

You can save it as:

```text
PSA_options_trading_manual.md
```

---

# PSA Options Trading Support Manual

A structured execution manual for **price-structure-based options trading** using:

* PSA chart structure
* option positioning
* controlled risk
* simple spread management

The objective is **consistent execution**, not prediction.

---

# 1. System Philosophy

The system prioritizes:

```
structure
liquidity
volatility
risk control
```

Over:

```
indicators
predictions
opinions
```

Trades must be **planned before execution**.

---

# 2. Complete Workflow

Every trading day must follow the same sequence.

```
Market Dashboard
↓
Chart Analysis
↓
Trade Plan
↓
Execution
↓
Trade Journal
```

Skipping steps increases emotional trading.

---

# 3. Market Dashboard (Pre-Market)

This step defines **market context**.

Fill these values before market open.

| Parameter        | Source                 |
| ---------------- | ---------------------- |
| Spot Price       | Index level            |
| India VIX        | Volatility index       |
| PCR              | Option chain           |
| Max Pain         | Option chain           |
| ATM Call Premium | Option chain           |
| ATM Put Premium  | Option chain           |
| Call OI Wall     | Highest call OI strike |
| Put OI Wall      | Highest put OI strike  |

---

## ATM Straddle

```
ATM Straddle = ATM Call + ATM Put
```

Example

```
Call = 120
Put = 110
Straddle = 230
```

---

## Expected Move

```
Expected Move = ATM Straddle
```

---

## Expected Range

```
Upper Range = Spot + Expected Move
Lower Range = Spot - Expected Move
```

---

## Market Bias

Based on PCR.

| PCR | Interpretation |
| --- | -------------- |

> 1.2 | Bullish |
> 0.7–1.2 | Neutral |
> <0.7 | Bearish |

---

## Market Type

Based on VIX.

| VIX   | Market |
| ----- | ------ |
| <14   | Range  |
| 14–16 | Normal |

> 16 | Volatile |

---

# 4. PSA Chart Layout

The chart must contain **five layers**.

| Layer              | Purpose          |
| ------------------ | ---------------- |
| Market Structure   | VWAP + EMA       |
| Volatility Context | Expected move    |
| Positioning        | OI levels        |
| Entry Trigger      | Liquidity sweeps |
| Trade Management   | Spread zones     |

---

## Indicators

Use minimal indicators.

| Indicator | Purpose               |
| --------- | --------------------- |
| VWAP      | Institutional bias    |
| EMA 20    | Momentum              |
| EMA 50    | Trend                 |
| Volume    | Breakout confirmation |

Avoid indicator clutter.

---

# 5. Chart Visual Template

```
Expected Move High
Call OI Resistance

Price
VWAP
EMA20
EMA50

Put OI Support
Expected Move Low
```

This allows instant answers:

```
Where is price?
Where is liquidity?
Where is target?
```

---

# 6. Morning Levels to Mark

Before trading mark:

### Expected Move Range

Derived from ATM straddle.

Example

```
22335 – 22565
```

---

### OI Levels

| Level           | Meaning    |
| --------------- | ---------- |
| Highest Put OI  | Support    |
| Highest Call OI | Resistance |

---

### Liquidity Levels

Mark:

* Previous Day High
* Previous Day Low
* Swing Highs
* Swing Lows

These areas contain **stop clusters**.

---

# 7. Market Phase Identification

| Phase       | Characteristics          |
| ----------- | ------------------------ |
| Expansion   | Strong candles, momentum |
| Pullback    | Small retracement        |
| Compression | Tight range              |
| Transition  | Reversal behaviour       |

---

# 8. Trade Setup Types

| Setup                    | Description              |
| ------------------------ | ------------------------ |
| Liquidity Sweep Breakout | Stop run then reversal   |
| Pullback Continuation    | EMA20 retrace            |
| Range Reversal           | Range boundary rejection |
| Trend Continuation       | VWAP trend continuation  |

---

# 9. Trade Plan Procedure

Fill the trade plan **before entering a trade**.

---

## Step 1 – Market Context

Fill:

```
Market Phase
Bias
Setup Type
```

---

## Step 2 – Chart Levels

Fill:

```
Entry Level
Stop Level
Target Level
```

Example

```
Entry = 22450
Stop = 22380
Target = 22600
```

---

## Step 3 – Option Translation

Convert the index trade to options.

```
Option Entry Price
Option Stop Price
```

Example

```
Entry = 120
Stop = 95
```

---

## Step 4 – Risk Control

Define fixed risk.

```
Risk per Trade = ₹2000
```

Position size is calculated automatically.

---

## Step 5 – Trade Management

Plan trade outcome.

```
Planned Spread
EOD Decision Plan
```

Example

```
Buy 22450 CE
Sell 22600 CE
```

---

# 10. Spread Management Framework

The system uses **only one spread per trade**.

Purpose:

* reduce risk
* protect profits
* simplify execution

Avoid complex structures.

---

# 11. Allowed Spread Types

Only two spreads are used.

---

## Bull Call Spread

Used in bullish setups.

Structure

```
Buy ATM Call
Sell OTM Call
```

Example

```
Buy 22450 CE
Sell 22600 CE
```

---

## Bear Put Spread

Used in bearish setups.

Structure

```
Buy ATM Put
Sell OTM Put
```

Example

```
Buy 22450 PE
Sell 22300 PE
```

---

# 12. Spread Strike Selection

Choose second strike based on:

1. OI resistance/support
2. Expected move boundary
3. Liquidity level

Example

```
Entry = 22450
Resistance = 22600
Spread = 22450 / 22600
```

---

# 13. Spread Width Rule

Determine width using ATM straddle.

| Volatility | Spread Width |
| ---------- | ------------ |
| Low        | 100–150      |
| Normal     | 150–200      |
| High       | 200–300      |

Simple rule:

```
Spread width ≈ 60% of ATM straddle
```

---

# 14. Spread Conversion Timing

| Target Progress | Action            |
| --------------- | ----------------- |
| 0–50%           | Hold naked option |
| 50–70%          | Prepare spread    |
| 70–90%          | Convert to spread |
| Near target     | Exit              |

---

# 15. When NOT to Convert Spread

Do not convert during **strong expansion**.

Signs:

* strong candles
* rising volume
* price far from VWAP

In this phase:

```
Hold naked option
```

---

# 16. Spread Exit Rules

Exit spread when:

1. price hits spread short strike
2. strong reversal candle appears
3. end of trading day

---

# 17. One Spread Rule

The system allows:

```
one trade
one spread
one exit
```

Avoid:

* multiple spreads
* rolling strikes
* complex adjustments

---

# 18. Liquidity Sweep Playbook

These patterns move the market most often.

---

## Pattern 1 – Previous Day Sweep

Structure

```
Prev High sweep
VWAP reclaim
```

Trade

```
Buy call
```

Target

```
Next resistance
```

---

## Pattern 2 – Range Sweep

Structure

```
Range breakout
Fast rejection
```

Trade

```
Range reversal trade
```

---

## Pattern 3 – VWAP Liquidity Sweep

Structure

```
VWAP break
Immediate reclaim
```

Trade

```
Trend continuation
```

---

## Pattern 4 – OI Wall Sweep

Structure

```
Call OI break
False breakout
```

Trade

```
Short covering rally
```

---

## Pattern 5 – Double Liquidity Sweep

Structure

```
Low sweep
High sweep
VWAP reclaim
```

Trade

```
Strong directional move
```

---

# 19. Execution Rule (Prop Trader Rule)

Every trade must answer three questions.

```
Where is liquidity?
Where is invalidation?
Where is target?
```

If unclear:

```
No trade
```

---

# 20. Risk Management Rules

### Fixed Risk

```
1–2% capital per trade
```

---

### Maximum Daily Loss

```
Stop after 2 losing trades
```

---

### Avoid Overtrading

Trade only when:

```
liquidity sweep + structure alignment
```

---

# 21. Trade Journal

After trade completion record:

* entry
* exit
* option prices
* P&L
* mistake
* lesson

Purpose:

```
pattern recognition
```

---

# 22. Final System Objective

This framework ensures:

```
clear market context
structured trade planning
defined risk
systematic review
```

The goal is **consistent execution over time**.

---

If you'd like, I can also create a **clean printable 1-page “Daily Trading Checklist”** from this manual (something traders keep beside their screen while trading).
