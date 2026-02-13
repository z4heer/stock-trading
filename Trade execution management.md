# 🎯 Objective

You must achieve 4 things:

1. Always know which stocks need action TODAY
2. Get automatic alerts before losses increase
3. Have predefined exit rules (no emotional decisions)
4. Protect capital first, profit second

---

# 🧩 Complete Monitoring System Architecture

```
Screener → Zerodha Watchlist → Streak/Zerodha Alerts → Sensibull Chart Monitoring → Google Sheet Tracker → Exit Execution
```

---

# 🥇 Step 1: Zerodha Watchlist (Primary Monitoring Hub)

Create 3 watchlists in Zerodha Kite:

### Watchlist 1: ACTIVE TRADES

Stocks currently held

Example:

```
INFY
ICICIBANK
RELIANCE
HAL
```

### Watchlist 2: READY TO BUY

Momentum stocks identified but not yet bought

### Watchlist 3: EXIT WATCH

Stocks showing weakness

---

# 🥈 Step 2: Zerodha Alert System (MOST IMPORTANT)

This protects you automatically.

Open Kite → Chart → Alerts → Create alerts

Create these 3 alerts per stock:

---

## Alert 1: Stoploss Alert (MANDATORY)

Example:

If INFY bought at 1500
Stoploss = 1450

Create alert:

```
Alert when price <= 1450
```

Action:
Immediately exit

---

## Alert 2: Breakdown Alert

Condition:

```
Alert when price crosses below 20 EMA
```

Action:
Prepare to exit

---

## Alert 3: Profit Protection Alert

Condition:

```
Alert when price falls 3% from high
```

Locks profit

---

# 🥉 Step 3: Sensibull Chart Monitoring (Daily 5 minutes only)

Open Sensibull → Chart

Add indicators:

* 20 EMA
* 50 EMA
* Volume

Exit signal when:

* Price below 20 EMA
* High volume selling candle
* Lower high formation

---

# 🧠 Step 4: Mandatory Stoploss Rules (Professional Level)

Always define stoploss at entry time:

| Trade Type | Stoploss |
| ---------- | -------- |
| Swing      | 5%       |
| Momentum   | 6–8%     |
| Breakout   | 4–6%     |

Example:

Buy price: 1000
Stoploss: 950

Max loss = controlled

---

# 🧾 Step 5: Google Sheet Tracker (Control Tower)

Track these columns:

```
Stock Name
Buy Date
Buy Price
Current Price
Stoploss
Target
Alert Set (Yes/No)
Action Required (Hold/Exit)
```

Update once daily.

---

# 🚨 Step 6: Emergency Exit Rules (Follow blindly)

Exit immediately if ANY happens:

1. Price hits stoploss
2. Price below 50 EMA
3. Market weak (Nifty down >1.5%)
4. Stock falls >8% from your buy price
5. Strong red candle with high volume

NO EXCEPTION.

---

# 🛡️ Step 7: Automatic Protection Using Zerodha GTT Order (BEST METHOD)

This is the safest method.

When buying stock, immediately place:

GTT order with:

```
Stoploss sell price
Target sell price
```

Example:

Buy INFY at 1500

GTT:

```
Stoploss: 1450
Target: 1650
```

This protects automatically even if you don’t monitor.

---

# 🥇 Step 8: Daily 10-Minute Routine (Professional Workflow)

Morning:

```
Check Zerodha watchlist
Check alerts triggered
Exit weak stocks
```

Evening:

```
Check Sensibull chart
Update Google Sheet
Set alerts if missing
```

Total time: 10 minutes

---

# 🧠 Most Important Rule (Golden Rule)

Professional traders follow this strictly:

> Small loss = normal
> Big loss = mistake

Never allow loss > 8%

---

# 🧰 Best Combination (Final Setup)

Use this combination:

```
Screener → Find stocks
↓
Zerodha Watchlist → Monitor price
↓
Zerodha Alerts → Automatic warning
↓
GTT Orders → Automatic exit
↓
Sensibull → Chart confirmation
↓
Google Sheet → Tracking and discipline
```

---
