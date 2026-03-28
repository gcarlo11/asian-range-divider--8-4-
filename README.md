# Asia Range Divider [8+4 Levels]

## Overview

**Asia Range Divider [8+4 Levels]** is a TradingView Pine Script indicator that automatically detects the Asian trading session (GMT+7) and divides the session range into **8 equal levels**, plus **4 extension levels** above and below. The indicator generates automated trading signals when price breaks levels with confirmation filters (Volume, ATR, RSI), providing a complete trade management workflow with Take Profit and Stop Loss targets.

**Key Concept:** All levels are equidistant—this means Risk-to-Reward ratio is always **1:1**, making position sizing and risk management straightforward.

---

## Features

### 🕐 Session Detection
- **Automatic Asia Session Detection** (GMT+7): Defaults to 07:00–15:00 GMT+7—customize as needed
- **Session Box Visualization**: Shows the session high/low with a visual overlay
- **Post-Session Signals**: Signals only fire after the session closes, preventing mid-session noise

### 📊 Range Division
- **8 Core Levels (L0–L8)**
  - **L0** = Asia Session Low (Strong Demand Zone)
  - **L4** = Middle (Neutral)
  - **L8** = Asia Session High (Strong Supply Zone)
  - **L1–L3, L5–L7** = Intermediate levels

- **4 Extension Levels** (Bottom & Top)
  - **EXT-1 to EXT-4** (below L0): Demand extensions
  - **EXT+1 to EXT+4** (above L8): Supply extensions

### ✅ Confirmation Filters
Three independent filters ensure signal quality:

1. **Volume Filter**
   - Compares current volume against its moving average
   - Default: 20-bar SMA
   - Filters out low-volume breakouts

2. **ATR Filter**
   - Ensures volatility is sufficient
   - Default: 14-period ATR vs. auto threshold (50-bar ATR SMA × 0.5)
   - Prevents false signals in low-volatility conditions

3. **RSI Filter**
   - **Long Signals**: Only when RSI > 50 (bullish momentum)
   - **Short Signals**: Only when RSI < 50 (bearish momentum)
   - Default: 14 periods

### 🎯 Trade Management
- **Entry**: Level breakout with all 3 confirmations pass
- **Take Profit (TP)**: Next level up (for longs) / down (for shorts)
- **Stop Loss (SL)**: Previous level down (for longs) / up (for shorts)
- **Visual TP/SL Lines**: Green dashed line (TP), Red dashed line (SL)
- **Trade Lock Option**: Prevents overlapping signals until TP or SL is hit
- **Session Reset Option**: Clears trade lock at the start of each new Asia session

---

## Input Parameters

### 📅 Asia Session
| Parameter | Default | Description |
|-----------|---------|-------------|
| **Session Time (GMT+7)** | 0700-1500 | Format: HHMM-HHMM. Change to match your desired session hours. |
| **Show Range Levels** | ON | Display the 8 core levels (L0–L8). |
| **Show Extension Levels** | ON | Display the 4 extension levels (EXT±1 to ±4). |
| **Show Session Box** | ON | Display the session high/low box overlay. |
| **Show Past Session History** | OFF | ON = all past sessions stay visible. OFF = only the latest session is shown. |

### ⚙️ Confirmation Filters
| Parameter | Default | Description |
|-----------|---------|-------------|
| **Volume Filter** | ON | Require volume > 20-bar MA for entry. |
| **Volume MA Length** | 20 | Period for volume moving average. |
| **ATR Filter** | ON | Require ATR > threshold (auto or manual). |
| **ATR Length** | 14 | Period for ATR calculation. |
| **ATR Min Threshold** | 0 (auto) | Set custom threshold or 0 for auto (50-bar ATR SMA × 0.5). |
| **RSI Filter** | ON | Require RSI > 50 (long) or < 50 (short). |
| **RSI Length** | 14 | Period for RSI calculation. |

### 🎯 Trade Management
| Parameter | Default | Description |
|-----------|---------|-------------|
| **Lock: No new signal until TP or SL is hit** | ON | ON = signals blocked until trade closes. OFF = every level break triggers a signal. |
| **Reset trade lock on new session** | ON | ON = trade lock clears at session start. OFF = lock persists unless manually cleared. |

