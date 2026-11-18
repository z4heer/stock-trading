Chaliye bilkul clean aur structured tarike se aapke **WSL Ubuntu** development environment mein is project ka setup complete karte hain.

Hum poore setup ko **2 isolated parts** mein rakhenge taaki aapke baaki Python/FastAPI projects ke saath koi conflict na ho:

1. **Part A:** OpenAlgo Local Server (Broker gateway + Analyze/Paper Mode)
2. **Part B:** Aapka Custom Strategy Workspace (`sahi-paper-algo`)

---

### Initial Setup Tasks (Step-by-Step Execution)

#### Task 1: Workspace Folder Structure Banayein (WSL Ubuntu)

Apne WSL terminal mein ek dedicated directory banayein jahan OpenAlgo aur aapki custom strategy scripts alag-alag folders mein rahengi:

```bash
mkdir -p ~/trading-projects/openalgo-setup
cd ~/trading-projects/openalgo-setup

```

#### Task 2: Sahi API Credentials Generate & Approve Karein

1. Browser mein **Sahi Web** par login karein $\rightarrow$ Profile Icon $\rightarrow$ **API Dashboard**.
2. **Create API Key** par click karein:
* **App Name:** `OpenAlgo_Local`
* **Access Type:** `Market Data` (ya `Full Access` — paper trading mein dono chalenge)
* **Redirect URL:** `[http://127.0.0.1:5000/sahi/callback](http://127.0.0.1:5000/sahi/callback)`
* **IP Whitelist:** Abhi blank chhod dein (paper trading/data feed ke liye mandatory nahi hai).


3. Generated **API Key** aur **API Secret** copy karein, aur aaj ke liye dashboard par **"Approve"** button zaroor click karein (kyunki Sahi key daily 6:00 AM expire hoti hai).

#### Task 3: OpenAlgo Clone & Virtual Environment Setup

`~/trading-projects/openalgo-setup` ke andar OpenAlgo clone karein aur uska apna isolated `venv` banayein:

```bash
git clone https://github.com/marketcalls/openalgo.git
cd openalgo

python3 -m venv venv
source venv/bin/activate

pip install --upgrade pip
pip install -r requirements.txt

```

#### Task 4: OpenAlgo `.env` Configuration

`.env.example` ko copy karke `.env` banayein aur Sahi credentials dalein:

```bash
cp .env.example .env
nano .env

```

`.env` file mein yeh values update karein (baaki default chhod sakte hain):

```env
BROKER_API_KEY="aapki_sahi_api_key"
BROKER_API_SECRET="aapki_sahi_api_secret"
REDIRECT_URL="http://127.0.0.1:5000/sahi/callback"
HOST_SERVER="127.0.0.1"
PORT="5000"

```

*(Save karne ke liye `Ctrl+O`, `Enter`, aur exit ke liye `Ctrl+X`)*

#### Task 5: OpenAlgo Server Start & Analyze Mode Verify Karein

1. Server run karein:
```bash
python app.py

```


2. Browser mein `[http://127.0.0.1:5000](http://127.0.0.1:5000)` kholein:
* First-time local admin user ID aur password create karein.
* Broker login screen par **Sahi** select karke authenticate karein.


3. Dashboard open hone ke baad top bar mein **Analyze Mode (Paper Trade)** toggle ON karein.
4. Dashboard ke **API Keys** tab se apni **OpenAlgo Local API Key** copy karke rakh lein.

#### Task 6: Custom Strategy Project Folder Setup (Coding Workspace)

Ek **doosra WSL terminal tab** kholein (kyunki pehle tab mein OpenAlgo server chalta rahega) aur apni strategy coding ke liye alag `venv` banayein:

```bash
mkdir -p ~/trading-projects/openalgo-setup/my-option-algo
cd ~/trading-projects/openalgo-setup/my-option-algo

python3 -m venv venv
source venv/bin/activate

pip install openalgo pandas python-dotenv
code .

```

*(Yeh aapke VS Code ko directly is project folder mein open kar dega).*

---

Aap Task 1 se start kijiye—jaise hi koi step complete ho ya kahin koi terminal/Sahi redirect issue aaye, mujhe batayein, hum saath mein debug ya next coding step par chalenge.
