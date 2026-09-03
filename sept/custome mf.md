Bahut badiya! Sabhi 5 points par decisions crystal clear aur frozen ho chuke hain:

1. **Re-qualification:** Har subsequent tranche (T2 to T5) ke liye mandatory **fresh reference high → fresh ≥5% dip → fresh recovery → fresh 20 DMA & VWAP reclaim** cycle zaroori hai.
2. **Basket Exit:** **Tareeqa A (Active Pool)** — Active positions ka combined cost basis jab +6.5% hit karega, engine `CONSOLIDATED_TARGET_HIT` trigger karega aur cycle close hogi.
3. **Quarantine Logic (-20%):** Quarantined stock ko cut nahi karenge, alag bucket mein preserve rakhenge (Phase 2 ke liye). Agar woh recovery karke loss -20% se kam par le aata hai aur conditions banata hai, tabhi future consideration hoga.
4. **Data Provider:** **NSE tickers (`.NS`)** use honge Yahoo Finance ke through (e.g., `RELIANCE.NS`).
5. **Cash Utilization:** Chahe 25 me se 10 slots bhare hon ya 15, jaise hi active deployed capital par **+6.5% blended target** aayega, profit book hoga aur cycle reset hogi.
6. **Universe:** Strict **SENSEX 30 constituents** (30 liquid bluechip names).

Is updated architecture ko reflect karne ke liye hum Google Sheet aur Apps Script ka foundation code update karenge.

---

### Step 1: Apps Script code update karein

Apps Script editor mein jaakar do files ko update karna hai:

#### 1. `Config.gs` (Replace entire file)

