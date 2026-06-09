# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY   [{CONFIDENCE} — {SCORE}/6]
{TICKER} : BUY   [{CONFIDENCE} — {SCORE}/6]
{TICKER} : BUY   [{CONFIDENCE} — {SCORE}/6]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Also watching (close but not signalling): {TICKER_LIST}
Skipped (earnings blackout): {BLACKOUT_LIST}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Not financial advice. Size inside your own risk envelope.
```

**Field definitions:**
- `{DATE}` — today's date in DD-MMM-YYYY format (e.g. 09-Jun-2026)
- `{TIME}` — signal generation time in HH:MM GMT
- `{TICKER}` — exact ticker symbol from config/stocks.json
- `{CONFIDENCE}` — `HIGH` (5–6/6) or `MEDIUM` (4/6)
- `{SCORE}` — number of confluence categories confirmed (4, 5, or 6)
- `{TICKER_LIST}` — comma-separated tickers that scored 3/6 (watching only)
- `{BLACKOUT_LIST}` — comma-separated tickers skipped due to earnings proximity; write `none` if none

If there are no "also watching" tickers, write `none` for that line.
If there are no blackout tickers, omit the blackout line entirely.

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

Reason codes:
  VIX > {vix_max}     — market too volatile, stand aside
  FOMC day            — Fed announcement, skip all signals
  CPI release         — inflation print day, extreme volatility
  NFP release         — jobs day, extreme volatility
```

---

## Template C — Out-of-window run

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏰ OUT OF WINDOW — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Signal window is 02:30–08:30 GMT. No signals generated outside this window.
```

---

## Template D — All tickers failed data pull

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ DATA ERROR — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Could not fetch data for any ticker. No signals issued.
Failed tickers: {FAILED_LIST}
Check Alpha Vantage API key and rate limits.
```

---

## Formatting rules

- Use these exact separator lines: `━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━`
- Use `*bold*` for emphasis in Slack markdown
- Maximum 3 BUY signals per run (highest confluence wins ties)
- Always include the risk disclaimer on Template A
- Never modify these templates during a run