### 🎨 Visuals
| Parameter | Default | Description |
|-----------|---------|-------------|
| **Debug Mode (Info Table)** | OFF | Shows real-time values: RSI, ATR, Volume%, filters, trade status. |

---

## Trading Logic

### Signal Generation

#### Buy Signal 🟢
1. **Timing**: Post-session (after 15:00 GMT+7) & no trade already open (if lock enabled)
2. **Entry**: Price crosses **above** a level (`close > level` & `close[1] <= level`)
3. **Confirmation**: All active filters PASS
4. **Scan**: Bottom-to-top (fi 0→16) → catches the **lowest** level crossed
5. **TP**: Next level up (one level higher)
6. **SL**: Previous level down (one level lower)

#### Sell Signal 🔴
1. **Timing**: Post-session (after 15:00 GMT+7) & no trade already open (if lock enabled)
2. **Entry**: Price crosses **below** a level (`close < level` & `close[1] >= level`)
3. **Confirmation**: All active filters PASS
4. **Scan**: Top-to-bottom (fi 16→0) → catches the **highest** level crossed
5. **TP**: Next level down (one level lower)
6. **SL**: Previous level up (one level higher)

### Trade Exit

- **TP Hit**: Price reaches TP level → trade closes ✅
- **SL Hit**: Price reaches SL level → trade closes ❌
- **Session Reset**: If enabled, trade lock clears at new session start (open trade remains active)

---

## Level Index Reference

| Index | Level | Type | Description |
|-------|-------|------|-------------|
| 0 | EXT-4 | Extension | Strong Demand (very deep) |
| 1 | EXT-3 | Extension | Demand Extension |
| 2 | EXT-2 | Extension | Demand Extension |
| 3 | EXT-1 | Extension | Demand Extension |
| 4 | L0 | Core | **Asia Low** (Strong Demand) |
| 5 | L1 | Core | Demand |
| 6 | L2 | Core | Demand |
| 7 | L3 | Core | Middle-Low |
| 8 | L4 | Core | **Midpoint** |
| 9 | L5 | Core | Middle-High |
| 10 | L6 | Core | Supply |
| 11 | L7 | Core | Supply |
| 12 | L8 | Core | **Asia High** (Strong Supply) |
| 13 | EXT+1 | Extension | Supply Extension |
| 14 | EXT+2 | Extension | Supply Extension |
| 15 | EXT+3 | Extension | Supply Extension |
| 16 | EXT+4 | Extension | Strong Supply (very deep) |

---

## Visual Elements

### Lines & Colors
- **Core Levels (L0–L8)**: Solid lines, varying colors (green demand → gray neutral → red supply)
- **Extension Levels (EXT±1 to ±4)**: Dotted lines
- **Key Levels (L0, L4, L8, EXT±4)**: Thicker lines (width 2)
- **Session Box**: Orange border with semi-transparent fill (07:00–15:00 GMT+7)
- **TP Line**: Green dashed line from signal bar to 03:00 GMT+7
- **SL Line**: Red dashed line from signal bar to 03:00 GMT+7

### Labels
- **Level Labels**: Descriptive text (e.g., "L8 — Asia High ▲ Supply") floated to the right edge of the current session
- **Signal Labels**: TP, SL, and Risk-Reward ratio displayed at entry
- **Exit Labels**: ✅ or ❌ marker with exit price

### Shapes
- **Buy Signal**: Green triangle below the bar
- **Sell Signal**: Red triangle above the bar

---

## Alert Conditions

The indicator provides 4 alert conditions for real-time notifications:

1. **BUY Signal** — Level Break with Confirmation
2. **SELL Signal** — Level Break with Confirmation
3. **TP Hit** — Take Profit reached
4. **SL Hit** — Stop Loss hit

**Setup**: Right-click on the indicator → Alerts → Create Alert

---

## Debug Mode

Enable **Debug Mode** to see a real-time info table (top-right corner) displaying:
- **Asia High / Low** — Session range
- **Step Size** — Level spacing
- **RSI(14)** — Current RSI with direction
- **ATR(14)** — Current ATR vs. threshold
- **Vol / MA%** — Volume relative to 20-bar MA
- **Long / Short Filters** — Pass/Fail status
- **In Trade** — Current trade status (LONG / SHORT / —)
- **Trade TP / SL** — Active take profit and stop loss levels

