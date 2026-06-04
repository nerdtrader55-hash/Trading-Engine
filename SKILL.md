---
name: pre-market-signal-engine
description: Use when generating daily pre-market BUY signals for the 12-ticker universe defined in config/stocks.json. Triggered by the scheduled routine in this repo. Coordinates Alpha Vantage MCP data pulls, applies the 10-module 40-50 point analysis framework, enforces risk and macro kill-switches, scores confluence across 6 categories, and posts the result to Slack.
---

# Pre-Market Signal Engine — Operational Playbook

This is the file the routine runs. Follow it in order. Each step has a clear stop condition.

## 0. Read configuration

Always start by reading:

- `config/stocks.json` — the 12-ticker universe
- `config/runtime.json` — all thresholds, risk caps, kill-switch levels, Slack channel
- `templates/slack-output.md` — exact output format; do not deviate from it

Do not proceed until all three files load successfully.

**Key runtime.json values to cache for this run:**

| Key | Default | Used in |
|---|---|---|
| `confluence.min_categories_for_buy` | 4 | Step 5 |
| `confluence.min_categories_counter_trend` | 5 | Step 5 |
| `confluence.max_signals_per_run` | 3 | Step 7 |
| `risk.min_rr` | 2.0 | Step 6 |
| `risk.atr_stop_multiplier` | 1.5 | Step 6 |
| `risk.atr_target_multiplier` | 3.0 | Step 6 |
| `macro_kill_switches.vix_max` | 30 | Step 1 |
| `macro_kill_switches.vix_high_confidence_only_above` | 25 | Step 5 |
| `macro_kill_switches.vix_caution_above` | 20 | Step 7 |
| `momentum.rsi_buy_low` / `rsi_buy_high` | 40 / 65 | Step 5 |
| `earnings.blackout_trading_days_before` | 3 | Step 2 |
| `earnings.blackout_trading_days_after` | 1 | Step 2 |
| `sentiment.min_score_for_confirm` | 0.15 | Step 5 |
| `slack_channel_id` | — | Step 7 |

## 1. Macro kill-switches (check first, fail fast)

Pull these via Alpha Vantage MCP or `web_search`:

| Check | Condition to stop | Source |
|---|---|---|
| VIX level | `> runtime.macro_kill_switches.vix_max` | Alpha Vantage `GLOBAL_QUOTE` symbol `^VIX` |
| FOMC day | Today is on the Fed calendar | `web_search`: "FOMC meeting today {date}" |
| CPI release | Today is CPI release day | `web_search`: "US CPI release date this week" |
| NFP release | Today is jobs day | `web_search`: "US non-farm payrolls release date this week" |

If **any** fire: post Template B from `templates/slack-output.md` filling in `{REASON}` from the verbatim list in that file, then **stop**. Do not analyse tickers.

## 2. Earnings blackout (per-ticker)

For each ticker in the universe:

1. Call Alpha Vantage `EARNINGS_CALENDAR` with `horizon=3month` on the ticker symbol.
2. Find the nearest upcoming earnings date.
3. Count the trading days between today and that date (Mon–Fri, excluding NYSE holidays).
4. If the ticker is within `earnings.blackout_trading_days_before` trading days before earnings, or within `earnings.blackout_trading_days_after` trading days after a prior earnings date → **exclude** from analysis.
5. Record excluded tickers and their next earnings date for the Slack footer.

Also apply `earnings.post_earnings_raised_bar`: if today is the first eligible day back after the post-earnings blackout, require 5/6 confluence (not 4/6) for that ticker.

## 3. Data pull per surviving ticker

For each ticker that passes the earnings blackout, fetch from Alpha Vantage MCP:

