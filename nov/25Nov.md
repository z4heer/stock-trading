### Root Cause (100% Clear Now)

1. **Why WebSocket returned `0/30`:** In OpenAlgo, `client.get_quotes(...)` only reads from OpenAlgo's internal WebSocket cache **if** OpenAlgo's server-side broker WebSocket (`Fyers` data socket) has actively subscribed and received ticks for those symbols—and `broker.ensure_subscribed()` only fires a client-side `subscribe_quote()` callback without waiting for OpenAlgo's Fyers data socket to register all 30 symbols.
2. **Why `multiquotes` took `7.26s` earlier:** Remember when OpenAlgo restarted at `12:23 PM`? Look at what happened earlier:
* `HDFCBANK` `multiquotes` returned `{'results': [], 'status': 'success'} (7.26s)`—meaning **OpenAlgo's Fyers Data API session/token on the OpenAlgo server is currently rate-limited or disconnected from Fyers' quote/depth feed** (which is why BOTH `BFO` options AND `NSE` equities returned `'results': []` after `7.26s`!).
* Meanwhile, earlier **before** `12:23 PM`, `equity_screener.py` worked cleanly and selected `BAJFINANCE`, `ITC`, `HDFCBANK`.



---

### 1. Run This 1-Second OpenAlgo Health Check to Confirm

```bash
cd ~/trading-projects/option-trailing-algo && source venv/bin/activate
python3 -c 'import broker_adapter; b = broker_adapter.BrokerAdapter(); print("Quotes:", b.client.quotes(symbol="RELIANCE", exchange="NSE")); print("Depth:", b.client.depth(symbol="RELIANCE", exchange="NSE")); b.shutdown()'

```

* If both return empty or error right now, it confirms **OpenAlgo's Fyers Data Socket / REST session needs a clean restart** (or Fyers' daily/minute Data API rate-limit bucket cooled down after restarting OpenAlgo).

---

### 2. Clean Hybrid Fix for `equity_screener.py` (WebSocket First $\rightarrow$ `broker.client.multiquotes` Fallback)

Keep `equity_screener.py` clean and resilient so it checks WebSocket first (`2.0s`) and, if OpenAlgo's WebSocket cache isn't populated yet, falls back to `broker.client.multiquotes` in two 15-symbol chunks (`timeout=15` via `urllib`):

