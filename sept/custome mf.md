# 10-Stock Diversification Model — Beginner Friendly Hinglish

Averaging/diversification ka idea achha hai — paisa multiple stocks mein spread karne se **single stock ka risk kam** hota hai.

Lekin current design mein 2 important structural problems hain. Inko pehle samajhna zaroori hai, warna example dekhte waqt actual problem hide ho jayegi.

---

## Problem 1 — Capital ka calculation match nahi karta

Agar total corpus:

**₹1,00,000**

aur hum ise 5 tranches mein divide karte hain:

**₹1,00,000 ÷ 5 = ₹20,000 per tranche**

Ab agar Day 1 par saare 5 tranches deploy kar diye:

* T1 = ₹20,000
* T2 = ₹20,000
* T3 = ₹20,000
* T4 = ₹20,000
* T5 = ₹20,000

Total:

**₹1,00,000 deployed**

Matlab **100% capital already invest ho chuka hai**.

Ab agar hum bolte hain ki system baad mein "new stocks add karega", toh uske liye extra capital available hi nahi hai.

### Iska simple solution

Agar target **10 stocks** tak diversify karna hai, toh:

**₹1,00,000 ÷ 10 = ₹10,000 per tranche**

Isliye:

* T1 = ₹10,000
* T2 = ₹10,000
* T3 = ₹10,000
* ...
* T10 = ₹10,000

Total = **₹1,00,000**

So correct structure:

> **5 tranches × ₹20K nahi**

> **10 tranches × ₹10K**

Isse hum maximum **10 different stocks** mein capital spread kar sakte hain.

---

# Problem 2 — Consolidated Exit Capital ko Block Kar Sakta Hai

Ab doosri problem exit logic ki hai.

Maan lo hum wait karte hain ki **poore basket ka weighted average +6.5%** ho jaye.

Example:

10 stocks hain.

* 9 stocks achha perform kar rahe hain
* 1 stock continuously weak hai

Maan lo 9 stocks kaafi profit mein hain, lekin ek stock itna weak hai ki overall basket ka average **+6.5%** tak pahunch hi nahi raha.

Toh kya hoga?

**Poora ₹1,00,000 capital exit ka wait karta rahega.**

Matlab:

* 9 stocks achhe perform kar rahe hain
* 1 weak stock poore basket ko drag kar raha hai
* Income event nahi aa raha
* Capital rotate nahi ho raha

Ye hamare **reliable income** goal ke against hai.

---

# Iska Solution — Per-Stock Quarantine Rule

Is model mein bhi **-20% quarantine rule** active rehna chahiye.

Agar koi ek stock bahut zyada gir jata hai, jaise:

**-20%**

toh us stock ko:

**QUARANTINE**

mein daal do.

Phir us stock ko active basket ki calculation se temporarily hata do.

Simple meaning:

> Ek weak stock baaki stocks ko forever block nahi kar sakta.

Agar 10 mein se 1 stock quarantine mein chala gaya, toh baaki 9 stocks independently continue kar sakte hain.

---

# Ab Dono Fixes ke Saath Complete Example

## Starting Setup

Total corpus:

**₹1,00,000**

Total tranches:

**10**

Har tranche:

**₹10,000**

Starting mein:

**T1–T10 = IDLE**

IDLE ka matlab: paisa available hai, abhi kisi stock mein invest nahi hua.

---

# Day 1 — EOD Scan

Market close ke baad scanner stocks check karta hai.

Is din 5 stocks qualify karte hain:

* Stock A
* Stock B
* Stock C
* Stock D
* Stock E

5 tranches available hain, toh system inko allocate karta hai:

* T1 → Stock A
* T2 → Stock B
* T3 → Stock C
* T4 → Stock D
* T5 → Stock E

Total deployed:

**5 × ₹10,000 = ₹50,000**

Remaining:

**₹50,000 IDLE**

Ab:

* 5 stocks active
* 5 tranches still available

---

# Day 2 — EOD Scan

Day 2 par A–E mein se koi new entry signal nahi deta.

Lekin 2 **new stocks** qualify karte hain:

* Stock F
* Stock G

System existing stocks mein aur paisa add nahi karta.

Instead, available tranches use karta hai:

* T6 → Stock F
* T7 → Stock G

Ab total:

**₹70,000 deployed**

Aur:

**₹30,000 idle**

Ab 7 different stocks mein investment hai.

---

# Day 3 — EOD Scan

Ab 3 aur **new stocks** qualify karte hain:

* Stock H
* Stock I
* Stock J

Available tranches:

* T8 → Stock H
* T9 → Stock I
* T10 → Stock J

Ab saare 10 tranches deployed hain.

### Final deployment

**10 × ₹10,000 = ₹1,00,000**

Matlab:

**100% corpus 10 different stocks mein invested hai.**

Ab koi idle capital nahi hai.

Isliye system:

> **New entries dhundhna temporarily stop karega aur existing 10 stocks ko monitor karega.**

