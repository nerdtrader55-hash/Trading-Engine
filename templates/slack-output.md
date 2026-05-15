# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.
Fill every `{PLACEHOLDER}` with real data — never leave a placeholder unfilled.
Use Slack mrkdwn syntax only (no HTML). One message per run, no follow-up messages.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} | {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MARKET SNAPSHOT*
VIX {VIX} ({VIX_TREND}) · SPY Futures {SPY_FUTURES_PCT}% · QQQ Futures {QQQ_FUTURES_PCT}%
DXY {DXY} · 10Y Yield {YIELD_10Y}%

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟢 *{TICKER}* ({NAME}) — *BUY* | {CONFIDENCE} CONFIDENCE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Confluence  {SCORE}/6 → {CONFIRMED_CATEGORIES}
Entry zone  {ENTRY_LOW} – {ENTRY_HIGH}
Stop        {STOP} (−{STOP_PCT}% · 1.5× ATR)
Target      {TARGET} (+{TARGET_PCT}% · 3.0× ATR)
R:R         1:{RR}
Key reason  {PRIMARY_REASON}
Pattern     {PATTERN_NOTE}

[Repeat the block above for each BUY signal, up to 3 maximum]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*ALSO WATCHING* (3/6 — one confirm short)
• {WATCHING_TICKER} ({WATCHING_NAME}): missing {MISSING_CATEGORY}
[List each 3/6 ticker as its own bullet. Omit section entirely if none.]

*EXCLUDED — earnings blackout*
• {EXCLUDED_TICKER} (next earnings: {EARNINGS_DATE})
[List each excluded ticker as its own bullet. Omit section entirely if none.]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ _Signal engine output only · Not financial advice · Size within your own risk envelope_
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} | {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Next scheduled run: tomorrow 06:00 GMT_
⚠️ _Signal engine output only · Not financial advice_
```

---

## Template C — Scan complete, no qualifying setups

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SCAN — {DATE} | {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MARKET SNAPSHOT*
VIX {VIX} ({VIX_TREND}) · SPY Futures {SPY_FUTURES_PCT}% · QQQ Futures {QQQ_FUTURES_PCT}%
DXY {DXY} · 10Y Yield {YIELD_10Y}%

*No BUY signals today.* No setup reached {MIN_CATEGORIES}/6 confluence threshold.

*ALSO WATCHING* (3/6 — one confirm short)
• {WATCHING_TICKER} ({WATCHING_NAME}): missing {MISSING_CATEGORY}
[List each 3/6 ticker as its own bullet. Omit section entirely if none.]

*EXCLUDED — earnings blackout*
• {EXCLUDED_TICKER} (next earnings: {EARNINGS_DATE})
[List each excluded ticker as its own bullet. Omit section entirely if none.]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ _Signal engine output only · Not financial advice · Size within your own risk envelope_
```

---

## Placeholder reference

| Placeholder | Source | Example |
|---|---|---|
| `{DATE}` | Today's date | `Thu 15 May 2025` |
| `{TIME}` | Current UTC/GMT time | `06:02` |
| `{VIX}` | Alpha Vantage GLOBAL_QUOTE ^VIX | `18.4` |
| `{VIX_TREND}` | Rising / Falling / Flat vs. 5d ago | `Falling` |
| `{SPY_FUTURES_PCT}` | SPY pre-market % change | `+0.4` |
| `{QQQ_FUTURES_PCT}` | QQQ pre-market % change | `+0.6` |
| `{DXY}` | DXY index level | `104.2` |
| `{YIELD_10Y}` | 10-year Treasury yield | `4.31` |
| `{TICKER}` | Exact ticker symbol | `NVDA` |
| `{NAME}` | Company short name | `NVIDIA` |
| `{CONFIDENCE}` | HIGH or MEDIUM | `HIGH` |
| `{SCORE}` | Confluence score | `5` |
| `{CONFIRMED_CATEGORIES}` | Comma list of passed categories | `Trend, Momentum, Volume, Price Action, Macro` |
| `{ENTRY_LOW}` | Hourly support level | `$887.50` |
| `{ENTRY_HIGH}` | Pre-market price / last close | `$893.20` |
| `{STOP}` | Entry − 1.5 × ATR | `$871.00` |
| `{STOP_PCT}` | % distance from entry mid to stop | `2.4` |
| `{TARGET}` | Entry + 3.0 × ATR | `$926.00` |
| `{TARGET_PCT}` | % distance from entry mid to target | `4.8` |
| `{RR}` | Target / Stop distance ratio | `2.0` |
| `{PRIMARY_REASON}` | One-line human reason | `EMA50 bounce, MACD bullish cross, above-avg vol` |
| `{PATTERN_NOTE}` | Statistical pattern if present | `Bull flag — hit rate 3/3 last 12mo, avg +2.1% over 5d` |
| `{WATCHING_TICKER}` | Ticker at 3/6 | `META` |
| `{WATCHING_NAME}` | Name | `Meta` |
| `{MISSING_CATEGORY}` | Which category it failed | `Volume` |
| `{EXCLUDED_TICKER}` | Ticker in earnings blackout | `AAPL` |
| `{EARNINGS_DATE}` | Next earnings date | `2025-05-01` |
| `{MIN_CATEGORIES}` | From runtime.json | `4` |
| `{REASON}` | Kill-switch reason | `VIX at 32.1 — above 30 kill-switch` |
