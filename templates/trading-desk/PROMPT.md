# Trading Desk widget: reusable format

`desk-template.html` is a one-page trading desk: a price header, key levels, entry/exit setups,
a daily range chart, technicals, bias, and a profile. The layout is fixed. The page is filled in
entirely from the `DESK` object at the top of its `<script>`, so a new session only has to swap
the data.

It works for stocks (for example SOFI) and futures (for example MNQ).

---

## Paste this into a new session

Replace `<TICKER>` first. For a futures desk, keep the futures lines. For a stock, you can delete them.

```
Build a live trading-desk widget for <TICKER> using the format in
templates/trading-desk/desk-template.html (repo daryli7/reezxy, branch
claude/affectionate-babbage-mz9gt9). Keep the layout and CSS exactly as they are and only
replace the DESK data object.

Data (Interactive Brokers connector):
- search_contracts "<TICKER>" to get the contract_id (pick the exact-symbol US primary listing).
- get_price_snapshot: last, bid_ask, change, open, high, low, volume, misc_statistics,
  historical_vol (plus future_open_interest for futures).
- get_price_history: ONE_DAY bars over ONE_MONTH for the chart and levels, plus ONE_WEEK bars over
  SIX_MONTHS for the major support and resistance.
- Futures only: find the current front month with search_futures (sort by contract_month and take
  the earliest non-expired expiry). Don't reuse an old contract ID. Pass exchange=CME on every
  get_price_snapshot / get_price_history call.

Technicals (TradingView connector): mcp-tv-get-technicals-rating on the daily timeframe
(e.g. NASDAQ:<TICKER> for a stock, CME_MINI:MNQ1! for MNQ).

Fill DESK with:
- 3 resistance and 3 support levels plus the current price row, top to bottom.
- 3 setups with entry, stop, T1, T2, a risk and R:R note, and a state (ARMED / LIMIT / TRIGGERED /
  STOPPED OUT). Stocks: risk per share and per 100 shares. MNQ: points and $ per contract
  ($2/pt).
- chart.decimals = 2 for stocks, 0 for index futures.
- A bias paragraph naming the level that invalidates it, and a timestamp footer.

Publish it as a NEW artifact (or republish to <ARTIFACT URL> if I give you one), then give me a
short summary with a setups table.
```

---

## DESK fields, quick reference

| Field | What it holds |
|---|---|
| `symbol`, `name`, `venue` | Header title, subtitle, and exchange pill (`NASDAQ`, `CME · Globex`) |
| `headline.tone` | `bull` (amber), `bear` (red), `neutral` (grey) callout pill |
| `price`, `change.dir`, `change.text` | Hero price and the up/down change chip |
| `status.live` | `true` shows a green dot (market open), `false` a grey one |
| `stats` | `[label, value]` pairs for the stat strip (7 fit on one row) |
| `levels[].kind` | `r` resistance · `s` support · `p` pivot · `c` current price (highlighted row) |
| `setups[]` | `name, side (long/short), state, entry, stop, t1, t2, note` |
| `chart.bars` | `{d, h, l, c}` oldest → newest; add `live:true` to an in-progress bar |
| `chart.refs` | Dashed lines: `{p, kind:"r"|"s"}` |
| `technicals` | `rating`, `tone`, `summary` (HTML ok), `stats`, `note` |
| `bias` | HTML allowed (`<strong class='num'>`) |
| `specsTitle`, `specs` | Right-hand footer card (profile for stocks, contract specs for futures) |

The setup state pill colours itself from its text: STOP/FAIL/CANCEL shows red,
TRIGGER/FILL/HIT/OPEN shows amber, and anything else shows grey.
