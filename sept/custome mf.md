# Re-Buy / Averaging Logic — Beginner Friendly Hinglish

Samajh gaya — iska matlab ye hai ki **har signal ka matlab new stock hona zaroori nahi hai**.

Agar koi stock jo aapke portfolio/basket mein already hai, dobara same entry conditions fulfill karta hai:

**Fresh Dip → Recovery → Reclaim**

toh hum usi stock mein **ek aur ₹4,000 ka slot** add kar sakte hain.

Iske liye ek **fresh tranche/slot** use hoga.

Isko averaging bol sakte hain, kyunki stock dobara lower price par mil raha hai toh total holding ka **average buy price improve** ho sakta hai.

---

# Important: 10-Stock Limit Ka Meaning

Yahan ek important distinction hai:

**10-stock limit = maximum 10 DISTINCT stocks**

Ye total number of buys ki limit nahi hai.

Example:

* Stock A → Buy 1
* Stock A → Buy 2
* Stock A → Buy 3

Ye 3 buys hain, lekin **sirf 1 distinct stock** hai.

Similarly:

* A
* B
* C
* D
* E
* F
* G
* H
* I
* J

= **10 distinct stocks**

Lekin A aur C mein multiple re-buys ho sakte hain.

---

# Ek Daily Priority Rule Lock Karna Zaroori Hai

Ek situation important hai.

Maan lo same day:

* Ek **brand-new stock** qualify karta hai
* Ek **already-held stock** bhi dobara qualify karta hai
* Lekin available capital sirf **₹4,000 ke ek slot** ke liye hai

Toh paisa kisko milega?

### Option 1

New stock mein invest karo → diversification badhegi.

### Option 2

Existing stock mein dobara invest karo → averaging hogi.

Is example mein rule ye assume kiya gaya hai:

> **Pehle diversification. Jab tak 10 distinct stocks complete nahi hote, new stocks ko priority milegi. 10 stocks complete hone ke baad hi averaging/re-buy allowed hoga.**

Simple words mein:

**Pehle 10 different stocks banao → uske baad existing stocks mein averaging karo.**

Isse starting phase mein portfolio zyada spread rahega.

---

# Ab Complete Cycle Samajhte Hain

## Day 1–3 — First 5 Stocks

Starting mein basket building start hoti hai.

5 stocks qualify karte hain:

* A
* B
* C
* D
* E

Inmein 5 slots deploy hote hain.

Har stock mein:

**₹4,000**

Total:

**5 × ₹4,000 = ₹20,000**

Ab basket mein:

**5 distinct stocks**

hain.

---

# Day 4–10 — Next 5 Stocks

Ab next days mein 5 aur **new stocks** qualify karte hain:

* F
* G
* H
* I
* J

Ye bhi ₹4,000 each ke slots se buy hote hain.

Additional investment:

**5 × ₹4,000 = ₹20,000**

Ab total:

**₹20,000 + ₹20,000 = ₹40,000 deployed**

Aur basket mein:

**10 distinct stocks**

hain:

**A, B, C, D, E, F, G, H, I, J**

### 10-Stock Cap Reached

Ab 10-stock limit complete ho gayi.

Is point ke baad:

❌ New 11th stock allowed nahi

✅ Existing A–J stocks mein re-buy/averaging allowed

---

# Day 15 — Stock A Dobara Qualify Karta Hai

Maan lo Stock A mein pehle buy ke baad:

**Dip → Recovery → Reclaim**

phir se hua.

Matlab Stock A dobara entry conditions fulfill karta hai.

Lekin Stock A already basket mein hai.

Aur 10-stock limit already complete hai.

Toh ye **new stock entry nahi** hai.

Ye:

**RE-BUY / AVERAGING**

hai.

Ab ek fresh ₹4,000 slot use karke Stock A mein aur buy karte hain.

---

# Stock A ka Example

### First Buy

Stock A price:

**₹1,000**

Investment:

**₹4,000**

Shares:

**₹4,000 ÷ ₹1,000 = 4 shares**

So:

**4 shares @ ₹1,000**

---

### Second Buy

Day 15 par Stock A ka price:

**₹900**

Again investment:

**₹4,000**

Shares:

**₹4,000 ÷ ₹900 ≈ 4.44 shares**

Ab total:

**Investment = ₹8,000**

Total shares:

**4 + 4.44 = 8.44 shares**

Blended average price:

**₹8,000 ÷ 8.44 ≈ ₹948**

So Stock A ka average buy price approximately:

### **₹948**

ho gaya.

Pehle average price ₹1,000 tha.

Ab second lower-price buy ke baad average:

**₹948**

ho gaya.

Yani lower price par re-buy karne se **cost basis improve** hua.

---

# Day 20 — Stock C Dobara Qualify

Ab Stock C bhi same pattern follow karta hai:

**Dip → Recovery → Reclaim**

