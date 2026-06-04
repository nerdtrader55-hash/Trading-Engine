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

## 0.5. Signal window check

Check the current GMT time. Signals are only generated during the window defined in `runtime.signal_window_gmt_start` to `runtime.signal_window_gmt_end` (default **02:30–08:30 GMT**).

If the current time is **outside** this window, stop immediately without posting to Slack. This is a silent exit — the routine may have been triggered manually outside schedule.

## 1. Macro kill-switches (check first, fail fast)

Pull these via Alpha Vantage MCP or `web_search`:

| Check | Condition to stop | Source |
|---|---|---|
| VIX level | `> runtime.vix_max` (default 30) | Alpha Vantage `GLOBAL_QUOTE` symbol `^VIX` |
| FOMC day | Today is on the Fed calendar | `web_search`: "FOMC meeting today" |
| CPI release | Today is CPI release day | `web_search`: "US CPI release date this week" |
| NFP release | Today is jobs day | `web_search`: "US non-farm payrolls release date this week" |

If **any** fire: post the kill-switch Slack message from `templates/slack-output.md` Template B, then **stop**. Do not analyze tickers.

## 2. Earnings blackout (per-ticker)

For each ticker, call Alpha Vantage `EARNINGS_CALENDAR` (3-month horizon). If the ticker has earnings within `runtime.earnings.blackout_trading_days_before` trading days before or `runtime.earnings.blackout_trading_days_after` trading days after today, exclude it from analysis and note it in the Slack footer.

When a ticker re-enters after the blackout ends, require `runtime.earnings.post_earnings_raised_confluence` (default 5/6) for that first session only.

## 3. Data pull per surviving ticker

For each ticker that passes the earnings blackout, fetch from Alpha Vantage MCP:

| What | Alpha Vantage function | Why |
|---|---|---|
| Daily OHLCV (260 days) | `TIME_SERIES_DAILY` | Trend, support/resistance, gap analysis |
| Hourly OHLCV (last 5 days) | `TIME_SERIES_INTRADAY` interval=60min | Entry refinement only |
| EMA 20 / 50 / 200 daily | `EMA` time_period=20/50/200 | Trend alignment |
| RSI 14 daily | `RSI` time_period=14 | Momentum |
| MACD daily | `MACD` (12,26,9) | Momentum crossovers |
| Stochastic 14,3,3 daily | `STOCH` | Momentum confirmation |
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
| Volume | Today's volume > `runtime.min_volume_vs_20d_avg_pct`% of 20-day avg AND rising on up-days |
| Price action | Recent support hold OR clean breakout with retest |
| Macro | SPY & QQQ futures green AND VIX < `runtime.vix_caution` (default 20) |
| Sentiment | Alpha Vantage news sentiment ≥ 0.15 AND no major negative headlines |

**Decision rule:**

- `>= runtime.high_confidence_confluence` (default 5–6) confirms → **HIGH** confidence BUY
- `>= runtime.min_confluence` (default 4) confirms → **MEDIUM** confidence BUY (note smaller size)
- `< runtime.min_confluence` (default ≤3) confirms → **WAIT** — no signal output for this ticker

## 6. Risk gate (must pass to issue BUY)

Compute:

- **Stop:** entry − (`runtime.stop_atr_multiplier` × ATR14) — default entry − 1.5×ATR
- **Target:** entry + (`runtime.target_atr_multiplier` × ATR14) — default entry + 3.0×ATR
- **Risk:reward:** must be ≥ `runtime.min_rr` (default 1:2)

If R:R fails, downgrade to WAIT.

## 7. Post to Slack

Use the Slack connector. Channel = `runtime.slack_channel_id`. Use markdown that Slack will render correctly (no HTML tags).

Pick the template from `templates/slack-output.md` based on the outcome:

- **BUY signals exist** → Template A. Max `runtime.max_concurrent_signals` (default 3) tickers. Rank by confluence score if more qualify.
- **Kill-switch fired** → Template B. Already sent in step 1 — do not post again.
- **No tickers reached min_confluence** (but no kill-switch) → Template C.

Do not update anything into stock files or this repo during the run. This is an output-only engine.

Post exactly the template-based message to Slack. Do not post any other messages during the run.

Never change the template file.

## 8. Hard rules — never violate

- Never issue a signal within `runtime.earnings.blackout_trading_days_before` trading days of earnings
- Never claim guaranteed profits — include the risk note in the Slack message
- Never issue more than `runtime.max_concurrent_signals` concurrent signals
- Never skip macro checks
- Never issue a signal on fewer than `runtime.min_confluence` confirming categories
- Never issue a signal with R:R below `runtime.min_rr`
- Never execute trades — this is a signal engine, output only
- Never commit anything to this repo during a run
- Never post signals to Slack other than the slack-output templates
- If VIX > `runtime.vix_max`, issue Template B regardless of all other signals

## 9. End the run

Post the Slack message. Done. Do not loop. Do not start another analysis pass. Exit cleanly.