---

## Best Practices

### ✅ Recommended Settings
1. **Timeframe**: Daily (D) or 4-hour (4H) — allows full Asia session to play out
2. **Instrument**: Major currency pairs (EURUSD, GBPUSD, AUDJPY) — tight spreads during Asia
3. **All Filters ON**: Volume + ATR + RSI together maximize signal quality
4. **Trade Lock ON**: Prevents overlapping signals; one trade per session
5. **Session Reset ON**: Clean slate each new session; trade remains open

### ⚠️ Common Pitfalls to Avoid
1. **Ignoring Filters**: Disabled filters = more signals but lower quality—use at least 2 filters
2. **Wrong Timezone**: Ensure you're using GMT+7 (or adjust session time zone setting)
3. **Low Timeframe Noise**: Use 4H or higher to avoid whipsaws during volatile micro-moves
4. **Over-Trading**: With trade lock ON, max 1 signal per session—be patient
5. **Not Respecting SL**: Always use the SL provided; don't move it after entry

### 📊 Optimization Tips
- **Adjust ATR Threshold**: Increase if too many false signals; decrease if missing moves
- **Adjust RSI Length**: 14 is standard; try 10–21 for different sensitivity
- **Adjust Volume MA Length**: 20 is standard; try 10–30 for tighter/looser filtering
- **Adjust Session Time**: If trading different markets, adjust the session hours accordingly

---

## Installation

1. Open TradingView → Pine Script Editor (Alt+E)
2. Create a new indicator
3. Copy-paste the entire script
4. Click **Save** → name it "Asia Range Divider [8+4 Levels]"
5. Click **Add to Chart**
6. Adjust inputs as desired
7. Enable alerts if using automated notifications

---

## Trade Example

### BUY Signal Example
```
Session High: 1.0850
Session Low:  1.0750
Step Size:    0.0100

Levels:
L0 (Low):  1.0750 ← Buy signal fires here
L1:        1.0760
L2:        1.0770
L3:        1.0780
L4:        1.0800 (Midpoint)
...
L8 (High): 1.0850

Trade Entry:  Close > 1.0750 with all filters PASS
Take Profit:  1.0760 (L1 — next level up)
Stop Loss:    Next level down (EXT-1 ≈ 1.0740)
Risk/Reward:  1:1 (step size 0.0100)
```

---

## Frequently Asked Questions

**Q: Why only post-session signals?**  
A: The Asia session range is locked after close (15:00 GMT+7). Any intraday breakouts would be against an incomplete range, leading to false signals.

**Q: Can I run this on lower timeframes?**  
A: Yes, but expect more whipsaws. The indicator uses **daily** session logic; on 1H or 5M, the session range repeats identically every day, so filters become critical.

**Q: How do I change the session hours?**  
A: Go to Inputs → Asia Session → "Session Time (GMT+7)" and change to your desired hours (e.g., "0600-1400" for Sydney open).

**Q: Does it work on crypto?**  
A: Yes, but "Asia Session" is an FX convention. On crypto (24/5), pick a fixed time window and relabel. The mechanics remain the same.

**Q: Why is RSI set to > 50 for longs?**  
A: RSI > 50 indicates bullish momentum. This is a common filter; adjust if desired, but avoid < 30 or > 70 (oversold/overbought may reverse sharply).

**Q: Can I disable the session box?**  
A: Yes → Inputs → Asia Session → "Show Session Box" → OFF

---

## Support & Notes

- **Compatibility**: Pine Script v6 (TradingView modern syntax)
- **Max Objects**: 500 lines, 500 labels, 50 boxes (TradingView limits)
- **History**: When "Show Past Session History" is OFF, old drawings are cleared; when ON, all sessions stay visible (use carefully to avoid clutter)
- **Bugs / Suggestions**: Review the script's comments for implementation details

---

## Disclaimer

This indicator is for **educational and informational purposes only**. It does not constitute financial advice. Always:
- Test on a demo account first
- Use risk management (never risk more than 1-2% per trade)
- Combine with other analysis before trading live
- Past performance ≠ future results

**Trade responsibly.**

---

**Version**: 1.0  
**Language**: Pine Script v6  
**Updated**: March 2026
