
# NIFTY Options Trading Playbook v6

System components:

• Market Structure (VWAP + EMA)
• Volatility (IV Percentile + VIX)
• Option Positioning (PCR + OI)
• Liquidity Sweep Entries
• Market Phase Model
• Spread Adjustments
• Risk Management

---

# Market Phase Model

Markets cycle through four phases:

Expansion → Pullback → Compression → Transition

Expansion:
Strong trend and momentum.
Action: Hold naked option.

Pullback:
Temporary correction.
Action: Hold or re-enter.

Compression:
Small candles and tight range.
Action: Convert to spreads.

Transition:
Possible trend change.
Action: Reduce risk or exit.

---

# Liquidity Sweeps

Support Sweep:
Price dips below support then reverses.

Resistance Sweep:
Price spikes above resistance then reverses.

Range Sweep:
Both sides of range taken before breakout.

---

# Spread Conversion

Convert option to spread when:

• +30–40 NIFTY point move
• momentum slows
• compression begins

---

# Exit Rules

Exit when:

• 60–70% spread profit
• resistance/support hit
• VWAP reversal
• momentum loss

---

# End of Day Decisions

Expansion → hold
Pullback → partial hold
Compression → exit
Transition → exit

---

# Risk Model

1R = 1% of capital

Daily loss limit = 2R
Weekly loss limit = 5R

---

# Study Plan

Week 1: Study market phases and liquidity sweeps.

Week 2: Backtest setups on historical charts.

Week 3: Paper trade using Excel playbook.

Week 4: Trade with small capital.

Goal: understand system behavior before full implementation.

---

# Daily Workflow

1. Pre‑market dashboard
2. Identify market phase
3. Detect liquidity sweep
4. Plan trade
5. Execute trade
6. Convert to spread
7. Exit trade
8. Log trade
