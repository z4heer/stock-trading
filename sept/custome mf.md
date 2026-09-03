# Custom MF — Sensex 30 Tranche Rotation Income Fund — V1.0
### Master Requirements + Design + Implementation Handoff

---

## 1. Project Objective

Build a personal, semi-automatic **"custom mutual fund"** using:

- Google Sheets as the UI/control panel
- Google Apps Script as the backend/strategy engine
- Yahoo Finance as the market-data source
- Manual Zerodha execution
- Human-in-the-Loop (HITL)
- Sensex 30 as the stock universe
- Fixed corpus of ₹1,00,000, split into 5 rotating tranches of ₹20,000

This is a standalone project, fully independent of the separate SENSEX 60 satellite system. There is no capital overlap and no shared architecture between the two.

**Objective in one line:**
> A fixed-capital, rule-based fund that rotates through Sensex 30 opportunities, books profit at a fixed target, and pays that profit out as income — while principal is preserved and redeployed, cycle after cycle.

---

## 2. Core Philosophy

- A retail investor does not move the market — the system follows Sensex 30, it does not predict it.
- No traditional stop-loss. Downside is handled by quarantine, not forced selling.
- Capital is fixed forever. The fund is judged on **income generated**, not on corpus growth.
- The investor can give the system ~1 hour a day (review + manual execution) — this is what makes an active, rotating tranche model viable, unlike a pure buy-and-hold index approach.
- Simple, deterministic rules. No discretion, no ML, no prediction.

---

## 3. Frozen V1 Decisions

| Area | Decision |
|---|---|
| Universe | Sensex 30 |
| Total capital | ₹1,00,000 (fixed, no top-ups, never grows/shrinks from trading) |
| Tranche count | 5 |
| Tranche size | ₹20,000 each |
| Tranche allocation model | Shared pool — tranches rotate across whichever Sensex 30 stock qualifies. **Not** earmarked per stock. |
| Max concurrent positions | 5 (hard cap, since total capital = 5 × tranche size) |
| Entry signal | Trend + Dip + Recovery + 20 DMA Reclaim + VWAP Reclaim (identical logic to the proven SENSEX 60 engine) |
| Exit signal | **+6.5% gain on position average price** → full exit (new for this system) |
| Stop-loss | None. Downside handled via Quarantine Cell, not forced selling |
| Quarantine trigger | Position down ≥20% from average price (exact treatment logic — Phase 2) |
| Income | 100% of every booked profit is income/payout |
| Principal | Returns to idle pool unchanged after every exit |
| Signal cadence | Daily, EOD (3:30 PM) |
| Execution window | Next day, 09:45–11:00 AM, manual Zerodha |
| Review cadence | Monthly — reporting only, never a trigger |
| Data source | Yahoo Finance |
| Broker automation | None — manual execution only |

---

## 4. Capital / Tranche Model

```
Total corpus:        ₹1,00,000
Tranche size:         ₹1,00,000 ÷ 5 = ₹20,000
Max active tranches:  5 (i.e. max 5 concurrent positions)
```

Tranches are **not** bound to a specific stock. A tranche is a rotating capital slot:

```
IDLE (₹20,000 free)
   ↓ qualifying signal found
DEPLOYED (holding one Sensex 30 stock)
   ↓ price falls ≥20% from avg price
QUARANTINE (parked, excluded from rotation — Phase 2 defines exit)
   ↓ [normal path] price gains 6.5% from avg price
TARGET HIT → book profit → pay income → tranche returns to IDLE
```

At any time, up to 5 different Sensex 30 stocks may be held — one tranche each. The system does not care *which* stock a tranche is in; it only cares that the tranche completes its cycle (target hit) and rotates.

---

## 5. Entry Rules (Signal Engine)

Reused unchanged from the proven trend/dip/recovery/reclaim design:

1. Valid Sensex 30 stock
2. **Trend PASS**: 20 DMA flat-to-rising (vs prior day) AND EOD close above 50 DMA
3. **Qualifying dip**: ≥5% decline from the system-tracked reference high
4. **Recovery confirmed**: price moving up, no fresh lower low
5. **20 DMA reclaim**: EOD close > 20 DMA
6. **VWAP reclaim**: EOD close > previous completed session's VWAP
7. **Idle tranche available**: at least one of the 5 tranches is free

