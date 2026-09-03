Option 1 finalize karte hain. Saath hi universe ko simplify karke **SENSEX 30** (BSE 30 bluechips) par freeze karte hain. SENSEX 30 se liquidity, data reliability aur manual Zerodha execution retail size ke liye bilkul clean ho jayega.

Aapne discuss karne ko kaha hai, isliye pehle hum poore system ke mechanics ko crystal-clear kar lete hain.

Neeche **Basket Cycle Model (Option 1 + SENSEX 30)** ka poora architecture review hai. Isme 5 specific operational points hain jinpar aapka input aur review chahiye:

---

### 1. Capital, Basket & Slot Structure (Summary)

* **Universe:** SENSEX 30 only (Strict 30 constituents).
* **Total Cycle Capital:** ₹1,00,000 (Cycle Pool).
* **Slot Size:** ₹4,000 per buy/action (Total 25 slots across the cycle).
* **Distinct Name Cap:** Maximum **10 stocks** per basket.
* **Per-Stock Hard Cap:** Ek single stock maximum **5 slots (₹20,000)** le sakta hai (T1 to T5 averaging). Kisi bhi haal mein T6 nahi hoga.
* **Daily Action Limit:** Max 5 execution actions per day.

---

### 2. Priority Rule: "Diversify First" vs "Averaging"

Jab EOD scan run hoga:

* **Jab tak 10 distinct names complete nahi hote:** Naye stocks (New Names) ko top priority milegi. Agar 1 slot bacha hai aur ek naya stock + ek existing stock dono qualify hote hain, toh naye stock ko entry milegi taaki portfolio 10 names mein diversify ho sake.
* **Jab 10 distinct names poore ho jayein:** Uske baad universe freeze ho jayega unhi 10 names par. Naya stock scan nahi hoga. Sirf un 10 names mein se jo dip-recovery-reclaim karenge, unhi ko agla ₹4,000 slot (T2, T3, T4, T5) milega averaging ke liye.

---

### 3. Review ke liye 5 Crucial Points (Inpar dhyan dein)

#### Point 1: Re-qualification State Reset (Averaging Rule)

Agar humne Stock A ko Day 1 par buy kiya (T1 @ ₹1,000):

* Woh agle din fir se buy signal nahi de sakta.
* Usse T2 tabhi milega jab pehle buy ke baad price ek **fresh reference high** banaye, wahan se **fresh ≥5% dip** kare, aur fir **fresh recovery + 20 DMA & VWAP reclaim** kare.
* *Sawāl:* Kya ye rule aapke mind mein clear hai ki har tranche ek naya complete dip-recovery cycle maangta hai?

#### Point 2: Consolidated Exit (+6.5%) Calculation Base

Basket close karne ke do tareeqe hain:

* **Tareeqa A (Active Pool):** Quarantined stock (-20% hit) ko side mein rakh kar, baaki bache 9 active stocks ka blended profit dekho. Jab unka combined P&L +6.5% hit kare, exit all 9 stocks. (Isme cycle fast complete hoti hai).
* **Tareeqa B (Total Deployed Capital):** Quarantined stock ka loss bhi count karo. Pure deployed capital par net +6.5% aane par hi exit karo.
* *Review:* Aap **Tareeqa A** chahte hain ya **Tareeqa B**? (Aapke example text mein Tareeqa A mention tha).

#### Point 3: Quarantined Stock (-20%) ka Fate

Agar koi stock entry ke baad -20% dip kar gaya:

* Woh **Quarantine Bucket** mein chala gaya aur averaging freeze ho gayi (no more tranches).
* Jab baaki basket +6.5% par book hoke close ho jayega:
* Kya Quarantined stock Zerodha mein hold rahega (alway open until long-term breakeven)?
* Ya usko bhi Cycle End par book/cut karke fresh clean sheet shuru karni hai?



#### Point 4: SENSEX 30 Ticker Format & Yahoo Finance

SENSEX 30 ke liye Yahoo Finance tickers BSE (`.BO`) ya NSE (`.NS`) dono hote hain (jaise `RELIANCE.NS` / `RELIANCE.BO`). BSE SENSEX ke direct liquid stocks NSE par bhi identical trade hote hain. Hum NSE tickers (`.NS`) use karein ya BSE (`.BO`)? (NSE data Yahoo par thoda jyada consistent aur volume-rich hota hai).

#### Point 5: Cash Reserve / Partial Cycle Exit

Maan lijiye 10 stocks enter hue (₹40,000 deployed), koi stock dubara dip nahi hua aur market seedha upar chala gaya. Total deployed ₹40,000 par +6.5% target hit ho gaya, jabki ₹60,000 cash reserve idle hi reh gaya.

* Rule yahi rahega ki target hit hote hi **profit book** aur **cycle reset**, chahe pure 25 slots use hue hon ya sirf 10 slots. Sahi hai?

---

Aap in 5 points par apna view batayein, aur jo bhi adjustments aapke dhyan mein ho woh share karein. Uske baad hum is revised architecture ko lock karke sheet/code schema update karenge!
