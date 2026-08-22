Give a live BTC report with entry & exit levels, then publish/update a terminal-style widget artifact for it — the same treatment used for the MNQ futures widget earlier in this project's history.

Steps:
1. Pull live BTC price data via the Interactive Brokers (IBKR) connector.
   - The account holds a BTC CRYPTO position via PAXOS custody (contract_id 479624278 as of last check) — start there, but re-verify with `search_contracts` if it's stale or missing.
   - Try `get_price_snapshot` / `get_price_history` first with no `exchange` param (SMART may work for crypto). If the response comes back empty, retry passing `exchange` explicitly (e.g. `PAXOS` or `ZEROHASH`) the same way the MNQ futures fix required `exchange=CME` — crypto and futures both fall outside plain SMART routing on IBKR.
   - Pull enough intraday history (15-min bars, `outside_rth: true`) to chart the current session, plus recent daily bars for context (1-week/1-month range, volume).
2. Compute a short report: current price, recent range/session high-low, % change, and a plausible long-bias and short-bias entry/stop/target structure based on the actual levels in the data (not invented numbers).
3. Build a widget artifact for BTC, following the same design as the MNQ terminal: same dark trading-terminal aesthetic, embedded fonts, CVD-safe up/down/accent palette (reuse the same validated hex values: up `#007c00`, down `#df466c`, accent `#109bd5`, surface `#12161F`), candlestick session chart with hover tooltip, long/short setup ladder cards, and a stats row.
   - This is a SEPARATE artifact from the MNQ one — do not overwrite it. Track its own artifact URL across updates in this conversation.
4. Publish the artifact and give a concise text summary of the report.

On repeat invocations in the same conversation, republish to the same BTC artifact URL (pass `url`) rather than creating a new one each time, mirroring how the MNQ widget was iteratively updated.
