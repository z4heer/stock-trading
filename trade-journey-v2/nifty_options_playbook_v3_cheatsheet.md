
# NIFTY Options Trading Cheat Sheet

## Core Principle
Direction = Market Structure  
Strategy = Volatility  
Levels = Option Positioning

## Chart Setup
Indicators:
- VWAP
- EMA 20
- EMA 50
- Volume

Timeframes:
- 15m → Structure
- 5m → Entry

## Volatility Regime
| IV Percentile | Strategy |
|---|---|
| 0–30 | Buy Options |
| 30–60 | Debit Spreads |
| 60–100 | Credit Spreads |

## Expected Move
Expected Move = ATM Call Premium + ATM Put Premium

Example:
22450 CE = 120
22450 PE = 110
Expected Move = 230

## Strike Selection
Debit Spread:
Buy delta ≈ 0.50
Sell delta ≈ 0.25–0.30

Credit Spread:
Sell delta ≈ 0.20
Buy delta ≈ 0.10

## Adjustment Rules
Convert option to spread when:
- +30–40 NIFTY points move
- Momentum slows
- Market sideways

## Exit Rules
Exit when:
- 60–70% spread profit
- VWAP reversal
- Momentum loss
- Structure break

## Risk Model
1R = 1% of capital

Daily Loss Limit = 2R
Weekly Loss Limit = 5R

## Capital Growth Model
Increase position size only when capital grows 25–30%

## Daily Workflow
1. Check VIX + IVP
2. Check PCR + OI levels
3. Identify structure
4. Calculate expected move
5. Plan trade
6. Execute trade
7. Convert to spread
8. Exit
9. Log trade

## Golden Rule
Follow the system.

If rule says exit → exit.
