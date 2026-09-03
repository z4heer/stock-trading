## Starting Point

* Total corpus = **₹1,00,000**
* Total 5 tranches hain: **T1, T2, T3, T4, T5**
* Har tranche = **₹20,000**
* Starting mein sabhi tranches **IDLE** hain.

**IDLE ka simple meaning:**
Tranche ke paas paisa available hai aur abhi kisi stock mein invest nahi hua hai.

---

## Day 1 — Monday, 3:30 PM: EOD Scan

System market close ke around **30 Sensex stocks** ko scan karta hai.

Scanner kuch conditions check karta hai:

1. Trend sahi hai?
2. Stock mein dip aaya tha?
3. Dip ke baad recovery hui?
4. Stock ne **20 DMA** reclaim kiya?
5. Stock ne **VWAP** reclaim kiya?

Is din sirf **Stock A** sabhi 5 conditions pass karta hai.

Ab:

* 5 tranches available hain
* Sirf 1 stock qualify hua
* Isliye ranking karne ki zarurat nahi hai

System signal ko freeze karta hai:

**`BUY Stock A`**

Ye signal **ACTION_QUEUE** mein save ho jata hai, jiska matlab hai ki **kal execution window mein isko buy karna hai**.

---

## Day 2 — Tuesday, 09:45–11:00 AM: Buy Execution

Ab aap Zerodha open karte ho aur manually Stock A buy karte ho.

Stock A ka price:

**₹1,000 per share**

Aapke T1 mein ₹20,000 available hain.

₹20,000 ÷ ₹1,000 = **20 shares**

Toh aap:

**20 shares × ₹1,000 = ₹20,000**

ka Stock A buy karte ho.

Ab:

### T1 → DEPLOYED

T1 ki details:

* Stock = Stock A
* Average Buy Price = ₹1,000
* Target = ₹1,065 (**+6.5%**)
* Quarantine level = ₹800 (**-20%**)

Baaki:

* T2 = IDLE
* T3 = IDLE
* T4 = IDLE
* T5 = IDLE

Matlab abhi sirf T1 use hua hai.

---

## Day 2 — 3:30 PM: Same Evening EOD Scan

Market close ke baad system dobara scan karta hai.

Is baar:

* **Stock B** qualify karta hai
* **Stock C** qualify karta hai

Ab:

* 4 tranches IDLE hain
* 2 stocks qualify hue

Dono ko signal mil sakta hai.

System freeze karta hai:

**`BUY Stock B`**

**`BUY Stock C`**

Ye dono next day's execution ke liye ready hain.

---

# Day 3 — Wednesday: Execution

Ab Stock B aur Stock C dono ke buys execute hote hain.

### Stock B

Stock B = **₹500**

₹20,000 ÷ ₹500 = **40 shares**

So:

**40 × ₹500 = ₹20,000**

T2 → **DEPLOYED**

* Stock B
* Average Price = ₹500
* Target = ₹532.50 (+6.5%)

### Stock C

Stock C = **₹2,000**

₹20,000 ÷ ₹2,000 = **10 shares**

So:

**10 × ₹2,000 = ₹20,000**

T3 → **DEPLOYED**

* Stock C
* Average Price = ₹2,000
* Target = ₹2,130 (+6.5%)

Ab status:

* T1 = Stock A
* T2 = Stock B
* T3 = Stock C
* T4 = IDLE
* T5 = IDLE

---

## Agar Ek Din Bahut Saare Stocks Qualify Kar Jayein?

Maan lo kisi future day:

* 6 stocks qualify kar gaye
* Lekin sirf 2 tranches IDLE hain

Toh system randomly stocks select nahi karega.

**Ranking engine** sabhi 6 stocks ko score karega.

Example:

| Rank | Stock   | Score |
| ---- | ------- | ----: |
| 1    | Stock X |    92 |
| 2    | Stock Y |    89 |
| 3    | Stock Z |    85 |
| 4    | Stock P |    81 |
| 5    | Stock Q |    78 |
| 6    | Stock R |    74 |

Sirf top 2:

* Stock X
* Stock Y

ko available tranches milengi.

Baaki stocks ke signals valid rahenge aur **SIGNALS** mein visible honge, lekin us din unke liye capital available nahi tha.

---

# Day 4–18 — Holding Period

Ab T1, T2 aur T3 mein stocks hold ho rahe hain.

System daily unko monitor karta rahega.

Mainly system do important levels dekhta hai:

### 1. Target

Agar stock **+6.5%** tak pahunch gaya → profit book karne ka signal.

### 2. Quarantine Level

Agar stock **-20%** tak gir gaya → stock quarantine mein chala jayega.

### Example: Stock B Gir Gaya

Maan lo Stock B:

₹500 → ₹400

₹400, ₹500 se **20% neeche** hai.

Toh:

**T2 → QUARANTINE**

Iska matlab:

* T2 ko normal IDLE tranche nahi maana jayega
* T2 mein capital stuck hai
* T2 new stock buy karne ke liye available nahi hai
* Phase 2 quarantine logic banne tak ye position wahi parked rahegi