All conditions PASS → valid BUY candidate. Missing/insufficient data → `DATA_INSUFFICIENT`, never a guess.

---

## 6. Ranking Rule

Applied only when qualifying candidates exceed available idle tranches on a given day.

```
Rank Score = Trend Strength + Recovery Strength + Reclaim Strength
```

- No artificial tie-breaker. Equal-rank candidates remain equally eligible.
- At the manual execution stage, the human decides among tied candidates.
- Highest-ranked candidates fill idle tranches first.

---

## 7. Exit Rule (New for This System)

- Target: **+6.5% gain on the position's average price** (whole position, not per-tranche sub-splits — each tranche only ever holds one full position, so this is moot in practice, but stated for clarity).
- On EOD close ≥ average price × 1.065 → exit signal generated for next-day execution.
- Full exit only. No partial profit booking.
- On confirmed execution: profit realized = income (100% payout), principal (₹20,000) returns to idle pool.

---

## 8. Quarantine Protocol (Downside Safety, No Stop-Loss)

- If EOD close ≤ average price × 0.80 (i.e. -20% from entry), the position moves to a **Quarantine Cell**.
- Quarantined capital is **excluded from the idle rotation pool** — it does not count toward the 5 available tranches, and no new signal can claim it.
- Quarantine is not an automatic sale. No forced exit occurs at the threshold.
- **Detailed quarantine treatment and exit/recovery logic is deferred to Phase 2** — this includes questions like: does it get a lower re-entry bar for reclaim, a longer holding runway, a deeper loss threshold for eventual manual exit, etc. V1 only defines the trigger and the parked state.

---

## 9. Daily Workflow (3:30 PM)

1. Load Sensex 30 snapshot
2. Validate market data
3. Calculate 20 DMA, 50 DMA, previous-session VWAP
4. Update reference high, dip, recovery state per stock
5. Evaluate trend + reclaim conditions
6. For each **held** position: check +6.5% target and -20% quarantine trigger
7. Build valid BUY candidates from unheld, qualifying stocks
8. Count idle tranches (5 − deployed − quarantined)
9. Rank candidates if candidates > idle tranches
10. Freeze signals (BUY / EXIT / QUARANTINE) for next-day action
11. Write Signal History
12. Write Audit

## 10. Next-Day Execution (09:45–11:00 AM)

1. Load frozen signals
2. Predefined validity checks only (no strategy recalculation)
3. Human review, manual Zerodha execution
4. Enter actual quantity/price for BUY, or confirm EXIT fill
5. On EXIT: log realized profit as income, return ₹20,000 principal to idle pool
6. On QUARANTINE trigger: mark position quarantined, remove from active rotation count
7. Update Positions, Trade Log, Audit

---

## 11. Google Sheet Architecture

11 tabs (same proven structure as the SENSEX 60 system, adapted):

1. `DASHBOARD` — corpus, idle/deployed/quarantined tranches, income paid (lifetime + this month), system status
2. `SETTINGS` — all configurable values below
3. `WATCHLIST` — Sensex 30 symbols, Active flag
4. `MARKET_DATA` — OHLCV history
5. `INDICATORS` — DMA20, DMA50, VWAP, trend/dip/recovery/reclaim status per stock
6. `TRANCHES` — one row per tranche (T1–T5): status (IDLE/DEPLOYED/QUARANTINED), current symbol, avg price, entry date, capital
7. `SIGNALS` — daily generated BUY/EXIT/QUARANTINE signals, rank, reason, frozen flag
8. `ACTION_QUEUE` — next-day actions awaiting human execution
9. `TRADE_LOG` — actual executed trades (entries and exits)
10. `INCOME_LOG` — every booked profit event: date, symbol, tranche, profit amount, cumulative income
11. `SYSTEM_AUDIT` — action log

**Difference from the SENSEX 60 sheet**: `POSITIONS` is replaced by `TRANCHES` (tranche-centric, not stock-centric — since tranches rotate across stocks) and a new `INCOME_LOG` tab tracks the fund's actual purpose: income paid out.

---

