Yeh ek bahut hi smart aur structured plan hai. **OpenAlgo + Sahi** ka combination aapko bina kisi monthly subscription ke ek local web dashboard, live Sahi market data, built-in Paper Trading engine (Analyze Mode), aur aage chal kar 1-click Live Trading switch deta hai.

Kyunki aap laptop par kaam karenge, niche **Part 1 (Paper Trading Setup)** aur **Part 2 (Future Live Orders Shift)** ke complete action items diye gaye hain.

---

## Part 1: OpenAlgo + Sahi Paper Trading Setup (Laptop Guide)

### Phase 1: Prerequisites & Sahi API Setup (10 Minutes)

1. **Laptop Requirements Check Karein:**
* Python (`3.10` ya usse upar) aur `Git` installed hona chahiye.


2. **Sahi API Credentials Generate Karein:**
* Apne Sahi web account mein login karein $\rightarrow$ Profile Icon $\rightarrow$ **API Dashboard**.
* **Create API Key** par click karein, ek naam dein (e.g., `OpenAlgo_Local`), aur **Market Data** access select karein.
* Apni **API Key** aur **API Secret** copy karke safe rakhein.
* *Note:* Paper trading ke liye abhi Static IP ki zaroorat nahi hai.



---

### Phase 2: Laptop Par OpenAlgo Install & Configure Karein (15 Minutes)

1. **OpenAlgo Download (Clone) Karein:**
Laptop mein Terminal / Command Prompt open karein aur yeh commands chalayein:
```bash
git clone https://github.com/marketcalls/openalgo.git
cd openalgo

```


2. **Virtual Environment Aur Dependencies Install Karein:**
```bash
python -m venv venv
# Windows par activate karne ke liye:
venv\Scripts\activate
# Mac/Linux par activate karne ke liye:
source venv/bin/activate

pip install -r requirements.txt

```


3. **Environment File (`.env`) Setup Karein:**
Folder mein `.env.example` file ko rename karke `.env` banayein aur usme yeh details fill karein:
```env
BROKER_API_KEY="aapki_sahi_api_key"
BROKER_API_SECRET="aapki_sahi_api_secret"
REDIRECT_URL="http://127.0.0.1:5000/sahi/callback"

```


*(Sahi API Dashboard mein bhi Redirect URL mein `[http://127.0.0.1:5000/sahi/callback](http://127.0.0.1:5000/sahi/callback)` save kar dein).*
4. **OpenAlgo Server Start Karein:**
```bash
python app.py

```


Browser mein `[http://127.0.0.1:5000](http://127.0.0.1:5000)` kholein, apna local admin account banayein, aur **Sahi Broker** select karke login karein.

---

### Phase 3: Analyze Mode (Paper Trading) Enable Karein

OpenAlgo mein paper trading ko **"Analyze Mode"** kehte hain—yeh live Sahi data leta hai par orders real broker ko bhejne ke bajay virtual orderbook mein execute karta hai:

1. OpenAlgo dashboard ke top-right corner mein toggle ko **Live Mode se hata kar `Analyze Mode` (Paper Trade)** par switch karein.
2. Dashboard ke **API Keys** section se apni **OpenAlgo Local API Key** copy karein (aapki Python script Sahi ko direct call karne ke bajay isi OpenAlgo local API ko order bhejegi).

---

### Phase 4: Aapki Python Entry + Trailing SL Script (OpenAlgo Connected)

Ab aapko apni script mein Sahi ka complex Scrip Master ya WebSocket likhne ki zaroorat nahi hai. OpenAlgo aapko clean Python SDK (`openalgo`) deta hai.

1. Ek naye folder mein `pip install openalgo` run karein.
2. Subah apna manual market structure dekhne ke baad, script mein sirf **Strike Symbol** (`NIFTY26NOV24500CE` format) dalein:

