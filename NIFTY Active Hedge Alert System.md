# Strategy Name

**NIFTY Active Hedge Alert System v1**

---

# PART 1: Strategy Objective

Generate alert when market turns weak so you can:

• Buy NIFTY PUT option
• Protect equity portfolio
• Exit hedge when strength returns

---

# PART 2: Timeframe setup in Streak

Segment: NSE
Symbol: NIFTY 50 Index
Timeframe: 15 Minutes
Position: No position (alerts only)

---

# PART 3: ENTRY CONDITIONS (Bearish hedge signal)

Create conditions in Streak:

Condition 1
Close crosses below EMA(20)

Condition 2
AND Close is below VWAP

Condition 3
AND RSI(14) is below 45

Condition 4
AND Volume greater than SMA(Volume, 20)

---

# Streak Condition format (exact)

Condition block:

Close crosses below EMA(20)

AND

Close < VWAP

AND

RSI(14) < 45

AND

Volume > SMA(Volume, 20)

---

# What this means

Market turning weak with strong selling pressure.

This is your signal to BUY PUT.

---

# PART 4: EXIT CONDITIONS (Hedge exit signal)

Create exit alert:

Condition 1
Close crosses above EMA(20)

AND

RSI(14) > 55

---

Streak exit condition format:

Close crosses above EMA(20)

AND

RSI(14) > 55

---

# What this means

Market strength returning → exit PUT hedge

---

# PART 5: What YOU do when alert comes

When ENTRY alert comes:

Step 1
Open Zerodha option chain

Step 2
Select current month expiry

Step 3
Buy:

ATM PUT

Example:

NIFTY = 22000
Buy 22000 PE

Qty: 1 lot

---

When EXIT alert comes:

Sell PUT option

Book profit or loss

---

# PART 6: Additional powerful crash hedge strategy (create second strategy)

Strategy Name:
**NIFTY Crash Hedge Alert**

Timeframe: 5 min

Condition:

Close < EMA(20)

AND

Close < VWAP

AND

RSI(14) < 40

---

This gives faster hedge signals during crash.

---

# PART 7: Strike selection rule (Use this always)

When alert triggers:

If NIFTY at 22000

Buy:

22000 PE (best)

OR

22100 PE (safer)

Avoid far OTM puts

---

# PART 8: Quantity rule for your ₹5 lakh portfolio

Normal hedge:

Buy 1 lot

Strong bearish:

Buy 2 lots

Crash:

Buy 3 lots (optional)

---

# PART 9: Profit booking rules

Exit PUT when:

Profit reaches 40–80%

OR

Exit alert triggered

OR

End of day if market strong

---

# PART 10: Expected performance

Typical monthly hedge cost:

₹5,000–₹15,000

Protection against crash:

₹50,000–₹1,00,000 portfolio loss saved

---

# PART 11: How to create this in Streak (Step-by-step)

Open Streak
Click Create Strategy

Select:

Instrument: NIFTY 50
Timeframe: 15 min

Add conditions listed above

Click:

Create → Deploy → Enable Alerts

---

# PART 12: Advanced improvement (Recommended)

Create 3 strategies:

Strategy 1: 15 min hedge
Strategy 2: 5 min crash hedge
Strategy 3: Daily hedge confirmation

This gives professional-level protection.