```javascript
/**
 * Global Configuration & Schema Definitions — SENSEX 30 Basket Cycle Model V1.0
 */
const CONFIG = {
  VERSION: "1.0",
  UNIVERSE: "SENSEX 30",
  SIGNAL_TIME: "15:30",
  EXECUTION_WINDOW: { START: "09:45", END: "11:00" },
  
  // Capital & Basket Cycle Mechanics
  CYCLE_CAPITAL: 100000,          // Total cycle capital pool (₹1,00,000)
  SLOT_SIZE: 4000,                 // Size per entry action (₹4,000)
  TOTAL_CYCLE_SLOTS: 25,          // 25 slots of ₹4,000
  MAX_DISTINCT_STOCKS: 10,        // Max 10 distinct names per basket
  MAX_TRANCHES_PER_STOCK: 5,      // Max 5 slots (₹20,000) per individual symbol
  BASKET_TARGET_PERCENT: 6.5,     // Active pool blended exit trigger (+6.5%)
  QUARANTINE_THRESHOLD_PERCENT: -20.0, // Hard quarantine boundary (-20%)
  
  // Strategy Technicals
  DMA_PERIOD: 20,
  DMA50_PERIOD: 50,
  VWAP_METHOD: "PREVIOUS_SESSION",
  DIP_THRESHOLD_PERCENT: 5.0,
  DAILY_BUY_LIMIT: 5,             // Max 5 execution actions queued per day
  
  PRIMARY_DATA_SOURCE: "YAHOO_NSE",
  DATA_VALIDATION: true
};

const SHEET_SCHEMAS = {
  DASHBOARD: [],
  SETTINGS: ["Key", "Value", "Description", "Type"],
  WATCHLIST: ["Symbol", "Yahoo Ticker", "Active", "Sector", "Universe", "Universe As Of", "Universe Version"],
  MARKET_DATA: ["Date", "Time", "Symbol", "Open", "High", "Low", "Close", "Volume", "Data Source", "Data Timestamp"],
  INDICATORS: ["Date", "Symbol", "Close", "DMA20", "DMA20 Prior", "DMA50", "Previous Session VWAP", "Volume", "Average Volume", "Trend Status", "Dip Status", "Recovery Status", "DMA20 Reclaim", "VWAP Reclaim"],
  POSITIONS: ["Symbol", "Status", "Current Tranche", "Slots Used", "Total Invested", "Quantity", "Average Price", "Current Price", "Unrealized PnL %", "Basket Status", "Last Buy Date", "Next Eligible Tranche"],
  SIGNALS: ["Signal ID", "Signal Date", "Signal Time", "Execution Date", "Symbol", "Candidate Type", "Current Tranche", "Next Tranche", "Close", "DMA20", "DMA50", "Previous VWAP", "Dip", "Recovery", "DMA20 Reclaim", "VWAP Reclaim", "Trend", "Rank Score", "Rank", "Final Signal", "Reason", "Signal Status", "Frozen"],
  ACTION_QUEUE: ["Execution Date", "Symbol", "Action Type", "Tranche", "Slot Amount", "Rank", "Rank Score", "Signal ID", "Validity", "Action Status", "User Confirmation", "Execution ID"],
  TRADE_LOG: ["Execution ID", "Date", "Time", "Symbol", "Action", "Tranche", "Quantity", "Execution Price", "Gross Value", "Broker", "Signal ID", "User Confirmation", "Notes"],
  SIGNAL_HISTORY: ["Date", "Symbol", "Candidate Type", "Close", "DMA20", "DMA50", "VWAP", "Trend", "Rank Score", "Rank", "Final Signal", "Reason"],
  SYSTEM_AUDIT: ["Timestamp", "User", "Function", "Action", "Status", "Records Processed", "Result", "Error", "Execution Time (ms)"]
};

// SENSEX 30 Bluechips (NSE Tickers: .NS)
const SENSEX_30_UNIVERSE = [
  { symbol: "RELIANCE", ticker: "RELIANCE.NS", sector: "Energy" },
  { symbol: "TCS", ticker: "TCS.NS", sector: "Information Technology" },
  { symbol: "HDFCBANK", ticker: "HDFCBANK.NS", sector: "Financial Services" },
  { symbol: "ICICIBANK", ticker: "ICICIBANK.NS", sector: "Financial Services" },
  { symbol: "BHARTIARTL", ticker: "BHARTIARTL.NS", sector: "Telecommunication" },
  { symbol: "INFY", ticker: "INFY.NS", sector: "Information Technology" },
  { symbol: "SBIN", ticker: "SBIN.NS", sector: "Financial Services" },
  { symbol: "ITC", ticker: "ITC.NS", sector: "FMCG" },
  { symbol: "HINDUNILVR", ticker: "HINDUNILVR.NS", sector: "FMCG" },
  { symbol: "LT", ticker: "LT.NS", sector: "Capital Goods" },
  { symbol: "BAJFINANCE", ticker: "BAJFINANCE.NS", sector: "Financial Services" },
  { symbol: "HCLTECH", ticker: "HCLTECH.NS", sector: "Information Technology" },
  { symbol: "MARUTI", ticker: "MARUTI.NS", sector: "Automobile" },
  { symbol: "SUNPHARMA", ticker: "SUNPHARMA.NS", sector: "Healthcare" },
  { symbol: "ADANIENT", ticker: "ADANIENT.NS", sector: "Metals & Mining" },
  { symbol: "TATAMOTORS", ticker: "TATAMOTORS.NS", sector: "Automobile" },
  { symbol: "KOTAKBANK", ticker: "KOTAKBANK.NS", sector: "Financial Services" },
  { symbol: "NTPC", ticker: "NTPC.NS", sector: "Power" },
  { symbol: "TITAN", ticker: "TITAN.NS", sector: "Consumer Durables" },
  { symbol: "AXISBANK", ticker: "AXISBANK.NS", sector: "Financial Services" },
  { symbol: "ONGC", ticker: "ONGC.NS", sector: "Energy" },
  { symbol: "POWERGRID", ticker: "POWERGRID.NS", sector: "Power" },
  { symbol: "TATASTEEL", ticker: "TATASTEEL.NS", sector: "Metals & Mining" },
  { symbol: "M&M", ticker: "M&M.NS", sector: "Automobile" },
  { symbol: "ASIANPAINT", ticker: "ASIANPAINT.NS", sector: "Consumer Durables" },
  { symbol: "ULTRACEMCO", ticker: "ULTRACEMCO.NS", sector: "Construction Materials" },
  { symbol: "BAJAJFINSV", ticker: "BAJAJFINSV.NS", sector: "Financial Services" },
  { symbol: "NESTLEIND", ticker: "NESTLEIND.NS", sector: "FMCG" },
  { symbol: "TECHM", ticker: "TECHM.NS", sector: "Information Technology" },
  { symbol: "JSWSTEEL", ticker: "JSWSTEEL.NS", sector: "Metals & Mining" }
];

```

---

#### 2. `Setup.gs` (Replace entire file)

