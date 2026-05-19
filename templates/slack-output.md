# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE}  {TIME} {TIMEZONE}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*Macro* | VIX {VIX} | SPY futs {SPY_FUTURES} | QQQ futs {QQQ_FUTURES} | DXY {DXY_DIRECTION}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY ✅  ({CONFIDENCE} — {SCORE}/6)
• Entry zone: ${ENTRY_LOW}–${ENTRY_HIGH}
• Stop: ${STOP}  |  Target: ${TARGET}  |  R:R 1:{RR}
• Confirms: {CONFIRM_LIST}
{PATTERN_NOTE}

[Repeat the block above for each BUY signal. Maximum 3 blocks.]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*Also watching* (scored but below threshold today)
{WATCHING_LIST}

*Excluded — earnings blackout*: {EARNINGS_EXCLUSIONS}

*Data errors / skipped*: {ERROR_LIST}

⚠️ _Research only — not financial advice. Never risk more than 1–2% of account per trade._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Field reference

| Placeholder | What to fill in |
|---|---|
| `{DATE}` | e.g. `Mon 19 May 2026` |
| `{TIME}` | e.g. `06:00` |
| `{TIMEZONE}` | From `config/runtime.json → timezone`, e.g. `GMT` or `BST` |
| `{VIX}` | Current VIX level, e.g. `18.4 ↓` |
| `{SPY_FUTURES}` | e.g. `+0.3%` or `flat` |
| `{QQQ_FUTURES}` | e.g. `+0.5%` |
| `{DXY_DIRECTION}` | e.g. `flat` / `strong (+0.4% 5d)` / `weak` |
| `{TICKER}` | Exact ticker, e.g. `NVDA` |
| `{CONFIDENCE}` | `HIGH` (5–6/6) or `MEDIUM` (4/6) |
| `{SCORE}` | Integer, e.g. `5` |
| `{ENTRY_LOW}` / `{ENTRY_HIGH}` | Hourly support lower bound and current pre-market price |
| `{STOP}` | Entry − (1.5 × ATR14), rounded to 2 d.p. |
| `{TARGET}` | Entry + (3.0 × ATR14), rounded to 2 d.p. |
| `{RR}` | Computed ratio, e.g. `2.0` or `2.4` |
| `{CONFIRM_LIST}` | Comma-separated confirmed categories, e.g. `Trend, Momentum, Volume, Price Action, Macro` |
| `{PATTERN_NOTE}` | Optional one-liner from module 10, e.g. `• Bull flag hit rate on NVDA last 12mo: 3/3, avg +2.1% (5d)` — omit line if no pattern |
| `{WATCHING_LIST}` | Tickers that scored 3/6 with a one-word reason, e.g. `META (volume weak), AAPL (RSI 71)` — use `none` if empty |
| `{EARNINGS_EXCLUSIONS}` | e.g. `NVDA (next: 2026-05-20), CRWD (next: 2026-06-09)` — use `none` if empty |
| `{ERROR_LIST}` | Tickers skipped due to data errors, e.g. `ARM (AV timeout)` — omit line if empty |

### MEDIUM confidence note

When `{CONFIDENCE}` is `MEDIUM` (4/6), append this sentence on its own line after the R:R line:

```
  ⚡ _MEDIUM confidence — consider half-size position._
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE}  {TIME} {TIMEZONE}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Next run: tomorrow pre-market unless the condition persists._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Kill-switch reasons

| Scenario | `{REASON}` text |
|---|---|
| VIX above threshold | `VIX at {VIX_LEVEL} — above the {VIX_MAX} kill-switch threshold` |
| FOMC announcement day | `FOMC rate decision today — no signals on Fed days` |
| CPI release day | `US CPI release today — extreme volatility expected` |
| NFP / jobs day | `US Non-Farm Payrolls today — extreme volatility expected` |
| All tickers in blackout | `All 12 tickers in earnings blackout` |

---

## Template C — No signals (scored but none qualified)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE}  {TIME} {TIMEZONE}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*Macro* | VIX {VIX} | SPY futs {SPY_FUTURES} | QQQ futs {QQQ_FUTURES} | DXY {DXY_DIRECTION}

No ticker reached the {MIN_CATEGORIES}/6 confluence threshold today.

*Closest setups* (highest scorers):
{CLOSEST_LIST}

*Excluded — earnings blackout*: {EARNINGS_EXCLUSIONS}

⚠️ _Research only — not financial advice._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

`{CLOSEST_LIST}` — up to 3 lines, format: `NVDA — 3/6 (missing: Volume, Sentiment)`
