Bilkul sahi plan hai! Coding logic build aur verify ho chuka hai, ab aaj ke bache hue setup aur repository tasks clean tarike se wrap up kar lete hain taaki next session mein aap seedha feature upgrades aur Shoonya live feed par kaam kar sakein.

---

### 1. OpenAlgo Local Dashboard Ready Karna (Bina Broker API Ke Bhi)

Aap OpenAlgo ko abhi clone karke uska local admin dashboard aur SQLite database ready kar sakte hain—taaki Shoonya credentials aate hi sirf `.env` update karna pade:

```bash
cd ~/trading-projects
git clone https://github.com/marketcalls/openalgo.git
cd openalgo

python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt

cp .env.example .env

```

`.env` mein abhi ke liye sirf server settings aur placeholder broker (`shoonya`) set karke server start check kar lein:

```env
BROKER="shoonya"
BROKER_API_KEY="PENDING_SHOONYA_KEY"
BROKER_API_SECRET="PENDING_SHOONYA_SECRET"
REDIRECT_URL="http://127.0.0.1:5000/shoonya/callback"
HOST_SERVER="127.0.0.1"
PORT="5000"

```

Ab `python app.py` run karein, browser mein `[http://127.0.0.1:5000](http://127.0.0.1:5000)` kholein, apna **Local Admin Username & Password** register karein, aur **API Keys** tab se apni **OpenAlgo Local API Key** copy karke `config.py` mein save kar lein.

---

### 2. Custom Strategy Code Ko GitHub Par Push Karna

Apne `option-trailing-algo` folder mein `.gitignore` add karein taaki `venv/`, `.env`, aur `paper_trades_log.csv` accidental push na hon:

```bash
cd ~/trading-projects/option-trailing-algo

cat <<EOF > .gitignore
venv/
__pycache__/
*.pyc
.env
*.csv
EOF

git init
git add config.py broker_adapter.py strategy_engine.py .gitignore
git commit -m "feat: initial option trailing SL paper trading engine with simulation mode"

```

Iske baad GitHub par ek private repo (e.g., `option-trailing-algo`) banakar remote add karein aur push kar dein:

```bash
git branch -M main
git remote add origin https://github.com/<your-username>/option-trailing-algo.git
git push -u origin main

```

---

### 3. Next Session Ke Liye Readiness Checklist

* **Broker Account:** Shoonya (Finvasia) account opening + TOTP 2FA enable + API portal se `API Key` / `Vendor Code` generate karna.
* **Next Coding Session Agenda:**
1. OpenAlgo `.env` mein Shoonya credentials daal kar **Analyze Mode (Paper Trade)** mein live tick connection verify karna.
2. `strategy_engine.py` mein **3-min candle close / VWAP pullback entry filter** aur **max 2–3 trades/day kill-switch** add karna.



Aap OpenAlgo dashboard aur Git push complete kar lijiye—jab next session mein connect karenge toh wahin se aage badhenge!
---
### 1. Shoonya vs Dhan vs Fyers (Free API Comparison)

| Broker | Trading API | Live Market Data & WebSocket | OpenAlgo Support | Best For |
| --- | --- | --- | --- | --- |
| **Fyers** | **100% Free** | **100% Free** (Ticks, Option Chain, Historical) | Native & Super Stable | **Best Overall for Algo + Coding:** Clean Python SDK, fast WebSocket, daily auto-login (TOTP) easily automate hota hai. |
| **Dhan** | **100% Free** | Paid (`DhanHQ Data API` ~₹499/mo + GST) | Native Support | Free order execution, par live data feed ke liye monthly subscription lagta hai. |
| **Shoonya (Finvasia)** | **100% Free** | **100% Free** (Zero brokerage + Free Data) | Native Support | Zero brokerage cost, par API login/session kabhi-kabhi unstable rehta hai aur documentation thodi basic hai. |

**Recommendation:** Agar naya account open karna ho toh **Fyers** best choice hai kyunki usme **Trading API + Live Tick Data + Historical Data sab 100% free** hai aur OpenAlgo ke saath rock-solid chalta hai.

---

### 2. Option 3: Aaj Ka Complete Coding & Simulation Setup (WSL Ubuntu)

Aaj hum aisa modular architecture banayenge jisme **2 modes** honge:

1. **`SIMULATION` Mode (Aaj ke liye):** Yeh ek realistic live option premium generator chalayega (jo price spikes, pullback, breakout, aur SL hit simulate karega) taaki aap apni **Entry, Strict SL, Cost-to-Cost Breakeven, aur Trailing SL logic** ko 100% verify aur debug kar sakein.
2. **`OPENALGO` Mode (Kal ke liye):** Jaise hi Sahi ya Fyers API active hoga, aapko config mein sirf `MODE = "OPENALGO"` karna hai—poora strategy engine bina ek line change kiye live data par chal padega.

---

### Step 1: Project Folder & Files Banayein (WSL Terminal)

Apne WSL Ubuntu terminal mein yeh commands run karein:

```bash
mkdir -p ~/trading-projects/option-trailing-algo
cd ~/trading-projects/option-trailing-algo

python3 -m venv venv
source venv/bin/activate

pip install openalgo pandas python-dotenv

```

Ab folder mein yeh **3 clean files** banayenge:

1. `config.py` — Daily manual inputs (Strike Symbol, Lot Size, Initial SL, Trail Step, Trading Window).
2. `broker_adapter.py` — Abstraction layer (aaj `SIMULATION` ticks dega, kal `OPENALGO` se live LTP aur orders lega).
3. `strategy_engine.py` — Core Entry + Strict SL + Trailing SL + CSV Trade Logger.

---

### Step 2: Teeno Files Ka Code Setup Karein

#### File 1: `config.py` (Daily Manual Inputs)

Subah apna manual market structure analysis karne ke baad aap sirf isi file ko edit karenge:

```python
import datetime

# Execution Mode: "SIMULATION" (local test) ya "OPENALGO" (live feed + paper/live orders)
MODE = "SIMULATION"

# OpenAlgo Settings (Jab MODE = "OPENALGO" use karein)
OPENALGO_API_KEY = "AAPKI_OPENALGO_API_KEY"
OPENALGO_HOST = "http://127.0.0.1:5000"

# Daily Manual Strike & Risk Parameters
SYMBOL = "SENSEX26NOV80800PE"  # Strike symbol
EXCHANGE = "BFO"  # NFO (Nifty/BankNifty) ya BFO (Sensex)
QTY = 20  # 1 Lot Sensex = 20, Nifty = 75

# Entry & Risk Rules
ENTRY_BUFFER_POINTS = 1.0  # Reference high ke kitne points upar entry leni hai
INITIAL_SL_POINTS = 15.0  # Strict Initial Stop Loss (points)
TRAIL_STEP_POINTS = 8.0  # Breakeven ke baad har 8 point move par SL trail hoga
MOVE_TO_BREAKEVEN_AT_1R = True  # 1:1 profit milte hi SL ko Entry Price par layein

# Time Windows (Aapke disciplined trading window ke hisaab se)
START_TIME = datetime.time(9, 15)
SQUARE_OFF_TIME = datetime.time(15, 15)

```

---

#### File 2: `broker_adapter.py` (Simulation + OpenAlgo Bridge)

Yeh file aapke strategy engine ko broker se alag rakhti hai:

```python
import random
import time
from openalgo import api
import config


class BrokerAdapter:

  def __init__(self):
    self.mode = config.MODE
    self.sim_price = 120.0  # Simulation starting option premium
    self.sim_tick_count = 0

    if self.mode == "OPENALGO":
      self.client = api(
          api_key=config.OPENALGO_API_KEY, host=config.OPENALGO_HOST
      )

  def get_ltp(self, symbol, exchange):
    if self.mode == "OPENALGO":
      quote = self.client.quotes(symbol=symbol, exchange=exchange)
      return float(quote["data"]["ltp"])

    # SIMULATION MODE: Realistic option premium movement (Range -> Breakout -> Rally -> Pullback)
    self.sim_tick_count += 1
    if self.sim_tick_count <= 10:
      # Pehle 10 ticks consolidation (118 - 125 range)
      self.sim_price = round(random.uniform(118.0, 125.0), 2)
    elif self.sim_tick_count <= 35:
      # Breakout & upward momentum (SL trail test karne ke liye)
      self.sim_price = round(self.sim_price + random.uniform(-1.5, 4.0), 2)
    else:
      # Reversal / Pullback (Trailed SL hit test karne ke liye)
      self.sim_price = round(self.sim_price - random.uniform(0.5, 3.5), 2)

    return max(5.0, self.sim_price)

  def place_order(self, symbol, exchange, action, qty, price):
    if self.mode == "OPENALGO":
      return self.client.placeorder(
          strategy="Intraday_Option_Trail",
          symbol=symbol,
          action=action,
          exchange=exchange,
          price_type="MARKET",
          product="MIS",
          quantity=qty,
      )
    else:
      print(
          f"\n[SIM ORDER] {action} {qty} qty of {symbol} @ ₹{price} (MARKET)"
      )
      return {"status": "success", "mode": "SIMULATION"}

```