Lekin hamare current example mein assume karte hain ki Stock B quarantine mein nahi gaya.

---

# Day 19 — EOD Scan

Ab Stock A ka price:

**₹1,067**

Uska target tha:

**₹1,065**

Stock A target se upar chala gaya.

System automatically signal generate karta hai:

**`TARGET_HIT`**

Aur action:

**`EXIT Stock A`**

Matlab Stock A ko sell karna hai.

---

# Day 20 — Execution Window

Ab aap Stock A ke saare **20 shares sell** kar dete ho.

Selling price ≈ **₹1,067**

Profit calculation:

**20 × (₹1,067 − ₹1,000)**

= 20 × ₹67

= **₹1,340 profit**

Ye ₹1,340 **INCOME_LOG** mein record hota hai.

### Ab T1 ka kya hua?

Stock A sell ho gaya.

Isliye:

**T1 → IDLE**

Aur T1 ka original principal:

**₹20,000**

wapas available ho gaya.

Ab T1 dobara kisi naye stock mein deploy ho sakta hai.

---

# Important Point: Corpus vs Income

Yahan ek important concept hai.

Initial corpus:

**₹1,00,000**

Stock A se profit:

**₹1,340**

Lekin corpus abhi bhi:

**₹1,00,000**

hi maana ja raha hai.

Kyun?

Kyuki ₹1,340 ko hum **income/profit pool** mein alag track kar rahe hain.

Simple way:

> **Corpus = Trading ke liye allocated original capital**

> **Income = Trading se nikla realized profit**

Isliye ₹1,340 ko automatically corpus mein add nahi kiya gaya.

---

# Day 20 — 3:30 PM: EOD Scan

Same day market close ke baad scanner phir stocks check karta hai.

Is baar:

**Stock D qualify karta hai.**

Ab sirf T1 IDLE hai.

Isliye:

**`BUY Stock D`**

signal freeze ho jata hai.

---

# Day 21 — Stock D Buy

Stock D ka price:

**₹300**

T1 mein available capital:

**₹20,000**

₹20,000 ÷ ₹300 ≈ **66 shares**

66 shares ki cost:

**66 × ₹300 = ₹19,800**

Toh:

* 66 shares buy
* ₹19,800 invested
* ₹200 remaining

Ye ₹200 chhota residual cash hai.

Ye normal hai. Ye tranche ke saath hi available rahega aur future redeployment mein use ho sakta hai.

Ab:

**T1 → DEPLOYED in Stock D**

---

# Current Status

| Tranche | Day 1 | Day 3   | Day 20                | Day 21  |
| ------- | ----- | ------- | --------------------- | ------- |
| T1      | IDLE  | Stock A | IDLE → +₹1,340 profit | Stock D |
| T2      | IDLE  | Stock B | Stock B               | Stock B |
| T3      | IDLE  | Stock C | Stock C               | Stock C |
| T4      | IDLE  | IDLE    | IDLE                  | IDLE    |
| T5      | IDLE  | IDLE    | IDLE                  | IDLE    |

### Current Situation

* T1 → Stock D mein deployed
* T2 → Stock B mein deployed
* T3 → Stock C mein deployed
* T4 → IDLE
* T5 → IDLE
* Realized income = **₹1,340**
* Original corpus = **₹1,00,000**

---

# Simple Flow Samjho

Basically poora system kuch aise kaam karta hai:

**IDLE Capital**

↓

**Stock scanner stock ko check karta hai**

↓

**Stock dip + recovery + DMA reclaim + VWAP reclaim karta hai**

↓

**BUY signal generate hota hai**

↓

**Available tranche mein capital deploy hota hai**

↓

**Stock ko daily monitor karo**

↓

### Do possibilities:

**Option 1: Stock +6.5% target hit kare**

→ SELL

→ Profit realize

→ Tranche IDLE

→ Capital next opportunity ke liye ready

**Option 2: Stock -20% gir jaye**

→ QUARANTINE

→ Tranche normal deployment ke liye available nahi

---

# Ek Line Mein Pura Engine

> **Paisa idle rehta hai → stock apni strength prove karta hai → capital deploy hota hai → stock target hit karta hai to profit book hota hai → tranche free hoti hai → wahi capital next opportunity ko search karta hai.**

Aur agar stock bahut zyada gir jaye, jaise **-20%**, toh position **QUARANTINE** mein chali jaati hai.

Is process ko continuously repeat kiya jaata hai.

## Daily Kitna Time Lagega?

Is design ka purpose ye nahi hai ki aap poora din market dekhte raho.

Aapka main manual work roughly:

**09:45–11:00 AM execution window**

mein hoga:

* Dashboard check karo
* BUY/EXIT actions dekho
* Zerodha mein manually order place karo
* Execution confirm karo

Baaki scanning, signal generation, ranking aur tracking system handle karega.

So overall idea:

**System = Stock scanning + signal + tracking**

**You = Final decision + manual order execution**

Ye hi poora cycle repeatedly chalta rahega.