| What | Alpha Vantage function | Why |
|---|---|---|
| Daily OHLCV (260 days) | `TIME_SERIES_DAILY` | Trend, S/R, gap analysis, patterns |
| Hourly OHLCV (last 5 days) | `TIME_SERIES_INTRADAY` interval=60min | Entry zone refinement only |
| EMA 20 / 50 / 200 daily | `EMA` time_period=20 / 50 / 200 | Trend alignment |
| RSI 14 daily | `RSI` time_period=14 | Momentum |
| MACD daily | `MACD` fastperiod=12 slowperiod=26 signalperiod=9 | Momentum crossovers |
| Stochastic daily | `STOCH` fastkperiod=14 slowkperiod=3 slowdperiod=3 | Momentum confirmation |
| Bollinger Bands daily | `BBANDS` time_period=20 | Volatility squeeze / regime |
| ATR 14 daily | `ATR` time_period=14 | Stop-loss and target sizing |
| VWAP intraday | `VWAP` interval=60min | Institutional anchor level |
| News sentiment (last 24h) | `NEWS_SENTIMENT` tickers=<TICKER> | Sentiment score + headlines |
| Earnings calendar | `EARNINGS_CALENDAR` horizon=3month | Already checked in step 2 — skip if done |

Also pull **once per run** (not per ticker):

- SPY daily OHLCV — market direction
- QQQ daily OHLCV — Nasdaq direction
- `^VIX` quote — already pulled in step 1, reuse
- DXY quote — dollar strength context
- 10Y Treasury yield — use `web_search` "US 10 year Treasury yield today" if Alpha Vantage does not have it

If any required call returns null or an error for a ticker, skip that ticker and add it to a data-failure list for the Slack footer.

## 4. Apply the 10-module framework

Read each file under `framework/` and apply it to the ticker's fetched data. Each module outputs a directional read:

| Module | Output type |
|---|---|
| `01-trend-analysis.md` | `STRONG_BULL` / `BULL` / `NEUTRAL` / `BEAR` / `STRONG_BEAR` |
| `02-momentum.md` | `BULLISH` / `NEUTRAL` / `BEARISH` + divergence flag |
| `03-price-action.md` | S/R levels, candle pattern, fib position, gap tag |
| `04-volume.md` | `STRONG_CONFIRM` / `CONFIRM` / `NEUTRAL` / `WEAK` |
| `05-volatility.md` | `SQUEEZE` / `NORMAL` / `EXPANDING` / `EXTREME` + ATR stop/target |
| `06-macro.md` | `SUPPORTIVE` / `NEUTRAL` / `HEADWIND` + macro snapshot |
| `07-sentiment.md` | `STRONG_POSITIVE` / `POSITIVE` / `NEUTRAL` / `NEGATIVE` / `STRONG_NEGATIVE` |
| `08-earnings-events.md` | `PASS` / `BLACKOUT` (already done in step 2) |
| `09-multi-timeframe.md` | Weekly trend label + entry zone (low / high) |
| `10-statistical-patterns.md` | Pattern tags + historical analog note |

## 5. Confluence score

Roll the 10 modules into 6 confluence categories (this is the gate):

| Category | BUY confirms when |
|---|---|
| **Trend** | `BULL` or `STRONG_BULL` AND HH/HL structure intact AND market alignment neutral or supportive |
| **Momentum** | RSI in [`rsi_buy_low`, `rsi_buy_high`] AND MACD bullish (cross within last 5 sessions OR line above signal with rising histogram). Bearish divergence flag = automatic non-confirm. |
| **Volume** | Volume output is `CONFIRM` or `STRONG_CONFIRM`. `WEAK` = automatic non-confirm regardless of other signals. |
| **Price Action** | Bullish candle pattern at support/EMA50/fib level OR clean breakout above resistance with conviction. |
| **Macro** | Module 06 output `SUPPORTIVE` or `NEUTRAL` (not `HEADWIND`) AND VIX < `vix_caution_above` |
| **Sentiment** | News sentiment score ≥ `sentiment.min_score_for_confirm` AND no flagged negative headline keywords |

**Decision rule:**

- 5–6 confirms → `HIGH` confidence BUY
- 4 confirms → `MEDIUM` confidence BUY (50% position size note)
- ≤ 3 confirms → `WAIT` — ticker moves to ALSO WATCHING if score is exactly 3

**Threshold adjustments from module 09:**

- Weekly bull + daily bull (with-trend) → standard threshold from `confluence.min_categories_for_buy`
- Weekly bear + daily bull (counter-trend) → raise threshold to `confluence.min_categories_counter_trend`
- Weekly bear + daily bear → no BUY allowed, skip ticker entirely

