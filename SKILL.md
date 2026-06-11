---
name: pre-market-signal-engine
description: Use when generating daily pre-market BUY signals for the 12-ticker universe defined in config/stocks.json. Triggered by the scheduled routine in this repo. Coordinates Alpha Vantage MCP data pulls, applies the 10-module 40-50 point analysis framework, enforces risk and macro kill-switches, scores confluence across 6 categories, and posts the result to Slack.
---

# Pre-Market Signal Engine — Operational Playbook

This is the file the routine runs. Follow it in order. Each step has a clear stop condition.

---

## 0. Read configuration

Always start by reading:

- `config/stocks.json` — the universe (12 tickers)
- `config/runtime.json` — all risk caps, thresholds, Slack channel, timezone, signal window
- `templates/slack-output.md` — output format (never modify this file during a run)

Do not proceed until all three load successfully.

Key `runtime.json` fields you will use throughout this playbook:

| Field | Default | Used in |
|---|---|---|
| `slack_channel_id` | `C0B2CTBAV9B` | Step 7 — Slack post |
| `signal_window.start_gmt` | `02:30` | Step 0a — window check |
| `signal_window.end_gmt` | `08:30` | Step 0a — window check |
| `risk.vix_max` | `30` | Step 1 — kill-switch |
| `risk.min_rr` | `2.0` | Step 6 — risk gate |
| `risk.max_concurrent_positions` | `3` | Step 7 — signal cap |
| `confluence.high_threshold` | `5` | Step 5 — scoring |
| `confluence.medium_threshold` | `4` | Step 5 — scoring |
| `confluence.atr_stop_multiplier` | `1.5` | Step 6 — stop sizing |
| `confluence.atr_target_multiplier` | `3.0` | Step 6 — target sizing |

---

## 0a. Signal window check

Signals are only valid during the pre-market window:
- Window open: `signal_window.start_gmt` (default 02:30 GMT)
- Window close: `signal_window.end_gmt` (default 08:30 GMT)
- Target delivery: 06:00 GMT

Check current GMT time. If you are outside the window, do not proceed. Post a brief Slack note using Template B with reason `Run triggered outside pre-market window (02:30–08:30 GMT).` and stop.

The US market opens at 14:30 GMT. All signals must be issued at least 6 hours before the open.

---

## 1. Macro kill-switches (check first, fail fast)

Pull these via Alpha Vantage MCP or `web_search`:

| Check | Condition to stop | Source |
|---|---|---|
| VIX level | `> risk.vix_max` (default 30) | Alpha Vantage `GLOBAL_QUOTE` symbol `^VIX` |
| FOMC day | Today is on the Fed calendar | `web_search`: "FOMC meeting today {DATE}" |
| CPI release | Today is CPI release day | `web_search`: "US CPI release date this week" |
| NFP release | Today is jobs day | `web_search`: "US non-farm payrolls release date this week" |

If **any** fire: post the kill-switch message (Template B from `templates/slack-output.md`) with the exact `{REASON}` string from the template, then **stop**. Do not analyse any tickers.

---

## 2. Earnings blackout (per-ticker)

For each ticker, call Alpha Vantage `EARNINGS_CALENDAR` (3-month horizon). If the ticker has earnings within **3 trading days** in either direction of today, exclude it from analysis and add it to the "Earnings blackout" section of the Slack output.

Do not use hardcoded earnings dates — always fetch live from Alpha Vantage.

---

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

If any required call returns null/error for a ticker, skip that ticker, add it to the failures list, and continue with remaining tickers. If all tickers fail, use Template C or Template B as directed in `templates/slack-output.md`.

Never fabricate prices, indicator values, or levels. If you cannot fetch the data, you cannot issue a signal.

---

## 4. Apply the 10-module framework

Read each file under `framework/` and apply it to every surviving ticker. Each module outputs a directional read:

