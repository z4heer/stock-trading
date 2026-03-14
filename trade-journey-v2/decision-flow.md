# NIFTY Options Trading Decision Flow

```
START DAY
   │
   │
   ▼
Check Market Dashboard
(VIX, IVP, PCR, OI, Expected Move)
   │
   │
   ▼
Identify Market Structure
(VWAP + EMA)
   │
   ├───────────────┬───────────────┐
   │               │               │
   ▼               ▼               ▼
TREND DAY      RANGE DAY      REVERSAL DAY
   │               │               │
   │               │               │
   ▼               ▼               ▼
Select Strategy Based on IVP

IVP < 30 → Long Options
IVP 30–60 → Debit Spread
IVP > 60 → Credit Spread
   │
   │
   ▼
Define Trade Plan
(Entry, Invalidation, Target)
   │
   │
   ▼
Enter Trade
(Long Call / Long Put)
   │
   │
   ▼
Monitor Price Movement
   │
   ├───────────────┬────────────────┐
   │               │                │
   ▼               ▼                ▼
+30–40 pts      Sideways        Structure Break
Move            30 min          VWAP Break
   │               │                │
   ▼               ▼                ▼
Convert to      Convert to       Exit Trade
Debit Spread    Spread
   │
   │
   ▼
Manage Spread
   │
   │
   ▼
Exit Conditions
   │
   ├───────────────┬───────────────┬─────────────┐
   │               │               │             │
   ▼               ▼               ▼             ▼
60–70% Profit   Resistance      VWAP Break   Momentum Loss
   │
   │
   ▼
Exit Trade
   │
   │
   ▼
Log Trade in Excel
(Execution_Log + Review)
   │
   │
   ▼
END OF DAY REVIEW
```
