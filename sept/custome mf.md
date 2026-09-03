Ok, now I follow — you don't want 1 tranche = 1 stock. You want **1 tranche (₹20,000) split into 5 smaller slots (₹4,000 each)**, so a single ₹20,000 "transaction" itself averages across up to 5 stocks instead of betting the whole ₹20,000 on one name. And that averaging can keep extending — a second tranche's ₹20,000 adds 5 more slots, taking the total basket to 10 stocks — with one consolidated +6.5% exit for the whole basket.

That's a sound design, but it needs one explicit rule I don't want to leave ambiguous: **what happens if fewer than 5 stocks qualify on the day a tranche opens?** Two ways to handle it:

- **(a) Fill gradually** — the tranche opens with however many slots qualify that day (say 3 of 5, ₹12,000 deployed, ₹8,000 still idle within that tranche), and the remaining slots fill on later days as new stocks qualify — up to 5 slots per tranche.
- **(b) Fill same-day only** — if fewer than 5 qualify on day 1, deploy only into those and don't backfill later; that tranche runs with fewer stocks.

(a) matches "daily keep following signal to add position" from your message, so I'll assume that's what you want. Here's one full cycle on that basis:

---

**Setup:** ₹1,00,000 corpus, **5 tranches of ₹20,000**, each tranche = **5 slots of ₹4,000**. All tranches idle.

**Day 1, EOD:** Stocks A, B, C qualify (only 3 today). → **Tranche 1 opens**: slot1→A, slot2→B, slot3→C, ₹12,000 deployed, 2 slots (₹8,000) still open within T1.

**Day 2, EOD:** Stock D qualifies. → T1 slot4→D. ₹16,000 deployed, 1 slot left.

**Day 3, EOD:** Stock E qualifies. → T1 slot5→E. **T1 is now full: 5 stocks, ₹20,000 fully deployed.** T2–T5 still fully idle (₹80,000 untouched).

**Day 4, EOD:** Stock F qualifies. Since T1 is full, this opens **Tranche 2**: slot1→F, ₹4,000 deployed. T2 has 4 slots left.

*(This keeps going — say by Day 10, T2 also fills up with 5 more stocks: F, G, H, I, J. Now the basket has grown to 10 distinct stocks total across T1+T2, ₹40,000 deployed, T3/T4/T5 = ₹60,000 still in reserve, untouched.)*

**Days 10–25 — monitoring the 10-stock basket (T1+T2 combined):**
Every day, track the blended value of all 10 positions against their combined cost (₹40,000). Also watch each stock individually for the -20% quarantine line — say Stock C drops 20%; it's parked in quarantine and dropped from both the slot count and the blended calculation, so it can't block the group's exit.

**Day 26, EOD:** Blended value of the 9 active stocks (C is quarantined) reaches ₹38,300 profit is +6.5% on the ~₹36,000 still active → `CONSOLIDATED_TARGET_HIT` for the T1+T2 basket.

**Day 27, execution window:** Exit all 9 active positions. Profit booked, logged as income. **T1 and T2 fully reset to idle** — all 10 slots empty again, ₹40,000 back in the pool, ready to build a fresh basket from scratch. Stock C stays quarantined in its own slot until Phase 2 resolves it.

---

This gives you real averaging (up to 5 stocks per ₹20,000, up to 25 stocks total if all 5 tranches fill this way), gradual entry that follows signals instead of forcing five buys on day one, and a group exit — while still ring-fencing weak stocks in quarantine so they can't hold the whole basket hostage.

Does the **(a) gradual fill** assumption match what you meant, and does capping a basket at "1 tranche = 5 stocks, up to 2 tranches = 10 stocks" feel right — or should a basket be allowed to grow past 10 if more tranches keep opening?