Stock C already basket mein hai.

Isliye ek aur ₹4,000 ka slot:

**T3 Slot 2 → Stock C**

mein deploy hota hai.

Stock C ka blended average price bhi accordingly update ho jayega.

---

# Day 20–25 — No More Re-Buys

Ab maan lo Day 20–25 ke beech koi aur stock re-qualify nahi karta.

Toh T3 mein:

* Slot 1 → Stock A
* Slot 2 → Stock C
* Slot 3 → IDLE
* Slot 4 → IDLE
* Slot 5 → IDLE

T3 ke total:

**2 × ₹4,000 = ₹8,000 deployed**

Aur:

**3 × ₹4,000 = ₹12,000 available**

T4 aur T5 bhi completely idle hain.

Total reserve:

**₹60,000**

Total deployed:

**₹48,000**

Ye ₹48,000 10 distinct stocks mein distributed hai, lekin A aur C ke paas **2-2 buys** hain.

---

# Day 26 — Consolidated Target Hit

Ab maan lo Stock H bahut gir gaya aur:

**-20%**

par pahunch gaya.

H:

**QUARANTINE**

mein chala jayega.

Isliye H ko active basket calculation se remove kar diya jayega.

Baaki **9 active stocks** ka combined performance calculate hoga.

Maan lo active 9 stocks ka combined value unke combined cost ke comparison mein:

**+6.5%**

reach kar gaya.

System signal generate karega:

**`CONSOLIDATED_TARGET_HIT`**

---

# Day 27 — Exit

Execution window mein aap **saare active positions exit** karte ho.

Ismein:

* Stock A ki first buy
* Stock A ki second buy
* Stock C ki first buy
* Stock C ki second buy
* Baaki active stocks

sabki full holdings sell hongi.

Yani averaging ke through jo extra shares liye the, woh bhi same time exit honge.

Poore active basket ka profit calculate hoga.

Example:

**Total Profit = Basket Exit Value − Basket Cost**

Ye realized profit:

**INCOME_LOG**

mein income ke roop mein record hoga.

---

# Exit Ke Baad Tranches/Slots

Active positions exit hone ke baad:

* T1 → IDLE
* T2 → IDLE
* T3 ke used slots → IDLE
* Baaki used slots → IDLE

Matlab jo capital use hua tha, woh dobara available ho gaya.

### Distinct Stock Count

Basket ka active distinct-stock count:

**10 → 0**

reset ho jayega.

Kyunki old basket close ho chuka hai.

Ab fresh basket building next EOD scan se start hogi.

---

# Quarantine Stock H ka Kya Hoga?

Stock H:

**QUARANTINE**

mein hi rahega.

Uska capital abhi normal basket ka part nahi maana jayega.

Phase 2 mein decide hoga:

* H ko kab review karna hai
* Kab exit karna hai
* Capital kaise recover/redeploy karna hai
* Quarantine se kab release karna hai

Important point:

> H ki wajah se baaki active stocks ka exit block nahi hua.

---

# Is Model ka Main Benefit

Is approach mein ek interesting behavior aata hai:

### Strong/Conviction Stock Dip Karta Hai

↓

Phir recover karta hai

↓

Entry conditions dobara satisfy karta hai

↓

Same stock mein **₹4,000 ka additional buy**

↓

Average cost improve ho sakta hai

↓

Basket target hit hone par **poori holding exit**

Isse ek stock ke andar multiple entries possible hain.

---

# 10-Stock Cap Kyu Useful Hai?

10-stock cap ka purpose hai ki portfolio unlimited stocks mein spread na ho jaye.

Without cap:

**10 → 15 → 20 → 30 → 50 stocks...**

Portfolio bahut difficult to manage ho sakta hai.

With cap:

**Maximum 10 distinct stocks**

Uske baad:

**New stocks ❌**

**Existing stocks mein averaging ✅**

Isse portfolio controlled aur manageable rehta hai.

---

# Final Priority Rule

System ka simple decision rule:

### Stage 1 — 10 Stocks Complete Nahi Hue

Agar available ₹4,000 slot hai aur:

* New stock qualify karta hai
* Existing stock bhi re-qualify karta hai

Toh:

### **NEW STOCK ko priority**

Goal:

**Diversification first**

---

### Stage 2 — 10 Stocks Complete Ho Gaye

Ab new stock allowed nahi.

Agar existing stock dobara qualify karta hai:

### **RE-BUY / AVERAGING allowed**

Goal:

**Existing positions mein conviction build karna**

---

# One-Line Logic

> **Pehle 10 different stocks build karo → 10-stock cap reach hone ke baad new stocks band → existing stocks ke fresh dip + recovery + reclaim par ₹4,000 ke additional buys → average cost improve → active basket +6.5% target hit kare → complete active holdings exit → profit income mein log → fresh basket start.**

Ye model basically **diversification first, averaging later** approach follow karta hai.
