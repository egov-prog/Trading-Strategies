# SKILL: Ichimoku Hosoda Unified Strategy (Pine Script v6)

## Overview

This skill describes the full structure, logic, and AI-interaction patterns for the **"Ichimoku Kinko Hyo [Price + Wave + Time] (Hosoda Unified) Sonnet 4.6 Strategy"** written in Pine Script v6 by @troias.

Use this file as a reference prompt context when asking Claude (or any AI assistant) to:
- Debug or modify the strategy
- Generate optimised input parameters
- Explain any component
- Create backtest reports
- Build companion indicators

---

## Strategy Identity

| Field | Value |
|-------|-------|
| **Name** | Ichimoku Kinko Hyo [Price + Wave + Time] (Hosoda Unified) Sonnet 4.6 Strategy @v6 |
| **Author** | @troias |
| **Language** | Pine Script v6 |
| **Type** | `strategy()` — overlay=true |
| **Capital** | $10,000 initial, 100% equity, 0.04% commission, 2 tick slippage |
| **GitHub** | https://github.com/egov-prog/Trading-Strategies |

---

## Input Parameters (Full Reference)

| Input | Variable | Default | Type |
|-------|----------|---------|------|
| Tenkan-sen | `tenkanLen` | 9 | int |
| Kijun-sen | `kijunLen` | 26 | int |
| Senkou Span B | `senkouLen` | 52 | int |
| Displacement | `displace` | 26 | int |
| Show Time Cycles | `showTimeCycles` | true | bool |
| Time Cycle Filter | `useTimeCycleFilter` | false | bool |
| Long Only | `useLongOnly` | false | bool |
| Short Only | `useShortOnly` | false | bool |
| ATR Length | `atrLen` | 14 | int |
| SL ATR Multiplier | `slMult` | 1.5 | float |
| TP1 ATR Multiplier | `tp1Mult` | 2.0 | float |
| TP2 ATR Multiplier | `tp2Mult` | 4.0 | float |

---

## Core Components

### 1. Ichimoku Lines
```pine
tenkan = (highest(high, tenkanLen) + lowest(low, tenkanLen)) / 2
kijun  = (highest(high, kijunLen)  + lowest(low, kijunLen))  / 2
spanA  = (tenkan + kijun) / 2
spanB  = (highest(high, senkouLen) + lowest(low, senkouLen)) / 2
chikou = close
```

### 2. Phase Classification
```
aboveKumo  = close > max(spanA, spanB)
belowKumo  = close < min(spanA, spanB)
insideKumo = NOT aboveKumo AND NOT belowKumo  ← P-Wave zone
phaseBull  = aboveKumo AND tenkan > kijun
phaseBear  = belowKumo AND tenkan < kijun
```

### 3. Wave Theory
```
N-Wave Bull: close > kijun AND kijun > kijun[1]   ← trend continuation
N-Wave Bear: close < kijun AND kijun < kijun[1]
V-Wave Bull: 3-bar low AND close > close[1]         ← reversal up
V-Wave Bear: 3-bar high AND close < close[1]        ← reversal down
P-Wave:      insideKumo                              ← consolidation
```

### 4. Chikou Freedom
```
Bull: close > high[26]   ← Chikou Span has upward space
Bear: close < low[26]    ← Chikou Span has downward space
```

### 5. Time Theory Cycles
Fires when: `bar_index % N == 0` for N ∈ {9, 17, 26, 33, 42, 65}

### 6. Strong Signal (entry gate)
```
strongBull = phaseBull AND waveBullConfirm AND chikouFreeBull
strongBear = phaseBear AND waveBearConfirm AND chikouFreeBear
```

### 7. ATR Exit Structure
```
Long:   SL = close - ATR×slMult  |  TP1 = close + ATR×tp1Mult (50%)  |  TP2 = close + ATR×tp2Mult (100%)
Short:  SL = close + ATR×slMult  |  TP1 = close - ATR×tp1Mult (50%)  |  TP2 = close - ATR×tp2Mult (100%)
```

### 8. Signal Flip Close
```
bearFlip = position > 0 AND strongBear  → close('Long')
bullFlip = position < 0 AND strongBull  → close('Short')
```

---

## Recommended Inputs Quick Reference

| TF | T | K | SB | D | ATR | SL | TP1 | TP2 |
|----|---|---|----|---|-----|----|-----|-----|
| 1M | 7 | 22 | 44 | 22 | 10 | 1.0 | 1.5 | 3.0 |
| 5M | 9 | 26 | 52 | 26 | 12 | 1.2 | 1.8 | 3.5 |
| 15M | 9 | 26 | 52 | 26 | 14 | 1.5 | 2.0 | 4.0 |
| 30M | 9 | 26 | 52 | 26 | 14 | 1.5 | 2.0 | 4.5 |
| 1H | 9 | 26 | 52 | 26 | 14 | 1.5 | 2.0 | 5.0 |
| 4H | 9 | 26 | 52 | 26 | 14 | 2.0 | 2.5 | 5.0 |
| 1D | 9 | 26 | 52 | 26 | 14 | 2.0 | 3.0 | 6.0 |
| 1W | 9 | 26 | 52 | 26 | 14 | 2.5 | 3.0 | 7.0 |
| 1M | 5 | 13 | 26 | 13 | 10 | 3.0 | 3.0 | 8.0 |

---

## How to Use This Skill (AI Prompt Context)

When asking Claude to work on this strategy, paste this SKILL.md + the full Pine Script, then use one of these prompt patterns:

### Debugging
> "Using the SKILL.md context below, fix this Pine Script error: [error]. Return the full rewritten script."

### Optimisation
> "Based on the SKILL.md parameter table, optimise inputs for XAUUSD on the 1H timeframe. Justify each change."

### New Feature
> "Add a [feature] to this strategy. Follow the existing code structure from SKILL.md. Return the full rewritten script."

### Backtest Report
> "Analyse this backtest result [paste data] for the 15M timeframe. Compare against the SKILL.md expected parameters."

---

## Author Notes

- Always deliver **full rewritten script** after any modification — no partial snippets
- Prefer `line.new()` for SL/TP visuals (capped at ~50 bars) over `plot()` to avoid y-axis distortion
- Use `plot()` for Ichimoku lines with appropriate offsets
- Strategy uses `strategy.position_size` checks to prevent double entries
- Pine Script v6 — do not use v5 or earlier syntax

---

*© 2026 @troias · TRoy Group™*  
*Skill Version: 1.0 · April 2026*
