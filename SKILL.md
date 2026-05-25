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

## 1. Macro kill-switches (check first, fail fast)

Pull these via Alpha Vantage MCP or `web_search`:

| Check | Condition to stop | Source |
|---|---|---|
| VIX level | `> macro_kill_switches.vix_max` (default 30) from `config/runtime.json` | Alpha Vantage `GLOBAL_QUOTE` symbol `^VIX` |
| FOMC day | Today is on the Fed calendar | `web_search`: "FOMC meeting today" |
| CPI release | Today is CPI release day | `web_search`: "US CPI release date this week" |
| NFP release | Today is jobs day | `web_search`: "US non-farm payrolls release date this week" |

If **any** fire: post the kill-switch Slack message from `templates/slack-output.md`, then **stop**. Do not analyze tickers.

## 2. Earnings blackout (per-ticker)

For each ticker, call Alpha Vantage `EARNINGS_CALENDAR` (3-month horizon). If the ticker has earnings within **3 trading days** in either direction of today, exclude it from analysis and note it in the Slack footer.

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

- `01-trend-analysis.md`
- `02-momentum.md`
- `03-price-action.md`
- `04-volume.md`
- `05-volatility.md`
- `06-macro.md`
- `07-sentiment.md`
- `08-earnings-events.md` (already partially applied in step 2)
- `09-multi-timeframe.md`
- `10-statistical-patterns.md`

## 5. Confluence score

Roll the 10 modules into 6 confluence categories (this is the gate):

| Category | BUY confirms when |
|---|---|
| Trend | Price > EMA20 > EMA50 > EMA200 (bull stack) |
| Momentum | RSI 40–65 AND MACD bullish cross or above signal |
| Volume | Today's volume > 20-day avg AND rising on up-days |
| Price action | Recent support hold OR clean breakout with retest |
| Macro | SPY & QQQ futures green AND VIX < 20 |
| Sentiment | Alpha Vantage news sentiment ≥ 0.15 AND no major negative headlines |

**Decision rule** (thresholds from `config/runtime.json → confluence`):

- `min_categories_high_confidence` (default 5) or 6 confirms → HIGH confidence BUY
- `min_categories_for_buy` (default 4) confirms → MEDIUM confidence BUY (note smaller position size)
- ≤3 confirms → WAIT (no signal output)

Cap total output at `max_signals_per_run` (default 3) BUY signals. When more tickers qualify, rank by confluence score descending, then by pattern-tag count, then by historical-analog hit rate.

## 6. Risk gate (must pass to issue BUY)

Compute using multipliers from `config/runtime.json → risk`:

- **Stop:** entry − (`risk.atr_stop_multiplier` × ATR14)  [default 1.5]
- **Target:** entry + (`risk.atr_target_multiplier` × ATR14)  [default 3.0]
- **Risk:reward:** must be ≥ `risk.min_rr`  [default 2.0]

If R:R fails, downgrade to WAIT.

## 7. Post to Slack

Use the Slack connector. Channel = `slack_channel_id` from `config/runtime.json`. Format = `templates/slack-output.md`. Use Slack-compatible markdown (`*bold*`, `_italic_`) — no HTML tags, no double-asterisk.

Choose the correct template from `templates/slack-output.md`:
- **Template A** — at least one BUY signal was generated
- **Template B** — a macro kill-switch fired (stop immediately after posting; do not analyse tickers)
- **Template C** — all tickers analysed but none reached the confluence threshold

Always include in the message:
- Macro snapshot header (SPY, QQQ direction; VIX level + trend; DXY level)
- All BUY signal blocks with: ticker, confidence, score, entry zone, stop, target, R:R, confirming categories, pattern tag, one-line rationale
- "Also watching" section for tickers at confluence_threshold − 1 (up to 3)
- Footer: earnings blackout list with next earnings date, and any tickers skipped due to API errors
- Risk disclaimer line

Post exactly one message per run. Do not post status updates, intermediate findings, or multiple messages. Do not update any file in this repo during the run.


## 8. Hard rules — never violate

These come from section 9 of the original spec:

- Never issue a signal within 3 trading days of earnings
- Never claim guaranteed profits — include the risk note in the Slack message
- Never issue more than 3 concurrent signals
- Never skip macro checks
- Never issue a signal on fewer than 4 confirming categories
- Never issue a signal with R:R below 1:2
- Never execute trades — this is a signal engine, output only
- Never commit anything to this repo during a run
- Never post signals to Slack other than slackoutput template

## 9. End the run

Post the Slack message. Done. Do not loop. Do not start another analysis pass. Exit cleanly.