- `01-trend-analysis.md` — EMA stack, HH/HL structure, market alignment
- `02-momentum.md` — RSI, MACD, Stochastic, divergences
- `03-price-action.md` — Support/resistance, candle patterns, Fibonacci, VWAP, gaps
- `04-volume.md` — Volume vs. 20d average, accumulation/distribution
- `05-volatility.md` — Bollinger Bands squeeze, ATR-based stops, VIX regime
- `06-macro.md` — Futures direction, VIX trend, DXY, Treasury yields, calendar
- `07-sentiment.md` — News sentiment score, analyst actions, options flow
- `08-earnings-events.md` — Blackout enforcement (partially done in step 2)
- `09-multi-timeframe.md` — Weekly trend confirmation, hourly entry refinement
- `10-statistical-patterns.md` — Pattern matching, peer correlation, anomaly detection

---

## 5. Confluence score

Roll the 10 modules into 6 confluence categories:

| Category | BUY confirms when |
|---|---|
| **Trend** | Price > EMA20 > EMA50 > EMA200 AND HH/HL structure intact AND market (SPY/QQQ) not in confirmed downtrend |
| **Momentum** | RSI in 40–65 range AND MACD bullish cross or histogram rising above zero |
| **Volume** | Today's volume > 20-day average AND up-day volume > down-day volume trend |
| **Price Action** | Recent support hold OR clean breakout with retest OR Fibonacci level holding |
| **Macro** | SPY & QQQ futures green or mixed (not both red) AND VIX < 20 AND no major scheduled macro event today |
| **Sentiment** | Alpha Vantage news sentiment ≥ 0.15 AND no major negative headline keywords |

**Decision rule:**

| Score | Output |
|---|---|
| 5–6 / 6 confirms | HIGH confidence → `{TICKER} : BUY [HIGH]` |
| 4 / 6 confirms | MEDIUM confidence → `{TICKER} : BUY [MED]` |
| 3 / 6 confirms | Near signal → add to "Also watching" with missing categories noted |
| ≤ 2 / 6 confirms | WAIT — do not include |

**Counter-trend override:** If the weekly chart (from `09-multi-timeframe.md`) is counter-trend, raise the bar to 5/6 minimum even for a MEDIUM signal.

**Concurrent position cap:** After scoring all tickers, keep only the top `risk.max_concurrent_positions` (default 3) signals ranked by confluence score. If two tickers tie, use `10-statistical-patterns.md` pattern strength as the tiebreaker.

---

## 6. Risk gate (must pass to issue BUY)

For each ticker that passed step 5, compute:

- **Stop:** `entry − (confluence.atr_stop_multiplier × ATR14)` → default `entry − (1.5 × ATR14)`
- **Target:** `entry + (confluence.atr_target_multiplier × ATR14)` → default `entry + (3.0 × ATR14)`
- **Risk:reward:** `(target − entry) / (entry − stop)` must be ≥ `risk.min_rr` (default 2.0)

If R:R fails: downgrade to WAIT. Do not issue a signal. You may add to "Also watching" with a note about R:R.

Use the hourly chart (from step 3) only to refine the entry price — do not use it to override the daily signal.

---

## 7. Post to Slack

Use the Slack MCP connector. Channel = `runtime.slack_channel_id`. Format = Template A (or B/C) from `templates/slack-output.md`.

Rules:
- Post exactly ONE message per run
- Signal lines must use the exact format: `{TICKER} : BUY` (space-colon-space, uppercase ticker)
- Always include the macro snapshot line and the risk disclaimer footer
- Include "Earnings blackout" and "Also watching" sections if populated; omit them if empty
- If zero tickers qualify after all gates: use Template B with reason `No tickers cleared 4/6 confluence + 1:2 R:R today.`
- Do not post any additional messages before or after the main output

Do not update anything in this repo during a run. This is an output-only engine.
Never change the template file.

---

## 8. Hard rules — never violate

- Never issue a signal within 3 trading days of earnings
- Never claim guaranteed profits — always include the risk note
- Never issue more than `risk.max_concurrent_positions` (3) concurrent signals
- Never skip macro kill-switches
- Never issue a signal on fewer than 4 confirming categories
- Never issue a signal with R:R below `risk.min_rr` (1:2)
- Never execute trades — this is a signal engine, output only
- Never commit anything to this repo during a run
- Never post signals outside the 02:30–08:30 GMT window
- Never fabricate data — if data is unavailable, skip or use Template C

---

## 9. End the run

Post the Slack message. Done. Do not loop. Do not start another analysis pass. Exit cleanly.
