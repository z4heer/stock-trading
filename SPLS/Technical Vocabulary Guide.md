# 📘 Technical Vocabulary Guide

(For NIFTY_Master_Control_System_v5)

Reference Instrument: NIFTY 50

---

# 🔹 1️⃣ Market State Vocabulary

These terms classify *market behavior*, not prediction.

## 🟢 Expansion

A volatility expansion phase where:

* ATR > recent average
* Wide candles
* VWAP clearly sloping
* Structure making clean higher highs or lower highs

Meaning:
Momentum is dominant.
Long options benefit from gamma expansion.

System Action:
Hold naked or allow pyramid.

---

## 🟡 Pullback

A corrective move inside an existing trend:

* Multi-candle retracement
* Moves toward 20 EMA / VWAP
* No swing structure break

Meaning:
Trend digestion, not reversal.

System Action:
Wait → Hedge only after rejection.

---

## 🔵 Compression

Volatility contraction phase:

* Overlapping candles
* Flat VWAP
* ATR below average
* Breakouts failing

Meaning:
Theta and IV decay dominate.

System Action:
Avoid naked longs. Prefer spreads or reduce.

---

## 🔴 Transition

Structural shift:

* Previous swing broken
* VWAP reclaim with follow-through
* Opposite impulse leg

Meaning:
Trend invalid.

System Action:
Exit full.

---

# 🔹 2️⃣ Volatility Vocabulary

## ATR Ratio

Current ATR ÷ 10-day average ATR.

Measures volatility expansion or contraction.

> > 1.2 → Expansion environment
> > <0.9 → Compression environment

---

## VWAP Distance %

% distance of price from VWAP.

Measures institutional positioning bias.

Large distance + slope = momentum control.

---

## IV Regime

Based on:

* India VIX
* IV Percentile

### High IV

Options expensive.
Prefer spreads.

### Low IV

Options cheap.
Prefer naked longs.

### Normal IV

Structure decides.

---

# 🔹 3️⃣ Structure Vocabulary

## Swing Score

Quantified directional structure:

* Lower high = -1
* Higher high = +1

Total over last 3 swings gives structural bias.

---

## Structure Intact

Trend valid if:

* No opposite swing break
* VWAP not decisively flipped

This prevents emotional exits.

---

# 🔹 4️⃣ Position Lifecycle Vocabulary

## Initiation Phase

First impulse after state turns Expansion.

No hedge. Let gamma expand.

---

## Expansion Phase

Momentum continuation.

Allow profit to grow. Avoid early hedging.

---

## Pullback Phase

Controlled retracement.

Hedge only after confirmed rejection.

---

## Compression Phase

Volatility contraction.

Reduce naked exposure.

---

## Termination Phase

Trend invalidated.

Exit fully.

---

# 🔹 5️⃣ Scaling & Rolling Vocabulary

## Scaling (Pyramiding)

Adding size during early expansion.

Allowed only when:

* Profit present
* Volatility expanding
* Not near expiry

---

## Rolling Short Leg

Adjusting hedge strike lower (for puts) after new impulse leg.

Purpose:

* Lock partial gains
* Maintain directional exposure

Not done during compression or near expiry.

---

# 🔹 6️⃣ Risk & Behavioral Vocabulary

## Discipline Score

Self-assessment score (0–5) based on mental readiness.

Below threshold → No trade or reduce size.

---

## Override

When trader action ≠ system suggestion.

System flags:

* Violation
* Severity (Medium / High)

This protects against emotional interference.

---

## Lifecycle Integrity

Following state-based action without deviation.

This matters more than single-trade P&L.

---

# 🔹 7️⃣ DTE (Days to Expiry) Logic

## Short DTE (≤15 days)

High gamma.
Sensitive to pullbacks.

## Mid DTE (20–45 days)

Balanced gamma + vega.
Best for structured swing.

## Low DTE (<7 days)

High gamma risk.
No aggressive rolling.

---

# 🔹 8️⃣ Control Board Vocabulary

## Master Dashboard

Central decision engine combining:

* Market State
* IV Regime
* Lifecycle Action
* Scaling Suggestion
* Rolling Suggestion
* Discipline Status

It is not predictive.
It is responsive.

---

# 🧠 Conceptual Philosophy

This system does NOT:

* Predict direction
* Guarantee profit
* Eliminate loss

It does:

* Classify behavior
* Standardize response
* Reduce emotional variance
* Prevent self-sabotage

---

# 🎯 Core Principle

You are not trading signals.

You are managing **state transitions**.

State → Response
Not prediction → hope.

---

If you’d like, I can also write:

* A short 1-page “Executive Summary” version
  or
* A psychological framework summary explaining how this system fits your personality

Which one would you prefer?
