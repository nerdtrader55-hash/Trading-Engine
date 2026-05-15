# Slack Output Templates

The routine sends ONE Slack message per run. Use the template that matches the outcome.
Never send more than one Slack message per run. Never deviate from these templates.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO*  SPY {SPY_FUTURES_PCT}%  |  QQQ {QQQ_FUTURES_PCT}%  |  VIX {VIX}  |  DXY {DXY}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY   [{CONFIDENCE} — {SCORE}/6]

Confirmed: {CONFIRMED_CATEGORIES}
Entry zone: {ENTRY_LOW} – {ENTRY_HIGH}
Stop: {STOP}  |  Target: {TARGET}  |  R:R {RR}
Pattern: {PATTERN_NOTE}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*ALSO WATCHING (3/6 — not yet)*
{WATCHING_LIST}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

_⚠️  Signal engine only — not financial advice._
_Never risk more than 1–2% of account per trade. Always use stops._
{FOOTER_NOTES}
```

### Field guide

| Placeholder | What to put here |
|---|---|
| `{DATE}` | Today's date — YYYY-MM-DD |
| `{TIME}` | Time this message was sent, e.g. 06:02 |
| `{SPY_FUTURES_PCT}` | Pre-market SPY % change, e.g. +0.4% or -0.3% |
| `{QQQ_FUTURES_PCT}` | Pre-market QQQ % change |
| `{VIX}` | Current VIX level, e.g. 17.3 |
| `{DXY}` | Current DXY level, e.g. 104.2 |
| `{TICKER}` | Ticker symbol — ARM, NVDA, META, GOOG, AMZN, UBER, AAPL, CRWD, GEV, V, SPGI, MCO |
| `{CONFIDENCE}` | HIGH (5–6/6) or MEDIUM (4/6) |
| `{SCORE}` | Numeric confluence score, e.g. 5 |
| `{CONFIRMED_CATEGORIES}` | Comma list of confirming categories, e.g. Trend, Momentum, Volume, Price Action, Macro |
| `{ENTRY_LOW}` | Lower bound of entry zone from hourly chart |
| `{ENTRY_HIGH}` | Upper bound of entry zone (pre-market price or last close) |
| `{STOP}` | Entry − (1.5 × ATR14), rounded to 2dp |
| `{TARGET}` | Entry + (3.0 × ATR14), rounded to 2dp |
| `{RR}` | Computed risk:reward, e.g. 1:2.0 |
| `{PATTERN_NOTE}` | One-line pattern and/or analog note, e.g. "Bull flag — 3/3 hit rate +2.1% avg 5d" or "—" if none |
| `{WATCHING_LIST}` | Bullet list of tickers at 3/6 with the one missing category, e.g. "• GOOG — needs Volume" |
| `{FOOTER_NOTES}` | Earnings-excluded tickers and any data-fetch failures. Omit line if empty. Format: "Earnings blackout: AAPL (next: 2026-07-29), NVDA (next: 2026-08-20)  |  Data unavailable: UBER" |

### Multi-signal block

If 2 or 3 BUY signals fire on the same day, repeat the signal block for each ticker, separated by the divider line. Example (2 signals):

```
{TICKER_1} : BUY   [HIGH — 5/6]

Confirmed: Trend, Momentum, Volume, Price Action, Macro
Entry zone: 123.40 – 124.80
Stop: 119.20  |  Target: 131.60  |  R:R 1:2.1
Pattern: Bull flag — 3/3 hit rate +2.1% avg 5d

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER_2} : BUY   [MEDIUM — 4/6]

Confirmed: Trend, Momentum, Price Action, Sentiment
Entry zone: 87.10 – 88.50
Stop: 84.30  |  Target: 91.90  |  R:R 1:2.0
Pattern: EMA50 touch in uptrend
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

Macro snapshot:  SPY {SPY_FUTURES_PCT}%  |  QQQ {QQQ_FUTURES_PCT}%  |  VIX {VIX}

_Engine will resume normal scoring on the next eligible trading day._
```

Kill-switch reasons (use exact wording):

| Trigger | `{REASON}` text |
|---|---|
| VIX above threshold | `VIX at {VIX} — above the {VIX_MAX} kill-switch level` |
| FOMC day | `FOMC announcement day — no signals on Fed days` |
| CPI release day | `US CPI release today — extreme volatility risk` |
| NFP/jobs day | `US Non-Farm Payrolls day — extreme volatility risk` |

---

## Template C — Engine ran, macro OK, but no ticker scored ≥ 4/6

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO*  SPY {SPY_FUTURES_PCT}%  |  QQQ {QQQ_FUTURES_PCT}%  |  VIX {VIX}  |  DXY {DXY}

No ticker scored ≥ {MIN_CATEGORIES}/6 today. Highest: {TOP_TICKER} at {TOP_SCORE}/6.

*CLOSEST TO SIGNAL*
{WATCHING_LIST}

_Sit on hands — no edge today. Next run tomorrow pre-market._
{FOOTER_NOTES}
```

---

## Formatting rules (Slack markdown)

- `*text*` = bold
- `_text_` = italic
- Use the horizontal divider `━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━` exactly as shown
- Do NOT use HTML tags (`<b>`, `<br>`, etc.)
- Do NOT use triple backtick code blocks inside the Slack message
- Bullet lists: prefix each item with `•` (Unicode bullet, not `-`)
- Percentage changes: always include sign — `+0.4%` or `-0.3%`, not just `0.4%`
