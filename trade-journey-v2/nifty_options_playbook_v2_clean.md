
# NIFTY Options Trading Playbook v2 (7–8 DTE)

A structured workflow for discretionary options trading using:

- Market Structure (VWAP + EMA)
- Volatility (India VIX + IV Percentile)
- Option Positioning (PCR + OI + Max Pain)
- Spread adjustments
- Excel journaling

Goal:

Remove emotional decisions and replace them with a **mechanical trading workflow**.

---

# 1. Trading Philosophy

Professional options traders separate three components:

Direction = Market Structure  
Strategy = Volatility  
Levels = Option Positioning

When these three align → probability improves.

---

# 2. Chart Setup

Indicators:

- VWAP → Institutional bias
- EMA 20 → Momentum
- EMA 50 → Trend confirmation
- Volume → Breakout validation

Timeframes:

| Use | Timeframe |
|---|---|
Market Structure | 15m |
Entry Confirmation | 5m |

---

# 3. Volatility Regime

Use IV Percentile to decide strategy.

| IV Percentile | Market Condition | Strategy |
|---|---|---|
0–30 | Options cheap | Buy options |
30–60 | Neutral | Debit spreads |
60–100 | Options expensive | Credit spreads |

---

# 4. Expected Move

Expected Move ≈ ATM Straddle Premium

Example:

22450 CE = 120  
22450 PE = 110  

Expected Move ≈ 230 points

Range:

22335 – 22565

Avoid selling spreads outside expected movement.

---

# 5. Option Chain Analysis

Support = Highest Put OI  
Resistance = Highest Call OI  

PCR interpretation:

| PCR | Meaning |
|---|---|
<0.7 | Bearish |
0.7–1 | Neutral |
1–1.3 | Bullish |
>1.3 | Overcrowded bullish positioning |

---

# 6. Market Type Detection (First 30 Minutes)

## Trend Day

Signals:

- Opening range breakout
- Price holds above/below VWAP
- Volume expansion

Strategy:

Long options → convert to debit spread after move.

## Range Day

Signals:

- Price crosses VWAP repeatedly
- EMAs flat
- Max Pain near spot

Strategy:

Credit spreads.

## Reversal Day

Signals:

- Liquidity sweep
- Sharp rejection candle
- VWAP reclaim or breakdown

Strategy:

Debit spreads.

---

# 7. Strike Selection

Debit spreads:

Buy delta ≈ 0.50  
Sell delta ≈ 0.25–0.30  

Credit spreads:

Sell delta ≈ 0.20  
Buy delta ≈ 0.10  

---

# 8. Trade Management

Convert long option to spread when:

- price moves ~30–40 NIFTY points
- momentum slows
- market becomes sideways

Reason:

Lock profit and reduce theta risk.

---

# 9. Spread Exit Rules

Exit when:

- spread profit reaches 60–70%
- price hits resistance/support
- VWAP reversal
- momentum loss
- structure break

Golden rule:

Never wait for the last 20% of profit.

---

# 10. Excel Playbook Usage

The Excel workbook is the control system.

Market_Dashboard  
→ Check volatility and market type.

Option_Levels  
→ Identify support and resistance.

Trade_Plan  
→ Define trade before entry.

Execution_Log  
→ Record actual trade actions.

Adjustments  
→ Mechanical rules for spread conversion.

Spread_Management  
→ Spread exit logic.

Trade_Review  
→ Improve discipline.

---

# 11. DTE Rules

Preferred expiry:

| DTE | Usage |
|---|---|
7–8 days | Primary trading window |
3–5 days | Faster trades |
<2 days | Avoid unless experienced |

More time reduces theta pressure and allows adjustments.

---

# 12. Daily Workflow

1. Check VIX and IV Percentile
2. Check PCR and OI levels
3. Identify market structure
4. Calculate expected move
5. Select strategy
6. Execute trade
7. Adjust to spread
8. Exit based on rules
9. Record trade in journal

---

# 13. Golden Rule

Do not predict the market.

Follow the system.

If the rule says:

Exit

You exit.

Consistency comes from executing rules, not predicting price.
