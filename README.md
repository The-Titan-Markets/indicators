# Titan Markets — TradingView Indicators

Pine Script indicators built and maintained by **Titan Markets LLC** for client use on TradingView.

| Indicator | File | Pine version | Type |
|---|---|---|---|
| [Titan VWAP Pro](#titan-vwap-pro) | [`TitanVWAPPro.pine`](TitanVWAPPro.pine) | v6 | Overlay |

---

## Installation

1. Open any chart on [TradingView](https://www.tradingview.com/) and open the **Pine Editor** panel at the bottom of the screen.
2. Copy the full contents of the `.pine` file and paste it into the editor, replacing any existing code.
3. Click **Save**, then **Add to chart**.
4. Open the indicator's **Settings** (gear icon) to adjust inputs. Every input is grouped by feature.

---

## Titan VWAP Pro

Session VWAP with volume-weighted standard deviation bands, an independently anchored VWAP, mean-reversion and trend-confirmation signals, a live stats HUD, and a multi-timeframe VWAP alignment table.

### Features

#### 1. Session VWAP
- Standard intraday VWAP built from cumulative `volume × hlc3` and `volume` since the session start.
- Resets once per session at a configurable start time. The default is **09:30 America/New_York**, which matches the RTH open for CME equity index futures (ES, NQ, YM, RTY) and US cash equities.
- Works on 24-hour futures sessions without drifting. The reset fires on exactly one bar per session, whatever the chart resolution.
- σ is a true **volume-weighted** standard deviation (`E_w[x²] − E_w[x]²`), not a simple `ta.stdev()`.

#### 2. Deviation bands
- **±σ1** bands are solid lines. **±σ2** bands are dashed lines with a little more transparency.
- A two-tone shaded cloud: a stronger fill from VWAP to ±σ1 and a lighter fill from ±σ1 to ±σ2.
- Both multipliers can be set to any value, such as 1.5 / 2.5 / 3. Each band can be toggled on or off.

#### 3. Anchored VWAP
- A second VWAP drawn as a dashed cyan line, anchored independently of the session VWAP.
- Anchor types: **Session**, **Day**, **Week**, **Month**, **Custom date/time** (for earnings, CPI, FOMC, swing points) and **Manual bar offset**.
- The anchor bar gets a triangle marker, an editable label (for example `ANCHOR · CUSTOM`) and an optional dotted vertical line.

> Pine Script can't read mouse clicks, so live click-to-anchor like TradingView's drawing tool isn't possible. Anchors are set from the settings dialog instead.

#### 4. Signals
| Signal | Condition | Marker |
|---|---|---|
| `+σ2` | Bar high reaches or exceeds the upper σ2 band | ▼ above the bar |
| `−σ2` | Bar low reaches or exceeds the lower σ2 band | ▲ below the bar |
| `VWAP RECLAIM` | Close moves back above session VWAP after a close below it | ● below the bar |
| `VWAP LOST` | Close moves back below session VWAP after a close above it | ● above the bar |

Each signal type has its own toggle and colour. Older signal labels are removed automatically, so long chart histories never reach TradingView's drawing limits.

#### 5. Live stats panel
A compact dark HUD card showing values for the current bar: **O / H / L / C**, **VWAP**, **σ**, **DEV** (distance from VWAP in σ units, for example `-1.91σ`) and **Volume** (bar and session). DEV turns amber once price is beyond the σ2 multiplier.

#### 6. Multi-timeframe table
Shows the session VWAP for the chart timeframe and up to three higher timeframes (defaults **15m** and **1H**, with an optional third). Each row has a ▲/▼ marker for whether price is above or below that VWAP. The table can be toggled, and its position is configurable.

#### 7. Branding
- A `TITAN VWAP PRO` tag with a configurable position.
- A low-opacity `TITANMARKETS` watermark in a bottom corner. Both can be turned off.
- Default palette (every colour can be changed in settings):

| Element | Default |
|---|---|
| Session VWAP | Titan gold `#F5A623` |
| σ bands and cloud | Lavender `#8B7FD1` |
| Anchored VWAP | Cyan `#22D3EE` |

### Alerts
Open **Create Alert**, choose *Titan VWAP Pro* as the condition, then pick one of:

- VWAP Reclaim (bullish)
- VWAP Lost (bearish)
- Price at +2 sigma band
- Price at -2 sigma band
- Anchored VWAP crossed up
- Anchored VWAP crossed down

Alert messages include the ticker, interval and the relevant price.

### Settings reference
| Group | Contents |
|---|---|
| ① Session | Start hour / minute, timezone |
| ② Session VWAP | Toggle, colour, line width |
| ③ Deviation Bands | σ1 / σ2 toggles and multipliers, colour, widths, cloud toggle and fill transparency |
| ④ Anchored VWAP | Toggle, anchor type, custom date/time, bar offset, label text, marker and line toggles, colour, width |
| ⑤ Signals | Band-touch and reclaim/lost toggles and colours, maximum labels kept |
| ⑥ Stats Panel | Toggle, position, text size |
| ⑦ Multi-Timeframe | Toggle, position, timeframes 1–3, confirmed-value (non-repainting) toggle |
| ⑧ Branding | Tag and watermark toggles, positions, watermark transparency |

### Repainting behaviour
- **VWAP, bands and anchored VWAP** never change after a bar closes.
- **Signals** are checked on the live bar, so a marker can appear and then disappear while that bar is still forming. Once the bar closes, the marker is final.
- **The stats panel** always shows live values for the current bar. This is intentional.
- **The multi-timeframe table** requests data with `lookahead_off`. By default it uses the last *closed* higher-timeframe value, so it doesn't repaint. Turn off *Use confirmed HTF values* to see the higher-timeframe bar that is still forming instead.

---

## Disclaimer

These indicators are analytical tools for informational and educational purposes only. They are not investment advice or a recommendation to buy or sell any security, future or other instrument. Trading involves substantial risk of loss. Past signals don't guarantee future results.

© Titan Markets LLC. All rights reserved.

---

## Troubleshooting

**`mismatched character "\n" expecting "` (or other errors on the last lines)**
The script got corrupted during pasting. TradingView's editor can add extra quotes or brackets while you paste, and pasting over part of an older version can leave stray lines behind. To fix it:
1. Click inside the Pine Editor and press **Cmd/Ctrl + A**, then **Delete**, so the editor is completely empty.
2. On GitHub, open [`TitanVWAPPro.pine`](TitanVWAPPro.pine) and click **Raw** (or the copy icon) to copy the full file.
3. Paste it in one go. The last line should end in `crossed BELOW the anchored VWAP.")`, with nothing after it.