**VIX elevated adjustment:**

- If VIX > `vix_high_confidence_only_above` (default 25) → only issue BUY if score is 5 or 6 (not 4)

## 6. Risk gate (must pass to issue BUY)

Using the ATR values from module 05:

- **Stop:** `entry_zone_high − (atr_stop_multiplier × ATR14)`
- **Target:** `entry_zone_high + (atr_target_multiplier × ATR14)`
- **R:R:** `(target − entry) / (entry − stop)` — must be ≥ `risk.min_rr`

If R:R fails, downgrade to WAIT regardless of confluence score.

Round stop and target to 2 decimal places. If entry zone high = current pre-market price, use last session's close as fallback if pre-market is flat.

## 7. Post to Slack

Use the Slack MCP connector. Post to the channel in `runtime.slack_channel_id`.

One message per run. Use Template A from `templates/slack-output.md` and populate every placeholder:

**Header block**
- `{DATE}` — today's date in "Weekday D Mon YYYY" format, e.g. `Wed 4 Jun 2026`
- `{TIME}` — current GMT time at moment of posting, e.g. `05:47`

**Macro snapshot block** (always present)
- `{FUTURES_STATUS}` — e.g. `ES +0.3% / NQ +0.5% (both green)` or `ES −0.2% / NQ flat (mixed)`
- `{VIX_LEVEL}`, `{VIX_CHANGE}` — from step 1 data
- `{DXY_LEVEL}`, `{DXY_5D_CHANGE}` — from once-per-run pull
- `{YIELD_LEVEL}`, `{YIELD_5D_CHANGE}` — from web_search

**BUY SIGNALS block** (repeat signal entry for each qualifying ticker, max `max_signals_per_run`)

Per signal:
- `{TICKER}` — exact symbol, uppercase
- `{CONFIDENCE_LABEL}` — `HIGH` (5–6/6) or `MEDIUM` (4/6)
- `{SCORE}` — integer
- `{ENTRY_LOW}` / `{ENTRY_HIGH}` — from module 09 entry zone
- `{STOP}` / `{TARGET}` — from step 6 risk gate
- `{RR_RATIO}` — from step 6
- `{PATTERN_TAG}` — from module 10 (e.g. `bull flag`, `50d EMA touch`, `ascending triangle`)
- `{CONFLUENCE_SUMMARY}` — comma-separated confirmed categories with ✓, e.g. `Trend ✓ Momentum ✓ Volume ✓ Price Action ✓ Macro ✓`
- `{HISTORICAL_ANALOG}` — from module 10; omit the analog line entirely if fewer than 3 prior occurrences

If zero signals qualify, still post Template A but omit the BUY SIGNALS block and replace it with:
`No signals today — all tickers scored below threshold or excluded.`

**ALSO WATCHING block** (tickers that scored exactly 3/6 — close but not enough)
- List ticker and score; omit if none

**Footer**
- `{EXCLUSIONS_FOOTER}` — list tickers excluded for earnings, data failures; omit the line if none

**Tie-breaking when more than `max_signals_per_run` tickers qualify:**
1. Highest confluence score wins
2. If tied on score: more pattern tags from module 10 wins
3. If still tied: higher historical analog hit rate wins
4. Losing tickers move to ALSO WATCHING

## 8. Hard rules — never violate

- Never issue a signal within `earnings.blackout_trading_days_before` trading days of earnings
- Never claim guaranteed profits — include the risk disclaimer from the template verbatim
- Never issue more than `confluence.max_signals_per_run` concurrent signals
- Never skip macro kill-switch checks
- Never issue a signal on fewer than `confluence.min_categories_for_buy` confirming categories
- Never issue a signal with R:R below `risk.min_rr`
- Never execute trades — this is a signal engine, output only
- Never commit anything to this repo during a run
- Never post to Slack outside the signal template format
- Never fabricate prices, levels, or indicator values — if data is unavailable, skip that field

## 9. End the run

Post the Slack message. Done. Do not loop. Do not start another analysis pass. Exit cleanly.