---

# Days 4–15 — Monitoring Period

Ab system daily existing positions ko monitor karega.

Maan lo Stock D ka performance kharab ho jata hai.

Stock D:

**-20%**

tak gir jata hai.

Toh:

### Stock D → QUARANTINE

Ab Stock D active basket ka part nahi rahega.

Active stocks:

**9 stocks**

Active capital:

**₹90,000**

Stock D ka ₹10,000 quarantine mein parked rahega.

Important:

> Stock D ki problem ki wajah se baaki 9 stocks ko block nahi karna hai.

---

# Day 16 — EOD

Ab remaining 9 active stocks ka combined value:

**₹95,850**

Initial active capital:

**₹90,000**

Profit:

**₹95,850 − ₹90,000 = ₹5,850**

Percentage:

**₹5,850 ÷ ₹90,000 = 6.5%**

Target hit!

System signal generate karega:

**`CONSOLIDATED_TARGET_HIT`**

Matlab active basket ka combined target achieve ho gaya.

---

# Day 17 — Execution Window

Ab aap active 9 positions exit kar dete ho.

Total active capital:

**₹90,000**

Exit value:

**₹95,850**

Realized profit:

**₹5,850**

Ye profit:

**INCOME_LOG**

mein record hoga.

### 9 Tranches ka kya hua?

T1–T3, etc. jo 9 active positions thi, unka capital release ho gaya.

Ye 9 tranches:

**IDLE**

ban jaati hain.

Ab ye dobara new stocks mein deploy ho sakti hain.

### Stock D ka kya hua?

Stock D already quarantine mein tha.

Isliye:

**T4 → QUARANTINE**

mein hi rahega.

Uska ₹10,000 abhi available capital nahi maana jayega.

Phase 2 mein decide hoga ki quarantine position ko kab aur kaise resolve karna hai.

---

# Day 17 Evening — Fresh Scan

Ab market close ke baad system fresh scan karega.

Agar naye stocks qualify karte hain, toh jo **9 tranches free hui hain**, unmein immediately redeployment start ho sakta hai.

Matlab system ko Stock D ke quarantine resolve hone ka wait nahi karna padega.

Cycle restart:

**9 free tranches → new opportunities → new stocks**

---

# Is Cycle ka Net Result

Approximately **16 days** mein:

* Active basket ne **+₹5,850** profit generate kiya
* ₹5,850 income ke roop mein book hua
* 9 tranches rotation mein wapas aa gayi
* 1 tranche quarantine mein parked hai
* Fresh stocks ke liye 9 tranches available hain

---

# Ab Dono Models ka Simple Comparison

## Model 1 — Per-Stock Target

Har stock ka apna target hai.

Example:

* Stock A → +6.5% → exit
* Stock B → +6.5% → exit
* Stock C → +6.5% → exit

Har stock independently exit hota hai.

### Benefit

**Frequent small payouts**

Capital bhi continuously rotate karta rehta hai.

Example:

**Stock A exit → T1 free → new stock**

**Stock B exit → T2 free → new stock**

Isliye hamesha kuch capital new opportunities search kar raha hota hai.

---

# Model 2 — Consolidated Basket

Multiple stocks ko ek basket maana jata hai.

Example:

10 stocks → ek basket

Aur jab active basket ka combined return:

**+6.5%**

hit kare, toh active positions collectively exit hoti hain.

### Benefit

* Better diversification
* Single-stock risk relatively spread out
* Averaging/basket approach feel hota hai
* Ek stock ki wajah se basket permanently stuck nahi hota, kyunki -20% quarantine rule active hai
* Profit ek larger chunk mein realize ho sakta hai

### Downside

Payouts **less frequent** ho sakte hain.

Aur jab tak basket mature nahi hota, capital opportunities ke liye locked/deployed reh sakta hai.

---

# Simple Trade-off

### Per-Stock Rotation

**Small profit → Exit → Capital free → New stock**

Iska focus:

> **Frequent rotation + frequent smaller payouts**

---

### Consolidated Basket

**Multiple stocks → Wait → Basket target → All active stocks exit → Larger payout**

Iska focus:

> **Diversification + fewer but potentially larger payouts**

---

# Sabse Simple Way Mein

Aap basically do approaches mein se choose kar rahe ho:

### Option A — Per Stock

**Har stock apna game khele.**

Stock target hit kare → exit → paisa free → next stock.

➡️ More frequent rotation
➡️ Smaller individual payouts
➡️ Capital continuously hunting mode mein

### Option B — Consolidated Basket

**Sab stocks milkar ek team ki tarah play karein.**

Basket +6.5% hit kare → active stocks collectively exit.

➡️ Better diversification feel
➡️ Fewer but potentially larger payouts
➡️ Capital blocks mein move karega
➡️ Basket target ka wait karna padega

**Important:** Dono approaches mein -20% quarantine rule ka purpose ye hai ki ek bahut weak stock baaki portfolio ko indefinitely block na kare.