---

#### File 3: `strategy_engine.py` (Core Entry + Strict SL + Trail + CSV Logger)

Isme humne **Reference High Breakout** entry, **1:1 Cost-to-Cost Breakeven**, **Step Trailing SL**, aur **CSV Trade Log** (slippage calculation ke saath) build kiya hai:

```python
import csv
import datetime
import os
import time
from broker_adapter import BrokerAdapter
import config


def log_trade_to_csv(
    symbol, entry_time, exit_time, entry_p, exit_p, max_p, qty, reason
):
  file_exists = os.path.isfile("paper_trades_log.csv")
  gross_pnl = round((exit_p - entry_p) * qty, 2)
  # Realistic slippage & charges deduction (approx 0.5% of turnover)
  est_charges = round((entry_p + exit_p) * qty * 0.0025, 2)
  net_pnl = round(gross_pnl - est_charges, 2)

  with open("paper_trades_log.csv", mode="a", newline="") as f:
    writer = csv.writer(f)
    if not file_exists:
      writer.writerow([
          "Date",
          "Symbol",
          "Entry_Time",
          "Exit_Time",
          "Entry_Price",
          "Exit_Price",
          "Highest_Price",
          "Qty",
          "Gross_PnL",
          "Net_PnL",
          "Exit_Reason",
      ])
    writer.writerow([
        datetime.date.today(),
        symbol,
        entry_time,
        exit_time,
        entry_p,
        exit_p,
        max_p,
        qty,
        gross_pnl,
        net_pnl,
        reason,
    ])
  print(f"\n[CSV LOGGED] Gross P&L: ₹{gross_pnl} | Net P&L: ₹{net_pnl}")


def run_algo():
  broker = BrokerAdapter()
  print(
      f"=== Starting Option Algo [{config.MODE} MODE] | Symbol:"
      f" {config.SYMBOL} ==="
  )

  # State Variables
  reference_high = 0.0
  observation_ticks = 0
  in_trade = False
  entry_price = 0.0
  entry_time = ""
  current_sl = 0.0
  highest_ltp = 0.0
  breakeven_locked = False

  while True:
    now_dt = datetime.datetime.now()
    now_time = now_dt.time()

    # Live mode mein market hours check karein
    if config.MODE == "OPENALGO" and now_time < config.START_TIME:
      time.sleep(1)
      continue

    ltp = broker.get_ltp(config.SYMBOL, config.EXCHANGE)

    # PHASE 1: Observation Window (Initial Range / Reference High set karna)
    # Simulation mein pehle 10 ticks observe karega; Live mein pehle 5 mins (300 ticks)
    obs_limit = 10 if config.MODE == "SIMULATION" else 300
    if not in_trade and observation_ticks < obs_limit:
      observation_ticks += 1
      if ltp > reference_high:
        reference_high = ltp
      print(
          f"[OBSERVING {observation_ticks}/{obs_limit}] LTP: {ltp:.2f} | Ref"
          f" High: {reference_high:.2f}"
      )
      time.sleep(0.5 if config.MODE == "SIMULATION" else 1.0)
      continue

    # PHASE 2: Entry Trigger (Reference High + Buffer Breakout)
    trigger_price = round(reference_high + config.ENTRY_BUFFER_POINTS, 2)
    if not in_trade:
      if ltp >= trigger_price:
        broker.place_order(
            config.SYMBOL, config.EXCHANGE, "BUY", config.QTY, ltp
        )
        in_trade = True
        entry_price = ltp
        entry_time = now_time.strftime("%H:%M:%S")
        current_sl = round(entry_price - config.INITIAL_SL_POINTS, 2)
        highest_ltp = entry_price
        print(
            f"\n>>> [ENTRY] BUY {config.SYMBOL} @ {entry_price:.2f} | Trigger:"
            f" {trigger_price:.2f} | Initial SL: {current_sl:.2f} <<<\n"
        )
      else:
        print(
            f"[WAITING ENTRY] LTP: {ltp:.2f} | Trigger Needed:"
            f" {trigger_price:.2f}"
        )

    # PHASE 3: Active Trade Management (Strict SL + Trailing Logic)
    elif in_trade:
      if ltp > highest_ltp:
        highest_ltp = ltp

      profit_pts = highest_ltp - entry_price

      # Rule A: 1:1 Profit par Cost-to-Cost (Breakeven) SL lock karein
      if (
          config.MOVE_TO_BREAKEVEN_AT_1R
          and not breakeven_locked
          and profit_pts >= config.INITIAL_SL_POINTS
      ):
        current_sl = entry_price
        breakeven_locked = True
        print(
            f"\n>>> [SL TRAIL - BREAKEVEN] 1:1 Target Reached! SL shifted to"
            f" Entry: {current_sl:.2f} <<<\n"
        )

      # Rule B: Breakeven ke baad har TRAIL_STEP_POINTS par SL upar shift karein
      elif breakeven_locked:
        extra_profit = profit_pts - config.INITIAL_SL_POINTS
        steps = int(extra_profit // config.TRAIL_STEP_POINTS)
        proposed_sl = round(entry_price + (steps * config.TRAIL_STEP_POINTS), 2)
        if proposed_sl > current_sl:
          current_sl = proposed_sl
          print(
              f"\n>>> [SL TRAIL - STEP] High: {highest_ltp:.2f} | New Trailed"
              f" SL: {current_sl:.2f} <<<\n"
          )

      unrealized_pnl = round((ltp - entry_price) * config.QTY, 2)
      print(
          f"[IN TRADE] LTP: {ltp:.2f} | Entry: {entry_price:.2f} | SL:"
          f" {current_sl:.2f} | Peak: {highest_ltp:.2f} | Live P&L:"
          f" ₹{unrealized_pnl}"
      )

      # Rule C: Strict SL Hit Check
      if ltp <= current_sl:
        broker.place_order(
            config.SYMBOL, config.EXCHANGE, "SELL", config.QTY, ltp
        )
        exit_time = now_time.strftime("%H:%M:%S")
        reason = "Trailed SL Hit" if breakeven_locked else "Initial Strict SL Hit"
        print(f"\n>>> [EXIT - {reason}] Sold @ {ltp:.2f} <<<")
        log_trade_to_csv(
            config.SYMBOL,
            entry_time,
            exit_time,
            entry_price,
            ltp,
            highest_ltp,
            config.QTY,
            reason,
        )
        break

      # Rule D: EOD Square-Off Check (Live Mode)
      if config.MODE == "OPENALGO" and now_time >= config.SQUARE_OFF_TIME:
        broker.place_order(
            config.SYMBOL, config.EXCHANGE, "SELL", config.QTY, ltp
        )
        exit_time = now_time.strftime("%H:%M:%S")
        print(f"\n>>> [EXIT - EOD SQUARE OFF] Sold @ {ltp:.2f} <<<")
        log_trade_to_csv(
            config.SYMBOL,
            entry_time,
            exit_time,
            entry_price,
            ltp,
            highest_ltp,
            config.QTY,
            "EOD Square-Off",
        )
        break

    time.sleep(0.5 if config.MODE == "SIMULATION" else 1.0)


if __name__ == "__main__":
  run_algo()

```

---

### Step 3: Apne Laptop Par Test Run Karein

Teeno files create karne ke baad WSL terminal mein run karein:

```bash
python strategy_engine.py

```

**Aapko live console par yeh flow chalta dikhega:**

1. Pehle 10 ticks mein script **Observation Range High** banayegi.
2. Jaise hi simulated premium `Ref High + 1.0` cross karega, **BUY order** trigger hoga aur `Initial SL` set hoga.
3. Jaise hi premium `+15 points` upar jayega, SL automatically **Breakeven (Entry Price)** par shift ho jayega.
4. Uske baad har `+8 points` ke move par SL upar trail hoga, aur pullback aate hi **Trailed SL** par exit karke `paper_trades_log.csv` mein poora record save kar dega.

Aap ise WSL mein banakar run kijiye—agar aap apne entry logic mein koi specific condition (jaise **VWAP ke upar entry**, ya **trailing step ko percentage mein rakhna**, ya **2-swing target booking**) add karna chahein, toh batayein, hum abhi code mein refine kar dete hain!
