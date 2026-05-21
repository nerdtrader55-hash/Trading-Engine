# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
VIX: {VIX} ({VIX_TREND}) | Futures: {FUTURES_DIRECTION} | DXY: {DXY_NOTE} | 10Y yield: {YIELD_NOTE}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*{TICKER}* — BUY  |  Confidence: *{HIGH/MEDIUM}*
Entry zone: *${ENTRY_LOW}–${ENTRY_HIGH}*  |  Stop: ${STOP}  |  Target: ${TARGET}  |  R:R 1:{RR}
Confluence: {SCORE}/6  {TREND_TICK} Trend · {MOM_TICK} Momentum · {VOL_TICK} Volume · {PA_TICK} Price Action · {MACRO_TICK} Macro · {SENT_TICK} Sentiment
_{KEY_OBSERVATION}_

---

*{TICKER2}* — BUY  |  Confidence: *{HIGH/MEDIUM}*
Entry zone: *${ENTRY_LOW}–${ENTRY_HIGH}*  |  Stop: ${STOP}  |  Target: ${TARGET}  |  R:R 1:{RR}
Confluence: {SCORE}/6  {TREND_TICK} Trend · {MOM_TICK} Momentum · {VOL_TICK} Volume · {PA_TICK} Price Action · {MACRO_TICK} Macro · {SENT_TICK} Sentiment
_{KEY_OBSERVATION}_

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*ALSO WATCHING* _(scored 3/6 — not signalling today)_
{WATCHING_LIST}

_Excluded (earnings within 3 days): {EXCLUDED_LIST}_
_Skipped (data error): {ERROR_LIST}_

> ⚠️ _Research only — not financial advice. Past patterns do not guarantee future results. Never risk more than you can afford to lose._
```

### Field reference

| Placeholder | What to put there |
|---|---|
| `{DATE}` | e.g. `Thu 21 May 2026` |
| `{TIME}` | e.g. `06:00` |
| `{VIX}` | Current VIX level, 1 dp, e.g. `16.4` |
| `{VIX_TREND}` | `falling` / `rising` / `flat` |
| `{FUTURES_DIRECTION}` | `SPY +0.3% QQQ +0.5%` or similar |
| `{DXY_NOTE}` | `104.2 (+0.1% 5d)` |
| `{YIELD_NOTE}` | `4.28% (+2bps 5d)` |
| `{TICKER}` | Exact ticker symbol, e.g. `NVDA` |
| `{HIGH/MEDIUM}` | `HIGH` (5–6/6) or `MEDIUM` (4/6) |
| `{ENTRY_LOW}` / `{ENTRY_HIGH}` | Hourly support level / pre-market price |
| `{STOP}` | entry − (1.5 × ATR14) |
| `{TARGET}` | entry + (3.0 × ATR14) |
| `{RR}` | Actual R:R, e.g. `2.1` |
| `{SCORE}` | Number of confirming categories, e.g. `5` |
| `{*_TICK}` | `✅` if category confirms, `⬜` if not |
| `{KEY_OBSERVATION}` | One-line pattern or context note, e.g. `Bull flag resolving after EMA50 touch; sector rotation into semis accelerating` |
| `{WATCHING_LIST}` | Comma-separated tickers that scored 3/6 |
| `{EXCLUDED_LIST}` | Tickers skipped for earnings, e.g. `AAPL (next: 2026-07-31)` — omit line if none |
| `{ERROR_LIST}` | Tickers skipped due to data errors — omit line if none |

### Confidence → position-size note

Include this sentence for MEDIUM-confidence signals:
`_Position size: consider 50% of normal size given medium confidence._`

### No-signal variant (engine ran, no tickers qualified — not a kill-switch)

Use Template A structure but replace the signals block with:

```
*No qualifying signals today.*
All tickers scored ≤3/6 or failed the R:R gate. Stand aside.
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Reason codes: VIX>{VIX_MAX} | FOMC day | CPI/PCE release | NFP release_

> ⚠️ _Research only — not financial advice._
```

### Kill-switch reason strings

| Trigger | `{REASON}` string |
|---|---|
| VIX too high | `VIX at {VIX} — above the {VIX_MAX} threshold` |
| FOMC day | `FOMC rate decision today — extreme intraday volatility expected` |
| CPI/PCE | `CPI/PCE data release today — signal engine paused` |
| NFP | `Non-Farm Payrolls day — signal engine paused` |
