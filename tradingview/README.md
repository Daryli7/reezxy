# RZ Pro Suite (TradingView indicator)

`rz_pro_suite.pine` is a Pine Script v6 overlay with five parts:

| Panel | What it shows |
|---|---|
| **Signal Stats** | Reversal (REV) and Continuation (CONT) signals tested on the loaded history: trades, win % to TP1, TP2/TP3 hit rate, full stops, net R |
| **Killzone** | Trades, win % and net R for Asia, London, NY Pre-open, NY Open and NY PM. The live session is shown in yellow |
| **DOL** | The 4 nearest draws on liquidity in the bias direction: PDH/PDL, Asia/London/NY AM highs and lows, and untaken BSL/SSL swing levels |
| **Pro Suite** | An 8-point checklist with a grade (A+ to F): sweep, PDA/FVG, iFVG/CISD, premium/discount, HTF bias, Silver Bullet/macro time, SMT, clear DOL ≥ 2R |
| **Trade Report** | The trades you paste in, drawn on the chart with entry, stop zone, target zone, exit, points, USD and R |

## Install
1. In TradingView, open **Pine Editor**, then **New → Indicator**.
2. Delete what's there, paste the whole of `rz_pro_suite.pine`, then click **Save** and **Add to chart**.

## Showing a trade report on your chart
Open the indicator's **Settings → Inputs → ① Trade Report** and paste rows into **Report rows**, one trade per line:

```
SYMBOL, DIR, QTY, ENTRY_TIME, ENTRY, STOP, TP(s), EXIT_TIME, EXIT, NOTE
MNQ1!, LONG, 2, 2026-11-12 09:41, 21045.25, 21020.00, 21080/21110, 2026-11-12 10:12, 21095.50, NY Open FVG
MNQ1!, SHORT, 1, 2026-11-12 14:05, 21130.00, 21148.00, 21090, 2026-11-12 14:31, 21148.00, PM sweep, stopped
MES1!, LONG, 3, 2026-11-13 09:50, 5950.25, 5944.00, 5965, , , still running
EURUSD, SELL, 1, 11/13/2026 03:15, 1.08420, 1.08510, 1.08250, 11/13/2026 05:02, 1.08260, London KZ
```

- **Symbol matching:** the indicator draws only the rows whose symbol matches the chart, so you can paste the same report on every chart. `MNQ1!`, `MNQZ2026` and `CME_MINI:MNQ1!` all match an MNQ chart. With *Match mini ↔ micro* turned on, `NQ` rows also show on MNQ (and `ES` on MES). Use `*` to show a row on any chart.
- **Open trades:** leave EXIT_TIME and EXIT blank. The trade is drawn to the current bar and shows live P&L.
- **Times** use the *Report timezone* setting (New York by default). The format can be `YYYY-MM-DD HH:MM` or `MM/DD/YYYY HH:MM`.
- **USD** is calculated as points × the contract's point value × QTY. MNQ is $2 per point, so 50.25 pts × 2 contracts = $201.
- Lines starting with `#` are skipped. The panel counts rows for other symbols and rows it couldn't read.

When you ask Claude for a trade report, ask for it "in RZ report format" and you can paste the rows straight in.

## Signal engine rules
- **REV:** price sweeps a swing high or low (wicks through it, then closes back inside). Within *N* bars a displacement candle (body ≥ 1.2 × ATR) forms an FVG in the opposite direction. The stop goes beyond the sweep extreme.
- **CONT:** the HTF bias and the current-timeframe structure agree, and price makes its first retest of a fresh FVG and closes back out of it. The stop goes beyond the FVG.
- **Exits:** one third comes off at each of 1R, 2R and 3R. The stop moves to breakeven after TP1. Any remainder is closed after *Max bars in a trade*.
- **SMT** compares the chart against *SMT comparison symbol* (MES1! by default). If you're on forex or crypto, change it to a correlated pair, for example DXY or ETHUSD.

The stats are a simple bar-by-bar model on the history you have loaded. They aren't a guarantee of future results.
