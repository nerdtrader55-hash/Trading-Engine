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

## 0.5. Signal window check (fail fast)

Check current UTC time. Signals are only generated during the **02:30–08:30 GMT** window (pre-market, before the US market opens at ~13:30–14:30 GMT).

- If the current GMT time is **before 02:30 or after 08:30**, post Template B with reason `Outside signal window (02:30–08:30 GMT)` and **stop**.
- This window is configurable in `config/runtime.json` under `signal_window_gmt`.

## 1. Macro kill-switches (check first, fail fast)

Pull these via Alpha Vantage MCP or `web_search`:

| Check | Condition to stop | Source |
|---|---|---|
| VIX level | `> runtime.macro_kill_switches.vix_max` (default 30) | Alpha Vantage `GLOBAL_QUOTE` symbol `^VIX` |
| FOMC day | Today is on the Fed calendar | `web_search`: "FOMC meeting today" |
| CPI release | Today is CPI release day | `web_search`: "US CPI release date this week" |
| NFP release | Today is jobs day | `web_search`: "US non-farm payrolls release date this week" |

If **any** fire: post Template B from `templates/slack-output.md`, then **stop**. Do not analyze tickers.

**VIX 20–25 zone (not a kill-switch, but raise the bar):**
If VIX is between `runtime.macro_kill_switches.vix_high_confidence_only_above` (default 20) and `vix_max` (default 30):
- Do NOT stop — continue analysis
- Raise the minimum confluence threshold to **5/6** for all BUY signals this run
- Note this in the Slack message

## 2. Earnings blackout (per-ticker)

For each ticker, call Alpha Vantage `EARNINGS_CALENDAR` (3-month horizon). Apply blackout rules from `framework/08-earnings-events.md` and `config/runtime.json` under `earnings`:

- Exclude tickers within `blackout_trading_days_before` (default 3) trading days of next earnings
- Exclude tickers within `blackout_trading_days_after` (default 1) trading day after earnings
- Note excluded tickers in the Slack footer

## 3. Data pull per surviving ticker

For each ticker that passes the earnings blackout, fetch from Alpha Vantage MCP:

| What | Alpha Vantage function | Why |
|---|---|---|
| Daily OHLCV (260 days) | `TIME_SERIES_DAILY` | Trend, support/resistance, gap analysis |
| Hourly OHLCV (last 5 days) | `TIME_SERIES_INTRADAY` interval=60min | Entry refinement + confirmed candle close |
| EMA 20 / 50 / 200 daily | `EMA` time_period=20/50/200 | Trend alignment |
| RSI 14 daily | `RSI` time_period=14 | Momentum |
| MACD daily | `MACD` (12,26,9) | Momentum crossovers |
| Stochastic daily | `STOCH` (14,3,3) | Momentum confirmation |
| Bollinger Bands daily | `BBANDS` time_period=20 | Volatility squeeze |
| ATR 14 daily | `ATR` time_period=14 | Stop-loss sizing |
| VWAP intraday | `VWAP` | Institutional anchor |
| News sentiment | `NEWS_SENTIMENT` tickers=<TICKER> | Sentiment score |

Also pull **once per run** (not per ticker):

- SPY and QQQ daily (`TIME_SERIES_DAILY`) — market direction
- `^VIX` quote — already pulled in step 1
- `DXY` quote — dollar strength context

**Hourly candle close rule:** Before scoring, verify the most recent hourly candle has fully closed (i.e., the candle's close time is in the past). Do not issue signals based on a candle that is still forming. If the last complete hourly candle is more than 2 hours old, note this in the Slack footer.

If any required call returns null/error for a ticker, skip that ticker and add it to the failures list.

## 4. Pre-market data processing

Before running the framework, compute and record these per-ticker values from the fetched data:

- **Gap direction and size:** today's pre-market open vs. yesterday's close. Gap >2% = flag.
- **Unusual pre-market volume:** pre-market volume run-rate vs. typical pre-market (roughly 10–20% of daily avg). If >3× typical, flag.
- **Key pre-market levels:** overnight high, overnight low, pre-market VWAP (from intraday data).
- **Nearest S/R levels:** three daily pivot highs/lows above and below current price.

## 5. Apply the 10-module framework

Read each file under `framework/` and apply it to each surviving ticker. Each module outputs a directional read:

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

## 6. Confluence score

Roll the 10 modules into 6 confluence categories (this is the gate):

| Category | BUY confirms when |
|---|---|
| Trend | Price > EMA20 > EMA50 > EMA200 (bull stack) |
| Momentum | RSI 40–65 AND MACD bullish cross or above signal |
| Volume | Today's volume > 20-day avg AND rising on up-days |
| Price action | Recent support hold OR clean breakout with retest |
| Macro | SPY & QQQ futures green AND VIX < 20 |
| Sentiment | Alpha Vantage news sentiment ≥ 0.15 AND no major negative headlines |

**Decision rule:**

Effective threshold = `runtime.confluence.min_categories_for_buy` (default 4), raised to 5 if:
- VIX is in the 20–25 zone (see step 1), OR
- Weekly chart is counter-trend to daily (see `framework/09-multi-timeframe.md`)

| Score | Action |
|---|---|
| 5–6 confirms | HIGH confidence BUY |
| 4 confirms (and threshold is 4) | MEDIUM confidence BUY — note smaller position size |
| ≤3 confirms, or score < effective threshold | WAIT — no signal output |

## 7. Risk gate (must pass to issue BUY)

Compute using `config/runtime.json → risk`:

- **Stop:** entry − (`stop_atr_multiplier` × ATR14) = entry − (1.5 × ATR14)
- **Target:** entry + (`target_atr_multiplier` × ATR14) = entry + (3.0 × ATR14)
- **Risk:reward:** must be ≥ `min_rr` (default 2.0 = 1:2)

If R:R fails, downgrade to WAIT.

Cap total BUY signals at `runtime.confluence.max_signals_per_run` (default 3). If more tickers qualify, keep the highest-confluence ones; break ties using pattern-tag count then historical-analog hit rate from `framework/10-statistical-patterns.md`.

## 8. Post to Slack

Use the Slack connector. Channel = `runtime.slack_channel_id`. Format = `templates/slack-output.md` Template A. Use markdown that Slack will render correctly (no HTML tags).

Signal line format per ticker:

```
{TICKER} : BUY
```

End the block with the separator:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

Do not update anything into stock files or this repo during the run. This is an output-only engine.

Post exactly one template-based message to Slack. Do not post any other messages during the run.

Never change the template file.

## 9. Hard rules — never violate

These come from section 9 of the original spec:

- Never issue a signal within 3 trading days of earnings
- Never claim guaranteed profits — include the risk note in the Slack message
- Never issue more than 3 concurrent signals
- Never skip macro checks
- Never issue a signal on fewer than 4 confirming categories (or the effective elevated threshold)
- Never issue a signal with R:R below 1:2
- Never execute trades — this is a signal engine, output only
- Never commit anything to this repo during a run
- Never post signals to Slack outside of the template format
- Never issue signals outside the 02:30–08:30 GMT window

## 10. End the run

Post the Slack message. Done. Do not loop. Do not start another analysis pass. Exit cleanly.