## 12. SETTINGS Values

```
SYSTEM_VERSION        = 1.0
UNIVERSE               = SENSEX_30
TOTAL_CORPUS           = 100000
TRANCHE_COUNT          = 5
TRANCHE_SIZE           = 20000

DMA_PERIOD             = 20
DMA50_PERIOD           = 50
VWAP_METHOD            = PREVIOUS_SESSION
DIP_THRESHOLD_PERCENT  = 5

PROFIT_TARGET_PERCENT  = 6.5
QUARANTINE_THRESHOLD_PERCENT = 20

SIGNAL_TIME             = 15:30
EXECUTION_START         = 09:45
EXECUTION_END           = 11:00

PRIMARY_DATA_SOURCE     = YAHOO
DATA_VALIDATION         = ENABLED
```

---

## 13. Apps Script File Structure

```
Config.gs
Setup.gs
Dashboard.gs
MarketData.gs
Indicators.gs
StateEngine.gs
SignalEngine.gs
RankingEngine.gs
TrancheManager.gs      (replaces PositionManager.gs — tranche-centric)
ExecutionManager.gs
TradeLog.gs
IncomeLog.gs           (new — tracks every profit-booking / income event)
SignalHistory.gs
AuditLogger.gs
Validation.gs
Utils.gs
```

---

## 14. Safety / Deterministic Rules

| Condition | Output |
|---|---|
| Missing/insufficient data | `DATA_INSUFFICIENT`, never BUY |
| Trend fails | `NO_ACTION` |
| No qualifying dip | No BUY |
| No recovery | `WAIT_RECOVERY` |
| One reclaim only | `WAIT_RECLAIM` |
| No idle tranche available | Signal recorded, but not actionable — `NO_TRANCHE_AVAILABLE` |
| Position ≥ +6.5% | `TARGET_HIT` → EXIT signal |
| Position ≤ -20% | `QUARANTINE` — parked, not sold |
| Duplicate signal generation | Must not create duplicate signals for same Date + Symbol + Cycle |
| Duplicate trade completion | Second completion attempt rejected |
| Signal generation | Must never modify tranche/position state |
| Broker mismatch | Flagged, never silently overwritten |

---

## 15. Testing Requirements

- Positive entry: all conditions PASS → BUY, tranche moves IDLE → DEPLOYED
- Target hit: position at avg price × 1.065 → EXIT signal, income logged, tranche returns to IDLE
- Quarantine trigger: position at avg price × 0.80 → QUARANTINE, excluded from idle count
- No idle tranche: valid signal exists but 0 idle tranches → recorded, not actionable
- Ranking: more candidates than idle tranches → ranked, highest fills first, ties preserved
- Duplicate signal/execution: rejected on second attempt
- Data failure: `DATA_INSUFFICIENT`, no BUY

---

## 16. Implementation Phases

- **Phase 1** — Google Sheet Foundation: 11 tabs, SETTINGS, WATCHLIST (Sensex 30), Dashboard, custom menu, validation. No live signals.
- **Phase 2** — Market Data: Yahoo Finance integration, Sensex 30 OHLCV, failure handling. **Also formalizes Quarantine Cell treatment/exit logic**, per Section 8.
- **Phase 3** — Indicator Engine: 20/50 DMA, previous-session VWAP, trend/reclaim status.
- **Phase 4** — State Engine: reference high, dip, recovery, quarantine state tracking.
- **Phase 5** — Signal Engine: entry (trend+dip+recovery+reclaim) and exit (+6.5% target) logic combined.
- **Phase 6** — Ranking Engine: rank score, no tie-breaker, idle-tranche-aware candidate limiting.
- **Phase 7** — Signal Freeze / Action Queue.
- **Phase 8** — Manual Execution: entries, exits, income logging, tranche state updates.
- **Phase 9** — Reconciliation: broker vs sheet.
- **Phase 10** — One-month review: income paid, cycles completed, avg days-to-target, quarantine status, capital utilization.

---

## 17. Current Status

Strategy design is frozen for V1. Immediate next step: **Phase 1 — Google Sheet Foundation** (new, standalone Sheet + Apps Script project — does not reuse the SENSEX 60 project).

---

END OF SPEC
