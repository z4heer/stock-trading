Here is the exact `tmux` command and step-by-step morning launch sequence for **Thursday, Nov 26, 2026 (SENSEX Expiry Day)**. Keep OpenAlgo in its own isolated `tmux` session (`openalgo`) so it never gets killed when you restart or close your trading engine session (`algo_sess0`).

---

### Step 1: Start OpenAlgo Gateway (`09:05 AM IST`)

Run this in WSL Ubuntu to launch OpenAlgo inside its dedicated `openalgo` session:

```bash
tmux kill-session -t openalgo 2>/dev/null || true
tmux new-session -d -s openalgo -c ~/trading-projects/openalgo "source venv/bin/activate && python3 app.py"
tmux attach -t openalgo

```

1. Wait until you see `Running on [http://127.0.0.1:5000](http://127.0.0.1:5000)` (and verify Fyers login in your browser at `[http://127.0.0.1:5000](http://127.0.0.1:5000)`).
2. **Detach cleanly without stopping OpenAlgo:** Press **`Ctrl + B`**, release, then press **`D`**.

---

### Step 2: Run Screeners with 60-Second Spacing (`10:15 AM IST`)

*(Spacing the two screeners by 60 seconds guarantees you stay well inside Fyers' `50 requests/min` REST limit.)*

1. **At `10:15 AM` — Screen Option Strikes (`12` REST calls):**
```bash
cd ~/trading-projects/option-trailing-algo && source venv/bin/activate
python3 strike_screener.py

```


* When prompted by **`[0-DTE EXPIRY WARNING]`**, press **`Y`** (Enter) to roll into next week's expiry (`₹4,000–₹5,500/lot` sweet spot).
* Copy the output `CANDIDATE_STRIKES = [...]` list into `config.py`.


2. **At `10:17 AM` — Screen Sensex-30 Cash Equities (`30` REST calls):**
```bash
python3 equity_screener.py

```


* Copy `EQUITY_WATCHLIST = [...]` and `EQUITY_DIRECTION = "..."` into `config.py` and save (`Ctrl + S`).



---

### Step 3: Launch Both Engines in `algo_sess0` (`10:25 AM IST`)

Run this exact block to spin up a clean two-window `tmux` session (`Window 0` = Options V3.4, `Window 1` = Cash Equity V1.0):

```bash
tmux kill-session -t algo_sess0 2>/dev/null || true
tmux new-session -d -s algo_sess0 -n options -c ~/trading-projects/option-trailing-algo "source venv/bin/activate && python3 v3_momentum_engine.py"
tmux new-window -t algo_sess0:1 -n equity -c ~/trading-projects/option-trailing-algo "source venv/bin/activate && python3 equity_intraday_engine.py"
tmux select-window -t algo_sess0:0
tmux attach -t algo_sess0

```

---

### Quick `tmux` Navigation Cheat Sheet

* **Switch between Options (`0`) and Equity (`1`):** `Ctrl + B`, then **`0`** or **`1`** (or **`N`** for next window).
* **Detach & leave both engines running hands-free:** `Ctrl + B`, then **`D`**.
* **Re-attach later to check logs:** `tmux attach -t algo_sess0`
* **Stop both engines cleanly after `01:30 PM`:** Attach to `algo_sess0`, press **`Ctrl + C`** in Window `0` and Window `1`, then `tmux kill-session -t algo_sess0`.