```javascript
/**
 * Master Setup Script — SENSEX 30 Basket Cycle Setup
 */
function runPhase1Setup() {
  const startTime = Date.now();
  const ss = SpreadsheetApp.getActiveSpreadsheet();

  try {
    const targetSheets = Object.keys(SHEET_SCHEMAS);
    targetSheets.forEach(sheetName => {
      let sheet = ss.getSheetByName(sheetName);
      if (!sheet) {
        sheet = ss.insertSheet(sheetName);
      }
    });

    const defaultSheet = ss.getSheetByName("Sheet1");
    if (defaultSheet && targetSheets.length > 0) {
      ss.deleteSheet(defaultSheet);
    }

    // Apply clean table headers
    targetSheets.forEach(sheetName => {
      const sheet = ss.getSheetByName(sheetName);
      const headers = SHEET_SCHEMAS[sheetName];

      if (headers.length > 0) {
        sheet.clear();
        sheet.getRange(1, 1, 1, headers.length).setValues([headers])
          .setFontWeight("bold")
          .setBackground("#1a365d")
          .setFontColor("#ffffff");
        sheet.setFrozenRows(1);
      }
    });

    // Populate SETTINGS Tab
    const settingsSheet = ss.getSheetByName("SETTINGS");
    settingsSheet.clear();
    const settingsHeaders = SHEET_SCHEMAS.SETTINGS;
    settingsSheet.getRange(1, 1, 1, settingsHeaders.length).setValues([settingsHeaders])
      .setFontWeight("bold").setBackground("#1a365d").setFontColor("#ffffff");

    const settingsRows = [
      ["SYSTEM_VERSION", CONFIG.VERSION, "Strategy engine release version", "STRING"],
      ["UNIVERSE", CONFIG.UNIVERSE, "Target scan universe", "STRING"],
      ["CYCLE_CAPITAL", CONFIG.CYCLE_CAPITAL, "Total basket cycle capital (INR)", "NUMBER"],
      ["SLOT_SIZE", CONFIG.SLOT_SIZE, "Size per tranche buy action (INR)", "NUMBER"],
      ["TOTAL_CYCLE_SLOTS", CONFIG.TOTAL_CYCLE_SLOTS, "Maximum total slots per cycle", "NUMBER"],
      ["MAX_DISTINCT_STOCKS", CONFIG.MAX_DISTINCT_STOCKS, "Maximum distinct stock names in basket", "NUMBER"],
      ["MAX_TRANCHES_PER_STOCK", CONFIG.MAX_TRANCHES_PER_STOCK, "Maximum tranches (slots) per individual stock", "NUMBER"],
      ["BASKET_TARGET_PERCENT", CONFIG.BASKET_TARGET_PERCENT, "Consolidated profit target for active pool (%)", "NUMBER"],
      ["QUARANTINE_THRESHOLD_PERCENT", CONFIG.QUARANTINE_THRESHOLD_PERCENT, "Unrealized loss threshold to quarantine position (%)", "NUMBER"],
      ["SIGNAL_TIME", CONFIG.SIGNAL_TIME, "EOD scan trigger time (IST)", "STRING"],
      ["EXECUTION_START", CONFIG.EXECUTION_WINDOW.START, "Morning execution window start", "STRING"],
      ["EXECUTION_END", CONFIG.EXECUTION_WINDOW.END, "Morning execution window end", "STRING"],
      ["DMA_PERIOD", CONFIG.DMA_PERIOD, "Short-term trend & reclaim moving average", "NUMBER"],
      ["DMA50_PERIOD", CONFIG.DMA50_PERIOD, "Medium-term trend filter moving average", "NUMBER"],
      ["VWAP_METHOD", CONFIG.VWAP_METHOD, "Reference VWAP calculation source", "STRING"],
      ["DIP_THRESHOLD_PERCENT", CONFIG.DIP_THRESHOLD_PERCENT, "Minimum dip % from reference high", "NUMBER"],
      ["DAILY_BUY_LIMIT", CONFIG.DAILY_BUY_LIMIT, "Maximum new BUY candidates queued per day", "NUMBER"],
      ["PRIMARY_DATA_SOURCE", CONFIG.PRIMARY_DATA_SOURCE, "EOD price feed provider (NSE on Yahoo)", "STRING"],
      ["DATA_VALIDATION", CONFIG.DATA_VALIDATION, "Enforce strict data validation prior to scan", "BOOLEAN"]
    ];
    settingsSheet.getRange(2, 1, settingsRows.length, settingsRows[0].length).setValues(settingsRows);
    settingsSheet.setFrozenRows(1);

    // Populate WATCHLIST Tab with SENSEX 30 constituents
    const watchlistSheet = ss.getSheetByName("WATCHLIST");
    const todayStr = Utilities.formatDate(new Date(), "Asia/Kolkata", "yyyy-MM-dd");
    const watchlistRows = SENSEX_30_UNIVERSE.map(item => [
      item.symbol,
      item.ticker,
      "YES",
      item.sector,
      "SENSEX 30",
      todayStr,
      "V1.0"
    ]);
    watchlistSheet.getRange(2, 1, watchlistRows.length, watchlistRows[0].length).setValues(watchlistRows);

    // Render Dashboard
    renderDashboardLayout(ss.getSheetByName("DASHBOARD"));

    logAudit("runPhase1Setup", "SETUP_PHASE_1", "SUCCESS", targetSheets.length, "Initialized 11 tabs for SENSEX 30 Basket Cycle", "", Date.now() - startTime);
    SpreadsheetApp.getUi().alert("SENSEX 30 Basket Cycle Setup Completed Successfully!");
  } catch (err) {
    logAudit("runPhase1Setup", "SETUP_PHASE_1", "FAILED", 0, "", err.message, Date.now() - startTime);
    SpreadsheetApp.getUi().alert("Setup failed: " + err.message);
  }
}

/**
 * Builds the Dashboard with Basket metrics & Active Pool Tracking
 */
function renderDashboardLayout(sheet) {
  sheet.clear();
  sheet.setGridlines(false);

  // Header Banner
  sheet.getRange("B2:H2").merge()
    .setValue("SENSEX 30 BASKET CYCLE ENGINE — V1.0")
    .setFontWeight("bold").setFontSize(14)
    .setBackground("#1a365d").setFontColor("#ffffff")
    .setHorizontalAlignment("center").setVerticalAlignment("middle");
  sheet.setRowHeight(2, 38);

  // Parameter Info Box
  const params = [
    ["Cycle Model:", "10-Stock Basket Cycle", "Active Pool Target:", "+6.5% Combined Gain"],
    ["Cycle Capital:", "₹1,00,000 (25 Slots @ ₹4,000)", "Quarantine Threshold:", "-20% (Freeze Averaging)"],
    ["Universe:", "SENSEX 30 (NSE Feed)", "Max Tranches/Stock:", "5 Slots (₹20,000)"],
    ["Priority Rule:", "Diversify First (up to 10), then Average", "Daily Action Limit:", "Max 5 Orders/Day"]
  ];
  sheet.getRange("B4:G7").setValues(params);
  sheet.getRange("B4:B7").setFontWeight("bold").setFontColor("#4a5568");
  sheet.getRange("E4:E7").setFontWeight("bold").setFontColor("#4a5568");
  sheet.getRange("C4:C7").setFontWeight("bold").setFontColor("#2b6cb0");
  sheet.getRange("F4:F7").setFontWeight("bold").setFontColor("#2b6cb0");

  // Real-time Basket Summary Cards
  sheet.getRange("B9:G9").merge().setValue("ACTIVE BASKET STATUS & METRICS")
    .setFontWeight("bold").setBackground("#edf2f7").setHorizontalAlignment("center");

  const metricHeaders = [
    "Distinct Names", "Slots Used", "Active Invested", "Quarantined Invested", "Active Pool PnL %", "Exit Signal"
  ];
  const metricFormulas = [
    '=IFERROR(COUNTIF(POSITIONS!B2:B, "OPEN"), 0) & " / 10"',
    '=IFERROR(SUM(POSITIONS!D2:D), 0) & " / 25"',
    '=IFERROR(SUMIF(POSITIONS!J2:J, "ACTIVE", POSITIONS!E2:E), 0)',
    '=IFERROR(SUMIF(POSITIONS!J2:J, "QUARANTINED", POSITIONS!E2:E), 0)',
    '=IFERROR(AVERAGEIF(POSITIONS!J2:J, "ACTIVE", POSITIONS!I2:I), 0%)',
    '=IF(AVERAGEIF(POSITIONS!J2:J, "ACTIVE", POSITIONS!I2:I) >= 0.065, "CONSOLIDATED_TARGET_HIT", "RUNNING")'
  ];

  sheet.getRange("B11:G11").setValues([metricHeaders]).setFontWeight("bold").setBackground("#e2e8f0").setHorizontalAlignment("center");
  sheet.getRange("B12:G12").setValues([metricFormulas]).setFontWeight("bold").setFontSize(12).setHorizontalAlignment("center");

  sheet.setColumnWidth(1, 20);
  sheet.setColumnWidth(2, 140);
  sheet.setColumnWidth(3, 140);
  sheet.setColumnWidth(4, 150);
  sheet.setColumnWidth(5, 170);
  sheet.setColumnWidth(6, 150);
  sheet.setColumnWidth(7, 180);
}

```

---

### Step 2: Run & Verify

1. Apps Script editor mein toolbar se **`runPhase1Setup`** select karein aur **Run** par click karein.
2. Google Sheet par wapas aayein:
* **`WATCHLIST`** tab dekhein: Exactly 30 liquid SENSEX stocks hone chahiye `.NS` format mein (e.g. `RELIANCE.NS`, `TCS.NS`, `INFY.NS`).
* **`SETTINGS`** tab dekhein: Cycle Capital (`₹1,00,000`), Slot Size (`₹4,000`), Target (`6.5%`), aur Quarantine (`-20%`) correctly loaded hone chahiye.
* **`DASHBOARD`** tab dekhein: Basket tracking metrics (Distinct Names `/ 10`, Slots Used `/ 25`, Active vs Quarantined Invested, Active Pool PnL %, aur Target Hit Status) show hone lagenge.



Setup run karke verify kar lijiye. Jaise hi aap confirm karenge, hum **Phase 2 — Market Data Engine (Yahoo Finance NSE fetcher + Caching + Insufficient Data Handling)** shuru karenge!
