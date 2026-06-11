---
name: pre-market-signal-engine
description: Use when generating daily pre-market BUY signals for the 12-ticker universe defined in config/stocks.json. Triggered by the scheduled routine in this repo. Coordinates Alpha Vantage MCP data pulls, applies the 10-module 40-50 point analysis framework, enforces risk and macro kill-switches, scores confluence across 6 categories, and posts the result to Slack.
---

# Pre-Market Signal Engine — Operational Playbook

This is the file the routine runs. Follow it in order. Each step has a clear stop condition.

## 0. Read configuration

Always start by reading:

- `config/stocks.json` — the universe
- `config/runtime.json` — risk caps, thresholds, Slack channel, timezone
- `templates/slack-output.md` — output format

Do not proceed until all three load successfully.

## 0.1. Signal window check

Check the current time in GMT.

- Valid window: `runtime.signal_window_start_gmt` (02:30) to `runtime.signal_window_end_gmt` (08:30)
- Target delivery: `runtime.target_delivery_gmt` (06:00)
- If the routine fires outside this window, post a note to Slack and stop. Do not analyse tickers outside this window.

## 1. Macro kill-switches (check first, fail fast)

Pull these via Alpha Vantage MCP or `web_search`:

| Check | Condition to stop | Source |
|---|---|---|
| VIX level | `> runtime.vix_max` (default 30) | Alpha Vantage `GLOBAL_QUOTE` symbol `^VIX` |
| VIX caution | `> runtime.vix_caution` (default 25) | same — raise confluence bar to 5/6 for all tickers |
| FOMC day | Today is on the Fed calendar | `web_search`: "FOMC meeting today" |
| CPI release | Today is CPI release day | `web_search`: "US CPI release date this week" |
| NFP release | Today is jobs day | `web_search`: "US non-farm payrolls release date this week" |

If VIX > 30 **or** FOMC/CPI/NFP day: post the kill-switch Slack message from `templates/slack-output.md` (Template B), then **stop**. Do not analyse tickers.

If VIX is 25–30: continue analysis but raise the minimum confluence bar to 5/6 for every ticker.

## 2. Earnings blackout (per-ticker)

For each ticker, call Alpha Vantage `EARNINGS_CALENDAR` (3-month horizon). If the ticker has earnings within **`runtime.earnings_blackout_days`** (default 3) trading days in either direction of today, exclude it from analysis and note it in the Slack footer.

## 3. Data pull per surviving ticker

For each ticker that passes the earnings blackout, fetch from Alpha Vantage MCP:

| What | Alpha Vantage function | Why |
|---|---|---|
| Daily OHLCV (260 days) | `TIME_SERIES_DAILY` | Trend, support/resistance, gap analysis |
| Hourly OHLCV (last 5 days) | `TIME_SERIES_INTRADAY` interval=60min | Entry refinement only |
| EMA 20 / 50 / 200 daily | `EMA` time_period=20/50/200 | Trend alignment |
| RSI 14 daily | `RSI` time_period=14 | Momentum |
| MACD daily | `MACD` (12,26,9) | Momentum crossovers |
| Bollinger Bands daily | `BBANDS` time_period=20 | Volatility squeeze |
| ATR 14 daily | `ATR` time_period=14 | Stop-loss sizing |
| VWAP intraday | `VWAP` | Institutional anchor |
| News sentiment | `NEWS_SENTIMENT` tickers=<TICKER> | Sentiment score |

Also pull **once per run** (not per ticker):

- SPY and QQQ daily (`TIME_SERIES_DAILY`) — market direction
- `^VIX` quote — already pulled in step 1
- `DXY` quote — dollar strength context

If any required call returns null/error for a ticker, skip that ticker and add it to the failures list.

## 4. Apply the 10-module framework

Read each file under `framework/` and apply it. Each module outputs a directional read for the ticker:

- `01-trend-analysis.md` — EMA 20/50/200 stack, HH/HL structure, SPY/QQQ alignment
- `02-momentum.md` — RSI(14), MACD(12,26,9), Stochastic, divergences
- `03-price-action.md` — Support/resistance, candle patterns, Fibonacci, VWAP, gaps, breakouts
- `04-volume.md` — Volume vs 20-day average, breakout confirmation, dark pool prints
- `05-volatility.md` — Bollinger Band squeeze, ATR(14), VIX context
- `06-macro.md` — SPY/QQQ futures, VIX, DXY, 10Y yield, economic calendar
- `07-sentiment.md` — News sentiment score, analyst upgrades/downgrades, options flow, put/call ratio
- `08-earnings-events.md` — 3-day blackout (already applied in step 2; re-confirm here)
- `09-multi-timeframe.md` — Weekly trend confirmation, hourly entry refinement
- `10-statistical-patterns.md` — Historical pattern matching, correlation, anomaly detection, sector rotation

## 5. Confluence score

Roll the 10 modules into 6 confluence categories (this is the gate):

| Category | BUY confirms when |
|---|---|
| Trend | Price > EMA20 > EMA50 > EMA200 (bull stack); higher highs / higher lows |
| Momentum | RSI 40–65 AND MACD bullish cross or above signal line (no bearish divergence) |
| Volume | Today's volume > 20-day avg AND rising on up-days (not a low-volume fakeout) |
| Price Action | Recent support hold OR clean breakout with retest; Fibonacci / VWAP aligned |
| Macro | SPY & QQQ futures green AND VIX < 20 AND DXY not spiking against tech |
| Sentiment | Alpha Vantage news sentiment ≥ 0.15 AND no major negative headlines AND options flow net positive |

**Decision rule:**

- 5–6 confirms → HIGH confidence BUY
- 4 confirms → MEDIUM confidence BUY (smaller size note in Slack footer)
- ≤3 confirms → WAIT (no signal output; post Template C)

**Adjustments that raise the bar to 5/6:**

- VIX is in the 25–30 caution zone (from step 1)
- Weekly trend (module 09) disagrees with the daily signal (counter-trend)
- First trading day after an earnings blackout lifts

## 6. Risk gate (must pass to issue BUY)

Compute:

- **Stop:** entry − (`runtime.atr_stop_multiplier` × ATR14) = entry − (1.5 × ATR14)
- **Target:** entry + (`runtime.atr_target_multiplier` × ATR14) = entry + (3.0 × ATR14)
- **Risk:reward:** must be ≥ `runtime.min_rr` (default 1:2)

If R:R fails, downgrade to WAIT. The 1:2 minimum is non-negotiable.

## 7. Post to Slack

Use the Slack connector. Channel = `runtime.slack_channel_id`. Format = `templates/slack-output.md`.

- **BUY signals exist:** use Template A — one line per signal: `{TICKER} : BUY`
- **No qualifying signals:** use Template C — `WAIT` message
- **Kill-switch fired:** use Template B — already posted in step 1

Use plain text. Copy the `━━━` separator exactly from the template. Do not add price levels, stops, targets, confidence labels, or any text not in the template.

Cap output at `runtime.max_concurrent_signals` (default 3) BUY signals. Highest confluence score wins ties.

**Do not update any files in this repo during the run. This is an output-only engine.**

## 8. Hard rules — never violate

These come from section 9 of the original spec:

- Never issue a signal within 3 trading days of earnings
- Never claim guaranteed profits — include the risk note in the Slack message
- Never issue more than 3 concurrent signals per run
- Never skip macro checks (VIX, FOMC, CPI, NFP)
- Never issue a signal on fewer than 4 confirming categories (5 when VIX 25–30 or counter-trend)
- Never issue a signal with R:R below 1:2
- Never signal if a stock is in a confirmed downtrend (price below all 3 EMAs) — only BUY setups fire
- Never execute trades — this is a signal engine, output only
- Never commit anything to this repo during a run
- Never post signals to Slack other than via the template
- Never fabricate prices, indicator values, or levels — if data is unavailable, skip the ticker

## 9. End the run

Post the single Slack message. Done. Do not loop. Do not start another analysis pass. Exit cleanly.
