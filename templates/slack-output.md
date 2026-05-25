# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.
Use Slack's mrkdwn format: *bold*, _italic_, `code`. No HTML tags.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
VIX: {VIX} ({VIX_DIRECTION}) | SPY fut: {SPY_FUTURES} | QQQ fut: {QQQ_FUTURES}
DXY: {DXY_LEVEL} ({DXY_5D_CHANGE}) | 10Y yield: {YIELD_LEVEL} ({YIELD_5D_CHANGE})

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*BUY SIGNALS — {SIGNAL_COUNT} of 3 max*
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*{TICKER}* — BUY | {CONFIDENCE} ({SCORE}/6)
Entry zone: {ENTRY_LOW}–{ENTRY_HIGH} | Stop: {STOP} | Target: {TARGET} | R:R 1:{RR}
Pattern: {PATTERN_TAG}
Confluence: {CONFLUENCE_SUMMARY}

{REPEAT_SIGNAL_BLOCK_FOR_EACH_BUY}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*ALSO WATCHING (3/6 — one confirm short)*
{ALSO_WATCHING_LIST}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
_Earnings blackout: {EARNINGS_BLACKOUT_LIST}_
_Data failures skipped: {DATA_FAILURES_LIST}_
_⚠️ Research use only — not financial advice. Size within your own risk rules._
```

### Field definitions

| Placeholder | What to put here |
|---|---|
| `{DATE}` | e.g. Mon 26 May 2025 |
| `{TIME}` | e.g. 06:02 — always GMT |
| `{VIX}` | Current VIX level to 2dp, e.g. 18.4 |
| `{VIX_DIRECTION}` | "falling ↓", "rising ↑", or "flat →" |
| `{SPY_FUTURES}` | e.g. +0.3% or -0.2% |
| `{QQQ_FUTURES}` | e.g. +0.5% or -0.1% |
| `{DXY_LEVEL}` | e.g. 104.2 |
| `{DXY_5D_CHANGE}` | e.g. +0.4% 5d — note impact on multinationals when >+0.5% |
| `{YIELD_LEVEL}` | 10Y Treasury yield e.g. 4.42% |
| `{YIELD_5D_CHANGE}` | e.g. -8bps 5d |
| `{SIGNAL_COUNT}` | Integer 1–3 |
| `{TICKER}` | e.g. NVDA |
| `{CONFIDENCE}` | HIGH (5–6/6) or MEDIUM (4/6) |
| `{SCORE}` | Integer 4–6 |
| `{ENTRY_LOW}` | Lower bound of hourly-refined entry zone |
| `{ENTRY_HIGH}` | Upper bound of entry zone (pre-market price or last close) |
| `{STOP}` | entry_low − (1.5 × ATR14), rounded to 2dp |
| `{TARGET}` | entry_low + (3.0 × ATR14), rounded to 2dp |
| `{RR}` | Ratio rounded to 1dp, must be ≥ 2.0 |
| `{PATTERN_TAG}` | e.g. "Bull flag", "EMA50 touch in uptrend", "Ascending triangle" — or "None" |
| `{CONFLUENCE_SUMMARY}` | Comma-separated list of confirming categories, e.g. "Trend ✓, Momentum ✓, Volume ✓, Price Action ✓" |
| `{ALSO_WATCHING_LIST}` | Comma-separated tickers that scored 3/6, e.g. "AAPL (3/6), GOOG (3/6)" — or "None" |
| `{EARNINGS_BLACKOUT_LIST}` | e.g. "AAPL (next: 2025-07-31), CRWD (next: 2025-06-09)" — or "None" |
| `{DATA_FAILURES_LIST}` | Tickers where Alpha Vantage returned no usable data — or "None" |

### Confidence label rules

- Score 5–6 → label `HIGH`
- Score 4 → label `MEDIUM` (add note "consider half-size position")
- Score ≤3 → do NOT include in BUY signals; this ticker goes to ALSO WATCHING or is silent

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Resume normal operation tomorrow unless the trigger persists._
_⚠️ Research use only — not financial advice._
```

### Kill-switch reason strings

| Trigger | `{REASON}` text |
|---|---|
| VIX > 30 | VIX at {VIX_LEVEL} — above 30 kill-switch. All signals suspended. |
| FOMC day | Federal Reserve rate decision today. No signals on FOMC announcement days. |
| CPI release | US CPI data releasing today. Extreme volatility risk — no signals. |
| NFP/jobs day | US Non-Farm Payrolls releasing today. Extreme volatility risk — no signals. |

---

## Template C — No signals (all tickers scored ≤3, no kill-switch)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SCAN — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
VIX: {VIX} ({VIX_DIRECTION}) | SPY fut: {SPY_FUTURES} | QQQ fut: {QQQ_FUTURES}

*No BUY signals today.* Highest scorer: {TOP_TICKER} at {TOP_SCORE}/6.

*ALSO WATCHING (closest to threshold)*
{ALSO_WATCHING_LIST}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
_Earnings blackout: {EARNINGS_BLACKOUT_LIST}_
_⚠️ Research use only — not financial advice._
```
