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

## 0.5. Signal window gate (check before anything else)

Check the current time in GMT.

- Allowed window: `runtime.signal_window.start_gmt` to `runtime.signal_window.end_gmt` (default **02:30–08:30 GMT**).
- If the current GMT time is **outside** this window: post Template B with reason `outside signal window (02:30–08:30 GMT)`, then **stop**. Do not analyse tickers.

This gate exists to prevent stale signals from firing if the routine is triggered manually outside pre-market hours.

## 1. Macro kill-switches (check first, fail fast)

Pull these via Alpha Vantage MCP or `web_search`:

| Check | Condition to stop | Source |
|---|---|---|
| VIX level | `> runtime.macro_kill_switches.vix_max` (default 30) | Alpha Vantage `GLOBAL_QUOTE` symbol `^VIX` |
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

**Decision rule** (values from `runtime.confluence`):

- `min_categories_high_confidence` (default 5) or 6 confirms → HIGH confidence BUY
- `min_categories_for_buy` (default 4) confirms → MEDIUM confidence BUY (smaller size note)
- Below `min_categories_for_buy` → WAIT — use Template C
- Counter-trend setup (weekly bear + daily bull) → threshold raises to `counter_trend_min_categories` (default 5)
- First day after earnings blackout → threshold raises to `post_earnings_min_categories` (default 5)

## 6. Risk gate (must pass to issue BUY)

Compute:

- **Stop:** entry − (`runtime.risk.stop_atr_multiplier` × ATR14) (default 1.5)
- **Target:** entry + (`runtime.risk.target_atr_multiplier` × ATR14) (default 3.0)
- **Risk:reward:** must be ≥ `runtime.risk.min_rr` (default 2.0 = 1:2)

If R:R fails, downgrade to WAIT.

## 7. Post to Slack

Use the Slack connector. Channel = `runtime.slack_channel_id`. Format = `templates/slack-output.md`.

- BUY signals with 5–6 confluence → Template A
- BUY signals with exactly 4 confluence → Template A (same format; position-sizing note is NOT added to the message — keep the output clean)
- No qualified signals → Template C
- Kill-switch fired → Template B

Rules:
- Post **one** Slack message per run. Never split into multiple messages.
- Use markdown Slack renders natively (`*bold*`, `_italic_`). No HTML tags.
- Do not update anything in this repo during the run. Output-only engine.
- Never change the template file.
- Cap output at `runtime.confluence.max_signals_per_run` signals (default 3). If more tickers qualify, rank by confluence score, then by pattern-tag count from module 10, then alphabetically.


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
