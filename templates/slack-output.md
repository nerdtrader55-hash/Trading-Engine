# Slack Output Templates

The routine sends **one** Slack message per run. Choose the template that matches the outcome. Use Slack-flavoured markdown (bold = `*text*`, italic = `_text_`). No HTML.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
• Futures: SPY {SPY_FUTURES_DIRECTION} | QQQ {QQQ_FUTURES_DIRECTION}
• VIX: {VIX_LEVEL} — {VIX_STATUS}
• DXY: {DXY_LEVEL} ({DXY_5D_CHANGE})
• 10Y yield: {YIELD_LEVEL}% ({YIELD_5D_CHANGE}bps)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SIGNALS ({SIGNAL_COUNT}/3 max)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*{TICKER}* — BUY | {CONFIDENCE} confidence ({SCORE}/6)
Entry zone: {ENTRY_LOW} – {ENTRY_HIGH}
Stop: {STOP} | Target: {TARGET} | R:R {RR}
Trend: {WEEKLY_TREND} weekly / {DAILY_TREND} daily
{PATTERN_NOTE}
_{TOP_HEADLINE}_

[Repeat block for each BUY signal]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*ALSO WATCHING* (scored but below threshold today)
• {TICKER}: {SCORE}/6 — {ONE_LINE_REASON}
[List up to 3; omit if none]

_Earnings blackout (next date): {TICKER (YYYY-MM-DD)}, ..._
_Data failures (skipped): {TICKER}, ... | none if clean_

⚠️ _Research only. Not financial advice. Never risk more than you can afford to lose._
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Reason codes: VIX>{vix_max} | FOMC day | CPI release | NFP release | PCE release_

⚠️ _Research only. Not financial advice._
```

---

## Field reference

| Placeholder | Source | Example |
|---|---|---|
| `{DATE}` | Today's date in Europe/London TZ | `Thu 15 May 2026` |
| `{TIME}` | Current time GMT | `06:02` |
| `{SPY_FUTURES_DIRECTION}` | Pre-market % change | `+0.4%` |
| `{QQQ_FUTURES_DIRECTION}` | Pre-market % change | `+0.6%` |
| `{VIX_LEVEL}` | Current VIX | `17.3` |
| `{VIX_STATUS}` | Derived from vix_context thresholds | `Normal` / `Caution` / `Tighten` |
| `{DXY_LEVEL}` | Current DXY | `104.2` |
| `{DXY_5D_CHANGE}` | 5-day change | `+0.3%` |
| `{YIELD_LEVEL}` | 10Y Treasury yield | `4.42` |
| `{YIELD_5D_CHANGE}` | 5-day change in bps | `+8` |
| `{TICKER}` | Exact AV ticker symbol | `NVDA` |
| `{CONFIDENCE}` | HIGH (5-6/6) or MEDIUM (4/6) | `HIGH` |
| `{SCORE}` | Confluence score | `5` |
| `{ENTRY_LOW}` | Hourly support within 2 ATR | `$118.40` |
| `{ENTRY_HIGH}` | Pre-market last price or close | `$120.10` |
| `{STOP}` | entry − (1.5 × ATR14) | `$115.20` |
| `{TARGET}` | entry + (3.0 × ATR14) | `$126.50` |
| `{RR}` | Target distance / Stop distance | `1:2.2` |
| `{WEEKLY_TREND}` | From module 09 | `BULL` |
| `{DAILY_TREND}` | From module 01 | `STRONG_BULL` |
| `{PATTERN_NOTE}` | From module 10 (optional, 1 line) | `Bull flag — hit rate 3/3 last 12mo, avg +2.1% over 5d` |
| `{TOP_HEADLINE}` | Top AV sentiment headline | `NVDA: Morgan Stanley raises PT to $160` |
| `{REASON}` | Kill-switch label | `VIX at 32.1 (max: 30)` |
