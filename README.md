# Ichimoku Kinko Hyo — Hosoda Unified Strategy
### `[Price + Wave + Time]` · Pine Script v6 · by [@troias](https://github.com/egov-prog)

![TradingView](https://img.shields.io/badge/TradingView-Pine_Script_v6-blue?logo=tradingview)
![License](https://img.shields.io/badge/License-MIT-green)
![Version](https://img.shields.io/badge/Version-Sonnet_4.6-orange)

---

## 📌 Overview

This strategy is a **full Hosoda Unified implementation** of Ichimoku Kinko Hyo that combines all three pillars of the original theory:

| Pillar | Component | Purpose |
|--------|-----------|---------|
| **Price Theory** | Kumo (Cloud), Tenkan/Kijun cross, Chikou Span | Trend direction & phase classification |
| **Wave Theory** | N-Wave, V-Wave, P-Wave detection | Entry quality confirmation |
| **Time Theory** | Cycles: 9, 17, 26, 33, 42, 65 | Optional timing filter for high-probability entries |

Unlike simplified Ichimoku implementations, this strategy enforces **all three conditions simultaneously** before triggering an entry — producing fewer but higher-quality signals.

---

## ⚙️ Strategy Logic

### Phase Classification

| Phase | Condition | Meaning |
|-------|-----------|---------|
| **Bull Phase** | Price above Kumo AND Tenkan > Kijun | Confirmed uptrend |
| **Bear Phase** | Price below Kumo AND Tenkan < Kijun | Confirmed downtrend |
| **P-Wave** | Price inside Kumo | Consolidation / avoid trading |

### Chikou Freedom Filter
- **Bull:** `close > high[26]` — Chikou Span has clear space above historical price
- **Bear:** `close < low[26]` — Chikou Span has clear space below historical price

### Wave Theory Confirmation
| Wave | Bull Signal | Bear Signal |
|------|-------------|-------------|
| **N-Wave** | Close > Kijun AND Kijun rising | Close < Kijun AND Kijun falling |
| **V-Wave** | 3-bar low with close > prev close | 3-bar high with close < prev close |

### Strong Signal = Phase + Wave + Chikou (all three required)

### Entry Triggers
- **Long:** `strongBull` — optionally filtered by Time Cycle
- **Short:** `strongBear` — optionally filtered by Time Cycle
- **Flip close:** If position is open and opposite strong signal fires, position is closed immediately

### Exit Structure (Dual TP + ATR Stop)
```
Long:  SL = Entry - (ATR × slMult)   |  TP1 = Entry + (ATR × tp1Mult) [50%]  |  TP2 = Entry + (ATR × tp2Mult) [100%]
Short: SL = Entry + (ATR × slMult)   |  TP1 = Entry - (ATR × tp1Mult) [50%]  |  TP2 = Entry - (ATR × tp2Mult) [100%]
```

---

## 📊 Optimised Inputs by Timeframe

> **Legend:**  
> `T` = Tenkan-sen | `K` = Kijun-sen | `SB` = Senkou Span B | `D` = Displacement  
> `ATR` = ATR Length | `SL` = SL Multiplier | `TP1` = TP1 Mult | `TP2` = TP2 Mult

### 🔴 1 Minute
> ⚠️ High noise. Aggressive scalping only. Recommend Long Only during clear macro trends.

| Param | Value | Rationale |
|-------|-------|-----------|
| Tenkan | **7** | Faster reaction to 1-min price swings |
| Kijun | **22** | Reduces lag vs default 26 |
| Senkou B | **44** | Proportional to Kijun |
| Displacement | **22** | Matches Kijun |
| ATR Length | **10** | Tighter ATR for micro price action |
| SL Mult | **1.0** | Tight stop essential at 1M |
| TP1 Mult | **1.5** | Quick partial exit |
| TP2 Mult | **3.0** | Realistic extension target |
| Time Cycle Filter | **OFF** | Too many missed entries at 1M |
| Recommended Mode | **Long Only** (trending sessions) |
| Best Instruments | Crypto (BTC, ETH), Major Forex pairs |

---

### 🟠 5 Minute
> Scalping / intraday. Use during London or NY session open for best results.

| Param | Value | Rationale |
|-------|-------|-----------|
| Tenkan | **9** | Standard Hosoda |
| Kijun | **26** | Classic setting |
| Senkou B | **52** | Classic setting |
| Displacement | **26** | Classic setting |
| ATR Length | **12** | Balanced for 5M volatility |
| SL Mult | **1.2** | Slightly tighter than default |
| TP1 Mult | **1.8** | Scalper's partial |
| TP2 Mult | **3.5** | Extended runner |
| Time Cycle Filter | **Optional** | Enhances signal quality |
| Best Instruments | Forex majors, Gold (XAUUSD), BTC/USDT |

---

### 🟡 15 Minute
> Intraday swing entries. Best balance of signal frequency and quality.

| Param | Value | Rationale |
|-------|-------|-----------|
| Tenkan | **9** | Standard |
| Kijun | **26** | Standard |
| Senkou B | **52** | Standard |
| Displacement | **26** | Standard |
| ATR Length | **14** | Default — works well at 15M |
| SL Mult | **1.5** | Standard |
| TP1 Mult | **2.0** | Standard |
| TP2 Mult | **4.0** | Standard |
| Time Cycle Filter | **ON** | Noticeably improves W/L ratio at 15M |
| Best Instruments | Forex, Indices (NAS100, SPX), Crypto |

---

### 🟢 30 Minute
> Short-term swing. Fewer signals, better quality. Suitable for part-time monitoring.

| Param | Value | Rationale |
|-------|-------|-----------|
| Tenkan | **9** | Standard |
| Kijun | **26** | Standard |
| Senkou B | **52** | Standard |
| Displacement | **26** | Standard |
| ATR Length | **14** | Standard |
| SL Mult | **1.5** | Standard |
| TP1 Mult | **2.0** | Standard |
| TP2 Mult | **4.5** | Slightly extended — 30M moves more |
| Time Cycle Filter | **ON** | Recommended |
| Best Instruments | All majors, Commodities, Crypto |

---

### 🔵 1 Hour
> Classic Ichimoku timeframe. Most community backtesting done here. Solid signal quality.

| Param | Value | Rationale |
|-------|-------|-----------|
| Tenkan | **9** | Standard |
| Kijun | **26** | Standard |
| Senkou B | **52** | Standard |
| Displacement | **26** | Standard |
| ATR Length | **14** | Standard |
| SL Mult | **1.5** | Standard |
| TP1 Mult | **2.0** | Standard |
| TP2 Mult | **5.0** | Extended — 1H trends carry well |
| Time Cycle Filter | **ON** | High value at 1H |
| Best Instruments | EURUSD, USDJPY, XAUUSD, BTC/USD, NAS100 |

---

### 🟣 4 Hour
> Swing trading. Kumo is very reliable here. Expect 2–6 signals per month per pair.

| Param | Value | Rationale |
|-------|-------|-----------|
| Tenkan | **9** | Standard |
| Kijun | **26** | Standard |
| Senkou B | **52** | Standard |
| Displacement | **26** | Standard |
| ATR Length | **14** | Standard |
| SL Mult | **2.0** | Wider SL needed — 4H candles are large |
| TP1 Mult | **2.5** | Standard |
| TP2 Mult | **5.0** | Realistic swing extension |
| Time Cycle Filter | **ON** | Strong alignment at 4H |
| Best Instruments | All major pairs, BTC, Gold, Indices |

---

### ⚫ 1 Day (Daily)
> Hosoda's primary timeframe. Original 9/26/52/26 was designed for Japanese 6-day weekly markets. For modern 5-day markets some analysts prefer 7/22/44/22.

| Param | Value | Rationale |
|-------|-------|-----------|
| Tenkan | **9** | Classic |
| Kijun | **26** | Classic (≈ 1 month) |
| Senkou B | **52** | Classic (≈ 2 months) |
| Displacement | **26** | Classic |
| ATR Length | **14** | Standard |
| SL Mult | **2.0** | Position trade — allow breathing room |
| TP1 Mult | **3.0** | Daily swings are substantial |
| TP2 Mult | **6.0** | Trend-following extension |
| Time Cycle Filter | **ON** | Especially powerful at Daily — Hosoda's 9, 26, 33, 42, 65 day counts are most meaningful here |
| Best Instruments | All — this is the primary Hosoda timeframe |

---

### 🟤 1 Week
> Long-term position trading. Very few signals — each one significant.

| Param | Value | Rationale |
|-------|-------|-----------|
| Tenkan | **9** | Standard |
| Kijun | **26** | Standard |
| Senkou B | **52** | Standard |
| Displacement | **26** | Standard |
| ATR Length | **14** | Standard |
| SL Mult | **2.5** | Large weekly candles demand wide stops |
| TP1 Mult | **3.0** | Partial exit at key weekly level |
| TP2 Mult | **7.0** | Multi-month trend runner |
| Time Cycle Filter | **OFF** | Weekly cycles don't map cleanly to bar_index % n |
| Best Instruments | BTC, Gold, Major Indices, Blue-chip Equities |

---

### 🔶 1 Month
> Macro / investment grade signals. Use for directional bias confirmation only — not active trading.

| Param | Value | Rationale |
|-------|-------|-----------|
| Tenkan | **5** | Reduced — fewer bars available |
| Kijun | **13** | Proportional |
| Senkou B | **26** | Proportional |
| Displacement | **13** | Proportional |
| ATR Length | **10** | Less ATR data available |
| SL Mult | **3.0** | Macro moves need very wide stops |
| TP1 Mult | **3.0** | Partial exit |
| TP2 Mult | **8.0** | Multi-year trend runner |
| Time Cycle Filter | **OFF** | N/A at monthly |
| Best Instruments | BTC (4-year cycles), Gold, S&P500 |

---

## ⚠️ Known Weaknesses & Mitigation

| Timeframe | Weakness | Mitigation |
|-----------|----------|------------|
| **1M / 5M** | Heavy whipsaw — cloud too slow for micro price action | Use Long Only in trending sessions; add ADX > 25 filter externally |
| **15M / 30M** | Late entries after fast V-Wave reversals | Enable Time Cycle Filter; reduce Tenkan to 7 |
| **1H / 4H** | Kumo twist lag — price can travel far before cloud confirms | Watch for TK cross AHEAD of kumo for early signal |
| **1D** | Signal flip close can exit profitable positions prematurely | Raise TP2 Mult; consider disabling flip close on daily |
| **1W / 1M** | Very few signals — months without trades possible | Use as directional bias only, not standalone |
| **All TFs** | P-Wave (kumo) periods produce no signals — can miss breakout | Monitor insideKumo state; prepare entry order on cloud breakout |

---

## 🛡️ Risk Management

- **Position sizing:** 100% equity per trade (TradingView default) — **reduce to 1–3% risk per trade in live trading**
- **Commission:** 0.04% per side (realistic for crypto/forex brokers)
- **Slippage:** 2 ticks built in
- **TP1 exits 50% of position** — locking in partial profit and moving risk to "house money"
- **TP2 runs the remainder** — capturing trend extension
- **Never trade P-Wave (kumo) periods** — flat until price resolves above or below

---

## 📋 Changelog

| Version | Date | Notes |
|---------|------|-------|
| Sonnet 4.6 | 2026-03 | Current production version. Full Hosoda Unified implementation. |
| v1.0 | 2026-02 | Initial Ichimoku + ATR strategy |

---

## 📂 Repository Structure

```
Trading-Strategies/
├── README.md                          ← This file
├── ichimoku-hosoda-unified/
│   ├── strategy.pine                  ← Full Pine Script v6 source
│   ├── SKILL.md                       ← AI reusable skills reference
│   └── screenshots/                   ← Backtest screenshots per TF
└── skills/
    └── ichimoku-hosoda/
        └── SKILL.md
```

---

## ⚖️ Disclaimer

> This strategy is for **educational and research purposes only**. Past backtested performance does not guarantee future results. Trading involves substantial risk of loss. Always apply proper position sizing and risk management. The author (@troias / TRoy Group™) accepts no liability for trading losses.

---

*© 2026 @troias · TRoy Group™ · Darwin, NT, Australia*  
*Pine Script v6 · Ichimoku Kinko Hyo Hosoda Unified Strategy*
