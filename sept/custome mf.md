Good — let's walk through one complete cycle end to end with made-up numbers, so you can see exactly how it behaves. All prices/dates below are illustrative, not real signals.

**Starting state:** ₹1,00,000 corpus, T1–T5 all IDLE, ₹20,000 each.

---

**Day 1 (Mon), 3:30 PM — EOD scan**
Scanner checks all 30 Sensex stocks. Only **Stock A** passes all 5 entry conditions (trend, dip, recovery, 20 DMA reclaim, VWAP reclaim). 5 tranches are idle, only 1 candidate → no ranking needed. Signal frozen: `BUY Stock A`. Written to `ACTION_QUEUE` for tomorrow.

**Day 2 (Tue), 09:45–11:00 AM — Execution window**
You open Zerodha, place the manual buy. Stock A is at ₹1,000 → 20 shares for ₹20,000.
→ **T1 becomes DEPLOYED**: Stock A, avg price ₹1,000, target ₹1,065 (+6.5%), quarantine trip-wire ₹800 (-20%).
→ **T2, T3, T4, T5 still IDLE** — nothing else qualified that day.

**Day 2, 3:30 PM — EOD scan (same evening)**
Now **Stock B** and **Stock C** both qualify. 4 tranches idle, 2 candidates → both get signals, no ranking contest needed. `BUY Stock B`, `BUY Stock C` frozen for tomorrow.

**Day 3 (Wed), execution window**
Both buys executed. Stock B at ₹500 → 40 shares. Stock C at ₹2,000 → 10 shares.
→ **T2 DEPLOYED**: Stock B, avg ₹500, target ₹532.50
→ **T3 DEPLOYED**: Stock C, avg ₹2,000, target ₹2,130
→ **T4, T5 still IDLE**

*(If on some later day 6 stocks qualified at once but only 2 tranches were idle, the ranking engine would score all 6 and the top 2 would fill the tranches — the rest stay as valid-but-unfilled signals, visible in `SIGNALS` but not actionable that day.)*

---

**Days 4–18 — holding period**
T1, T2, T3 are tracked daily against their target and quarantine lines. Nothing forces an exit; the system just watches. Say Stock B has a rough patch and drifts down — not our happy path, but worth knowing: if it closed at ₹400 (-20%), T2 would move to `QUARANTINE`, get excluded from the idle count, and simply sit there until Phase 2 quarantine logic is built. For this walkthrough, assume it doesn't happen.

**Day 19 (EOD scan)**
Stock A closes at ₹1,067 — above its ₹1,065 target. `TARGET_HIT` signal generated for T1: `EXIT Stock A`.

**Day 20, execution window**
You sell all 20 shares of Stock A at ~₹1,067.
→ Profit realized: 20 × (1067 − 1000) = **₹1,340**, logged in `INCOME_LOG` as income.
→ **T1 returns to IDLE**, ₹20,000 principal exactly restored, ready to redeploy.
→ Your bank/income pool is now richer by ₹1,340; the fund's corpus is still exactly ₹1,00,000.

**Day 20, 3:30 PM — same evening's EOD scan**
**Stock D** now qualifies. T1 is the only idle tranche → `BUY Stock D` frozen.

**Day 21, execution window**
Stock D bought at ₹300 → 66 shares for ₹19,800 (₹200 stays uninvested in that tranche, or you round to nearest lot — small residual cash is normal and just sits with that tranche until next redeploy).
→ **T1 DEPLOYED again**, now in Stock D.

---

**Where things stand after this one cycle:**

| Tranche | Day 1 | Day 3 | Day 20 | Day 21 |
|---|---|---|---|---|
| T1 | IDLE | Stock A | IDLE (exited, +₹1,340 income) | Stock D |
| T2 | IDLE | Stock B | Stock B | Stock B |
| T3 | IDLE | Stock C | Stock C | Stock C |
| T4 | IDLE | IDLE | IDLE | IDLE |
| T5 | IDLE | IDLE | IDLE | IDLE |

Cumulative income so far: **₹1,340**, corpus still **₹1,00,000**, 2 tranches still waiting for their first signal.

This is the whole engine, repeating indefinitely: idle capital waits → a stock proves itself (dip + recovery + reclaim) → capital deploys → it either reaches +6.5% and pays out, or it drops 20% and parks in quarantine → freed capital hunts again. Nothing about this requires daily attention — your ~1 hour a day is really just the 09:45–11:00 execution check and a glance at the dashboard.

Does this match the shape you had in mind, or is there a scenario in this flow (multiple tranches free on the same day, a quarantine event, etc.) you want to see played out differently before we build it?
