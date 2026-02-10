# Strategy Name

**NIFTY Active Hedge Alert System v1**
---
Here is the **exact step-by-step guide to implement the NIFTY hedge alert strategy in Streak (Zerodha)**. Follow this once, and it will automatically generate hedge alerts daily.

---

# PART 1: Login and open strategy builder

Step 1
Go to: [https://streak.tech](https://streak.tech)

Step 2
Click **Login with Zerodha**

Step 3
Enter Zerodha credentials and login

Step 4
Click **"Strategies"** from top menu

Step 5
Click **"Create Strategy"**

---

# PART 2: Basic strategy setup

You will see a form. Fill like this:

Strategy Name:
NIFTY Active Hedge Alert v1

Segment:
NSE

Instrument:
Index

Symbol:
NIFTY 50

Timeframe:
15 minute

Position type:
Select → **No Position (Alerts only)**

(This ensures Streak gives alert, not auto trade)

Click → **Next**

---

# PART 3: Add ENTRY conditions

Click → **Add Condition**

Now add conditions one by one:

---

Condition 1:

Indicator: Close
Operator: crosses below
Indicator: Exponential Moving Average
Period: 20

Click Save

---

Condition 2:

Click Add Condition

Indicator: Close
Operator: less than
Indicator: VWAP

Click Save

---

Condition 3:

Click Add Condition

Indicator: RSI
Period: 14
Operator: less than
Value: 45

Click Save

---

Condition 4:

Click Add Condition

Indicator: Volume
Operator: greater than
Indicator: Simple Moving Average
Period: 20

Click Save

---

Make sure ALL conditions connected with AND

---

# PART 4: Add EXIT conditions

Scroll down to Exit Conditions

Click Add Condition

Add:

Condition 1:

Close crosses above EMA(20)

---

Add Condition 2:

RSI(14) greater than 55

---

Click Save

---

# PART 5: Save strategy

Click:

Save Strategy

---

# PART 6: Deploy strategy

Now click:

Deploy

Select:

Enable Alerts → YES

Select Mode:

Alerts Only

Click Deploy

---

# PART 7: Enable mobile notification

Step 1
Open Streak mobile app

Step 2
Go to Settings

Step 3
Enable:

Push Notifications
Strategy Alerts

---

# PART 8: What happens next (very important)

When hedge signal comes, Streak sends alert like:

"NIFTY Active Hedge Alert – Entry Condition Met"

Now immediately:

Open Zerodha Kite
Go to Option Chain
Buy ATM PUT option

Example:

If NIFTY = 22000
Buy 22000 PE

Qty: 1 lot

---

# PART 9: Exit process

When exit alert comes:

"NIFTY Active Hedge Alert – Exit Condition Met"

Sell PUT option

Book profit/loss

---

# PART 10: Recommended additional crash strategy (optional but powerful)

Repeat same steps but use:

Timeframe: 5 min

Conditions:

Close < VWAP
RSI < 40

This catches fast crashes.

---

# PART 11: Final recommended setup (Professional level)

Create 3 strategies:

Strategy 1
NIFTY Hedge 15 min

Strategy 2
NIFTY Crash Hedge 5 min

Strategy 3
NIFTY Daily Hedge Confirmation

---

# PART 12: Your daily workflow (simple)

Morning 9:15–9:30
Check alerts

If alert comes → Buy PUT

If exit alert comes → Sell PUT

Total time needed: 5 minutes/day
---
****
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