```python
import datetime
import time
from openalgo import api

# ==========================================
# 1. OPENALGO LOCAL CONNECTION
# ==========================================
client = api(
    api_key="AAPKI_OPENALGO_LOCAL_API_KEY", host="http://127.0.0.1:5000"
)

# ==========================================
# 2. DAILY MANUAL INPUT (Sirf Strike & Risk)
# ==========================================
SYMBOL = "NIFTY26NOV24500CE"  # OpenAlgo Symbol format
EXCHANGE = "NFO"
QTY = 75
SL_POINTS = 20.0
TRAIL_STEP = 10.0
SQUARE_OFF_TIME = datetime.time(15, 15)


# ==========================================
# 3. ALGO ENGINE (Entry + Strict SL + Trail)
# ==========================================
def run_strategy():
  print(f"Tracking {SYMBOL} on OpenAlgo (Analyze/Paper Mode)...")
  range_high = 0.0
  in_trade = False
  entry_price = 0.0
  current_sl = 0.0
  highest_ltp = 0.0
  breakeven_done = False

  while True:
    now = datetime.datetime.now().time()
    if now < datetime.time(9, 15):
      time.sleep(1)
      continue

    # OpenAlgo se live LTP fetch karein (Sahi feed se aayega)
    quote = client.quotes(symbol=SYMBOL, exchange=EXCHANGE)
    ltp = float(quote["data"]["ltp"])

    # 9:15 - 9:20 Opening Range High record karein
    if datetime.time(9, 15) <= now < datetime.time(9, 20):
      if ltp > range_high:
        range_high = ltp
      print(f"Building Range... LTP: {ltp} | High: {range_high}", end="\r")
      time.sleep(1)
      continue

    # ENTRY TRIGGER (Breakout above 5-min High)
    if not in_trade and now >= datetime.time(9, 20):
      if range_high > 0 and ltp > range_high:
        # OpenAlgo ko Buy Order bhejein (Analyze Mode mein virtual fill hoga)
        client.placeorder(
            strategy="Intraday_Option_Trail",
            symbol=SYMBOL,
            action="BUY",
            exchange=EXCHANGE,
            price_type="MARKET",
            product="MIS",
            quantity=QTY,
        )
        in_trade = True
        entry_price = ltp
        current_sl = round(entry_price - SL_POINTS, 2)
        highest_ltp = entry_price
        print(f"\n[ENTRY] Bought {SYMBOL} @ {entry_price} | SL: {current_sl}")

    # TRADE MANAGEMENT (Strict SL + Trailing)
    if in_trade:
      if ltp > highest_ltp:
        highest_ltp = ltp

      profit = highest_ltp - entry_price

      # 1:1 par Cost-to-Cost SL
      if not breakeven_done and profit >= SL_POINTS:
        current_sl = entry_price
        breakeven_done = True
        print(f"\n[TRAIL] 1:1 Hit! SL moved to Breakeven: {current_sl}")

      # Uske baad har TRAIL_STEP par SL upar shift
      elif breakeven_done:
        steps = int((profit - SL_POINTS) // TRAIL_STEP)
        new_sl = entry_price + (steps * TRAIL_STEP)
        if new_sl > current_sl:
          current_sl = round(new_sl, 2)
          print(f"\n[TRAIL] New SL: {current_sl} (High: {highest_ltp})")

      # SL Hit ya 3:15 PM Square-off Check
      if ltp <= current_sl or now >= SQUARE_OFF_TIME:
        client.placeorder(
            strategy="Intraday_Option_Trail",
            symbol=SYMBOL,
            action="SELL",
            exchange=EXCHANGE,
            price_type="MARKET",
            product="MIS",
            quantity=QTY,
        )
        reason = "EOD Square-Off" if now >= SQUARE_OFF_TIME else "SL/Trail Hit"
        print(f"\n[EXIT - {reason}] Sold @ {ltp}")
        break

    time.sleep(1)


if __name__ == "__main__":
  run_strategy()

```

### Daily Morning Routine (Paper Trading Ke Dauran)

* **8:55 AM:** Sahi API Dashboard par jakar API Key ko **Approve** karein (har subah 6 AM expire hoti hai).
* **9:00 AM:** Laptop par `python app.py` chalayein, OpenAlgo dashboard mein login karein, aur check karein ki toggle **Analyze Mode** par hai.
* **9:10 AM:** Apna manual market structure analysis karke script mein `SYMBOL` update karein aur `python strategy.py` run kar dein.
* **3:30 PM:** OpenAlgo dashboard ke **Orderbook / Tradebook / Positions** tab mein apna virtual P&L aur execution logs review karein.

---

## Part 2: Baad Mein Actual Orders (Live Trading) Implement Karne Ke Liye Kya Karna Hoga?

Jab aap 2–4 hafte paper trading karke apni entry aur trailing accuracy se satisfied ho jayein, aur **completely Sahi par live orders** shift karna chahein, toh aapko yeh 5 changes karne honge:

### 1. SEBI / Broker Compliance (Static IP & Full Access)

* **Static IP Lenge:** SEBI aur broker rules ke hisaab se live automated orders bhejne ke liye aapke system ka **Static IP** broker ke paas whitelisted hona zaroori hai. Laptop ke home Wi-Fi ka IP badalta rehta hai, isliye live trading ke waqt aapko ya toh ISP se Static IP lena hoga, ya ek sasta Indian Cloud VPS (jaise AWS Mumbai / DigitalOcean Bangalore, ~₹400–₹500/month) lekar uska Static IP Sahi dashboard mein whitelist karna hoga.
* **API Permission Upgrade:** Sahi API Dashboard mein apni API key ko **Market Data** se badal kar **Trading / Full Access** par update karein.

### 2. Order Execution Logic Mein 2 Critical Upgrades

Paper trading mein `MARKET` order aur software-based SL chal jata hai, par real paison ke saath yeh risk ho sakta hai:

* **Broker-Side Hard Stop-Loss (`SL` / `SL-M` Order):** Real market mein agar aapka internet ya laptop band ho gaya, toh software SL trigger nahi hoga. Isliye live mode mein entry hote hi broker ke server par ek **actual Stop-Loss order** place karein, aur jaise-jaise price trail ho, `modifyorder()` API call karke broker ka SL order modify karein.
* **Market Price Protection / Limit Buffer:** Options mein sudden spike se bachne ke liye `MARKET` order ki jagah `LIMIT` order (e.g., `LTP + 2 points` buffer ke saath) use karein taaki illiquid candle mein kharab fill na mile.

### 3. Position Sizing & Margin Check

* Live script mein order place karne se pehle `funds()` API call karke check karein ki Sahi account mein sufficient margin available hai ya nahi.
* Ek **Max Daily Loss Limit** (Kill Switch) add karein—e.g., agar din ka loss ₹3,000 cross kare toh OpenAlgo/script saari positions close karke us din ke liye trading lock kar de.

### 4. OpenAlgo Se Direct Sahi Par Shift (Optional)

* **Option 1 (Recommended):** Aap live trading bhi **OpenAlgo ke through** hi chala sakte hain—aapko code mein ek line bhi nahi badalni padegi, bas OpenAlgo dashboard mein toggle ko **Analyze Mode se `Live Mode**` par karna hoga (aur Static IP lagana hoga).
* **Option 2 (Pure Sahi SDK):** Agar aap OpenAlgo hatakar sirf Sahi par rehna chahte hain, toh upar wali script mein `client.quotes()` aur `client.placeorder()` ko Sahi ke direct Python SDK (`sahi.get_ltp()` aur `sahi.place_order()`) se replace kar dein.
