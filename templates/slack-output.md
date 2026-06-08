# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.
Never modify this file during a run.

---

## Template A — Normal run (one block per BUY signal, max 3)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY  ←  {CONFIDENCE} ({SCORE}/6)
Entry zone  {ENTRY_LOW} – {ENTRY_HIGH}
Stop        {STOP}   |   Target  {TARGET}   |   R:R  1:{RR}
Confirms    {CONFLUENCE_LIST}

[Repeat the block above for each BUY signal. Separate blocks with a blank line.]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📡 MACRO SNAPSHOT
VIX {VIX}  |  SPY futs {SPY_FUTS}  |  QQQ futs {QQQ_FUTS}
DXY {DXY}  |  10Y yield {YIELD}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Also watching (3/6):  {WATCHING_LIST or "none"}
Earnings excluded:    {EARNINGS_EXCLUDED or "none"}
Data errors:          {FAILED_TICKERS or "none"}

⚠️  Not financial advice. Size within your risk envelope. Max 1–2% account per trade.
```

### Field reference

| Placeholder | What to fill in |
|---|---|
| `{DATE}` | Today's date, e.g. `Mon 09 Jun 2025` |
| `{TIME}` | Current GMT time, e.g. `05:47` |
| `{TICKER}` | Uppercase ticker, e.g. `NVDA` |
| `{CONFIDENCE}` | `HIGH CONFIDENCE` (5–6/6) or `MEDIUM CONFIDENCE` (4/6) |
| `{SCORE}` | Number of confirming confluence categories, e.g. `5` |
| `{ENTRY_LOW}` / `{ENTRY_HIGH}` | Hourly support/resistance zone around current price |
| `{STOP}` | entry − (1.5 × ATR14), rounded to 2 d.p. |
| `{TARGET}` | entry + (3.0 × ATR14), rounded to 2 d.p. |
| `{RR}` | Risk:reward ratio, e.g. `2.0` |
| `{CONFLUENCE_LIST}` | Comma-separated list of confirming categories, e.g. `Trend, Momentum, Volume, Price Action, Macro` |
| `{SPY_FUTS}` / `{QQQ_FUTS}` | Pre-market change, e.g. `+0.4%` or `-0.2%` |
| `{VIX}` | Current VIX level, e.g. `18.3` |
| `{DXY}` | DXY level + 5-day change, e.g. `104.2 (+0.3%)` |
| `{YIELD}` | 10Y yield + 5-day change, e.g. `4.31% (+4bps)` |
| `{WATCHING_LIST}` | Tickers that scored exactly 3/6 — worth monitoring |
| `{EARNINGS_EXCLUDED}` | Tickers skipped due to earnings blackout + next date |
| `{FAILED_TICKERS}` | Tickers skipped due to Alpha Vantage errors |

### No BUY signals variant (all tickers scored ≤3/6 but no kill-switch)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No signals today — confluence threshold not met for any ticker.

Also watching (3/6):  {WATCHING_LIST or "none"}
Earnings excluded:    {EARNINGS_EXCLUDED or "none"}

⚠️  Not financial advice. Size within your risk envelope. Max 1–2% account per trade.
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: {REASON}

Next scheduled run: tomorrow pre-market.
```

### Kill-switch reasons

| Code | Reason string to use |
|---|---|
| VIX > 30 | `VIX at {VIX} — above 30 kill-switch threshold. Standing aside.` |
| FOMC day | `FOMC announcement day. No signals on Fed decision days.` |
| CPI release | `US CPI release today. Extreme volatility expected — standing aside.` |
| NFP release | `US Non-Farm Payrolls day. Extreme volatility expected — standing aside.` |
| Outside window | `Current time {TIME} GMT is outside the 02:30–08:30 signal window.` |
