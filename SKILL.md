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

**Signal window check:** Signals are only valid between `runtime.signal_window.start_gmt` (02:30 GMT) and `runtime.signal_window.end_gmt` (08:30 GMT). If the current UTC time is outside this window, post a brief Slack note ("Outside signal window — next run at 02:30 GMT") and exit without analyzing any tickers.

## 1. Macro kill-switches (check first, fail fast)

Pull these via Alpha Vantage MCP or `web_search`:

| Check | Condition to stop | Source |
|---|---|---|
| VIX level | `>= runtime.macro_kill_switches.vix_max` (default 30) | Alpha Vantage `GLOBAL_QUOTE` symbol `^VIX` |
| FOMC day | Today is an FOMC announcement day | `web_search`: "FOMC meeting today" |
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
| Volume | Today's volume > `runtime.volume.confirm_pct`% of 20-day avg AND rising on up-days |
| Price action | Recent support hold OR clean breakout with retest |
| Macro | SPY & QQQ futures green AND VIX < 20 |
| Sentiment | Alpha Vantage news sentiment ≥ 0.15 AND no major negative headlines |

**Decision rule:**

- 5–6 confirms → HIGH confidence BUY (label: HIGH)
- 4 confirms → MEDIUM confidence BUY (label: MEDIUM, note smaller position size)
- ≤3 confirms → WAIT (no signal output)

**Threshold adjustments from module 09:**
- Counter-trend setup (weekly bear + daily bull) → raise bar to `runtime.confluence.counter_trend_min_categories` (default 5)
- First session post-earnings blackout → raise bar to `runtime.earnings.post_earnings_raised_bar` (default 5)

**Signal cap:** Never output more than `runtime.confluence.max_signals_per_run` (default 3) BUY signals in a single run. If more qualify, keep the highest-scoring ones; break ties by pattern-tag count, then historical analog hit rate.

## 6. Risk gate (must pass to issue BUY)

Compute using the entry midpoint (midpoint of `entry_zone_low` and `entry_zone_high` from module 09):

- **Stop:** entry − (`runtime.risk.stop_atr_multiple` × ATR14) = entry − (1.5 × ATR14)
- **Target:** entry + (`runtime.risk.target_atr_multiple` × ATR14) = entry + (3.0 × ATR14)
- **Risk:reward:** (target − entry) / (entry − stop) must be ≥ `runtime.risk.min_rr` (default 2.0)

If R:R fails, downgrade to WAIT. Never emit a signal with R:R below 2.0 regardless of confluence score.

## 7. Post to Slack

Use the Slack MCP connector. Channel = `runtime.slack_channel_id`. Format = exactly the matching template from `templates/slack-output.md`:

- BUY signals present → Template A
- Kill-switch fired → Template B
- No qualifying signals → Template C

Fill every placeholder. For missing data fields (API errors), replace with `N/A` and note it in the message footer.

Post exactly **one** Slack message per run. Do not post progress updates or intermediate messages.

## 8. Hard rules — never violate

- Never issue a signal within `runtime.earnings.blackout_trading_days_before` trading days of earnings
- Never claim guaranteed profits — the risk disclaimer line is mandatory in every message
- Never issue more than `runtime.confluence.max_signals_per_run` (3) BUY signals in a single run
- Never skip the macro kill-switch checks (step 1)
- Never issue a signal on fewer than `runtime.confluence.min_categories_for_buy` (4) confirming categories
- Never issue a signal with R:R below `runtime.risk.min_rr` (2.0)
- Never execute trades — this is a signal engine, output only
- Never commit anything to this repo during a run
- Never fabricate prices, indicator values, or news headlines

## 9. End the run

Post the Slack message. Done. Do not loop. Do not start another analysis pass. Exit cleanly.