```python
import datetime
import json
import time
import urllib.request
import broker_adapter
import config

# Historical average daily volume baseline for Sensex 30 constituents
SENSEX_30_BASE_VOL = {
    "HDFCBANK": 15000000,
    "ICICIBANK": 12000000,
    "RELIANCE": 8000000,
    "BHARTIARTL": 6000000,
    "LT": 2500000,
    "SBIN": 14000000,
    "INFY": 7000000,
    "AXISBANK": 8500000,
    "KOTAKBANK": 4500000,
    "BAJFINANCE": 1200000,
    "M&M": 3000000,
    "ITC": 13000000,
    "ETERNAL": 25000000,
    "TCS": 2500000,
    "SUNPHARMA": 2500000,
    "TITAN": 1200000,
    "HINDUNILVR": 2000000,
    "TATASTEEL": 30000000,
    "MARUTI": 600000,
    "NTPC": 12000000,
    "ADANIPORTS": 4000000,
    "BEL": 15000000,
    "HCLTECH": 3000000,
    "ULTRACEMCO": 400000,
    "POWERGRID": 11000000,
    "INDIGO": 1000000,
    "ASIANPAINT": 1500000,
    "BAJAJFINSV": 1800000,
    "TECHM": 2200000,
    "TRENT": 1000000,
}


def _extract_prev_close(q: dict) -> float:
    """Safely extracts prior close across OpenAlgo/Fyers field variations."""
    return float(
        q.get("prev_close")
        or q.get("prevclose")
        or q.get("close")
        or q.get("open")
        or 0
    )


def _compute_rvol(symbol: str, volume: float) -> float:
    """Computes time-of-day normalized Relative Volume (RVOL)."""
    now = datetime.datetime.now()
    market_open = now.replace(hour=9, minute=15, second=0, microsecond=0)
    if now <= market_open:
        return 1.0

    elapsed_minutes = max(1.0, (now - market_open).total_seconds() / 60.0)
    elapsed_fraction = min(1.0, elapsed_minutes / 375.0)

    base_vol = SENSEX_30_BASE_VOL.get(symbol, 3000000)
    expected_vol = base_vol * elapsed_fraction

    if expected_vol > 0 and volume > 0:
        return round(volume / expected_vol, 2)
    return 1.0


def _fetch_sensex30_quotes(broker, symbols: list[str], exchange: str) -> dict:
    """Fetches Sensex-30 quotes via WebSocket cache first, then chunked multiquotes REST fallback."""
    if getattr(broker, "mode", "") != "OPENALGO":
        try:
            fallback = broker.get_multiquotes(symbols, exchange)
            return fallback if isinstance(fallback, dict) else {}
        except Exception as e:
            print(f"[SCREENER WARN] Simulation multiquotes failed: {e}")
            return {}

    quotes_map = {}

    # 1. Fast WebSocket cache check (2.0s warm-up)
    if getattr(broker, "ws_connected", False):
        for sym in symbols:
            broker.ensure_subscribed(sym, exchange)
        time.sleep(2.0)
        for sym in symbols:
            try:
                ws_q = broker.client.get_quotes(symbol=sym, exchange=exchange)
                data = ws_q.get("data", ws_q) if isinstance(ws_q, dict) else None
                if isinstance(data, dict) and float(data.get("ltp", 0) or 0) > 0:
                    quotes_map[sym] = data
            except Exception:
                pass

    missing = [s for s in symbols if s not in quotes_map]
    if not missing:
        return quotes_map

    # 2. Chunked REST multiquotes fallback for any missing symbols (15 per batch, 15s timeout)
    host = getattr(config, "OPENALGO_HOST", "http://127.0.0.1:5000").rstrip("/")
    api_key = getattr(config, "OPENALGO_API_KEY", "")
    url = f"{host}/api/v1/multiquotes"

    chunk_size = 15
    for i in range(0, len(missing), chunk_size):
        chunk = missing[i : i + chunk_size]
        payload = json.dumps({
            "apikey": api_key,
            "symbols": [{"symbol": s, "exchange": exchange} for s in chunk],
        }).encode("utf-8")
        req = urllib.request.Request(
            url,
            data=payload,
            headers={"Content-Type": "application/json"},
            method="POST",
        )
        try:
            with urllib.request.urlopen(req, timeout=15) as resp:
                res = json.loads(resp.read().decode("utf-8"))
            if isinstance(res, dict) and res.get("status") == "success":
                for item in res.get("results") or []:
                    if isinstance(item, dict) and item.get("symbol") and isinstance(item.get("data"), dict):
                        quotes_map[item["symbol"]] = item["data"]
        except Exception as e:
            print(f"[SCREENER WARN] REST batch {i // chunk_size + 1} failed: {e}")

        if i + chunk_size < len(missing):
            time.sleep(0.5)

    return quotes_map


def select_trending_sensex_stocks(broker):
    """Checks Sensex-30 basket market regime and selects top 3 trending stocks.

    Returns:
        tuple: (regime, direction, selected_symbols)
    """
    print("\n" + "=" * 84)
    print("   NSE CASH EQUITY INTRADAY MOMENTUM ENGINE (V1.0) - SENSEX 30 SCREENER")
    print("=" * 84)

    trend_threshold = getattr(config, "MARKET_TREND_THRESHOLD_PCT", 0.30)
    symbols = getattr(config, "SENSEX_30_SYMBOLS", list(SENSEX_30_BASE_VOL.keys()))
    exchange = getattr(config, "EQUITY_EXCHANGE", "NSE")
    stock_min_move = getattr(config, "STOCK_MIN_MOVE_PCT", 0.80)
    max_notional = getattr(config, "EQUITY_MAX_NOTIONAL", 25000.0)

    quotes_map = _fetch_sensex30_quotes(broker, symbols, exchange)

    pct_moves = []
    for sym, q in quotes_map.items():
        if not isinstance(q, dict):
            continue
        ltp = float(q.get("ltp", 0) or 0)
        prev = _extract_prev_close(q)
        if ltp > 0 and prev > 0:
            pct_moves.append(((ltp - prev) / prev) * 100.0)

    if len(pct_moves) < 15:
        print(
            f"[ERROR] Insufficient Sensex-30 breadth ({len(pct_moves)}/30 valid quotes). Standing down."
        )
        print("[TIP] If OpenAlgo was restarted mid-day, restart OpenAlgo cleanly and verify broker login.")
        return "SIDEWAYS", "NONE", []

    index_pct_change = sum(pct_moves) / len(pct_moves)
    advancers = sum(1 for m in pct_moves if m > 0)
    decliners = sum(1 for m in pct_moves if m < 0)

    print(
        f"[MARKET REGIME] SENSEX-30 Basket ({len(pct_moves)}/30): "
        f"Avg Move {index_pct_change:+.2f}% | Adv: {advancers} / Dec: {decliners}"
    )

    if index_pct_change >= trend_threshold:
        regime = "BULLISH"
        direction = "LONG"
    elif index_pct_change <= -trend_threshold:
        regime = "BEARISH"
        direction = "SHORT"
    else:
        regime = "SIDEWAYS"
        direction = "NONE"

    print(f"[REGIME VERDICT] {regime} (Direction: {direction} | Threshold: ±{trend_threshold:.2f}%)")

    if regime == "SIDEWAYS":
        print(f"\n[ACTION REQUIRED] Standing down: SENSEX-30 basket is sideways (< ±{trend_threshold:.2f}%). No trades.")
        print("=" * 84)
        return regime, direction, []

    max_affordable_price = max_notional / 5.0
    candidates = []

    for sym in symbols:
        q = quotes_map.get(sym)
        if not isinstance(q, dict):
            continue

        ltp = float(q.get("ltp", 0) or 0)
        prev_close = _extract_prev_close(q)
        bid = float(q.get("bid", 0) or 0)
        ask = float(q.get("ask", 0) or 0)
        volume = float(q.get("volume", 0) or 0)

        if ltp <= 0 or prev_close <= 0:
            continue

        if bid <= 0 or ask <= 0 or ask < bid:
            continue

        spread_pct = ((ask - bid) / ltp) * 100.0
        if spread_pct > 0.10:
            continue

        if ltp > max_affordable_price:
            continue

        pct_change = ((ltp - prev_close) / prev_close) * 100.0
        rvol = _compute_rvol(sym, volume)

        if regime == "BULLISH" and pct_change >= stock_min_move:
            score = round(pct_change * max(rvol, 0.5), 2)
            candidates.append({
                "symbol": sym,
                "ltp": ltp,
                "pct_change": pct_change,
                "volume": int(volume),
                "rvol": rvol,
                "spread_pct": spread_pct,
                "direction": "LONG",
                "score": score,
            })
        elif regime == "BEARISH" and pct_change <= -stock_min_move:
            score = round(abs(pct_change) * max(rvol, 0.5), 2)
            candidates.append({
                "symbol": sym,
                "ltp": ltp,
                "pct_change": pct_change,
                "volume": int(volume),
                "rvol": rvol,
                "spread_pct": spread_pct,
                "direction": "SHORT",
                "score": score,
            })

    candidates.sort(key=lambda x: x["score"], reverse=True)
    top_3 = candidates[:3]

    if not top_3:
        print(f"\n[NOTE] No Sensex-30 stocks met >= ±{stock_min_move:.2f}% move with <= 0.10% spread & <= ₹{max_affordable_price:.0f} LTP.")
        print("=" * 84)
        return regime, direction, []

    print(f"\n{'SYMBOL':<14} | {'LTP':>9} | {'% CHG':>7} | {'VOLUME':>10} | {'RVOL':>5} | {'SPREAD %':>8} | {'DIR':<5} | {'SCORE':>6}")
    print("-" * 84)
    for c in top_3:
        print(
            f"{c['symbol']:<14} | ₹{c['ltp']:>8.2f} | {c['pct_change']:>+6.2f}% | "
            f"{c['volume']:>10,d} | {c['rvol']:>4.1f}x | {c['spread_pct']:>7.3f}% | "
            f"{c['direction']:<5} | {c['score']:>6.2f}"
        )

    selected_symbols = [c["symbol"] for c in top_3]
    print("\n" + "=" * 84)
    print(f"SELECTED TOP {len(selected_symbols)} ({direction}): {selected_symbols}")
    print("=" * 84 + "\n")

    return regime, direction, selected_symbols


if __name__ == "__main__":
    broker = broker_adapter.BrokerAdapter()
    try:
        select_trending_sensex_stocks(broker)
    finally:
        broker.shutdown()

```

### Summary of What to Do Now

1. Save this hybrid `equity_screener.py` and run the 1-line `RELIANCE` check (`Quotes:` and `Depth:`)—if `RELIANCE` returns empty, simply restart OpenAlgo cleanly (`python3 app.py` in `~/trading-projects/openalgo` inside its own `tmux` session) and re-run `python3 equity_screener.py`.
2. Commit and push `equity_screener.py` to `dev`:
```bash
git add equity_screener.py && git commit -m "Fix: Hybrid WebSocket + 15-symbol chunked REST fallback in equity_screener.py" && git push origin dev

```
