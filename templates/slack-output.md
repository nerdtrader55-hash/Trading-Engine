# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY
{TICKER} : BUY

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Macro: SPY {SPY_DIR} | QQQ {QQQ_DIR} | VIX {VIX}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Also watching (near signal, not yet confirmed):
• {TICKER} — {SCORE}/6 categories | {REASON}

Earnings blackout (excluded today):
• {TICKER} — earnings {N} days away

⚠️ Research only. Not financial advice. Size within your own risk rules.
```

### Field rules

- `{DATE}` — format `DD Mon YYYY` e.g. `11 Jun 2026`
- `{TIME}` — 24h clock e.g. `06:04`
- `{TICKER} : BUY` — one line per signal, exactly this format (space-colon-space), uppercase ticker
- Confidence tag on signal line — append `[HIGH]` or `[MED]` after BUY when relevant:
  - 5–6/6 categories → `NVDA : BUY [HIGH]`
  - 4/6 categories → `META : BUY [MED]`
- "Also watching" — any ticker that scored 3/6 and is close to confirming; note which categories are missing
- "Earnings blackout" — list all tickers excluded this run and how many days to their earnings
- If no tickers are in either list, omit that section entirely
- Never list more than 3 signals (cap at `runtime.risk.max_concurrent_positions`)

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

Next scheduled run: tomorrow at 06:00 GMT (market days only).

⚠️ Research only. Not financial advice.
```

### Kill-switch reasons (use exactly these strings)

| Trigger | `{REASON}` value |
|---|---|
| VIX > 30 | `VIX at {LEVEL} — above 30 threshold. Stand aside.` |
| FOMC day | `FOMC announcement day. No signals on Fed days.` |
| CPI release | `CPI data release today. Extreme volatility expected.` |
| NFP release | `Non-Farm Payrolls day. Extreme volatility expected.` |
| No qualifying setups | `No tickers cleared 4/6 confluence + 1:2 R:R today.` |

---

## Template C — Data failure (partial run)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ PARTIAL RUN — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Tickers with data errors (skipped): {TICKER_LIST}
Reason: {ERROR_REASON}

Signals from remaining universe (if any):
{SIGNAL_BLOCK or "No qualifying setups from remaining tickers."}

⚠️ Research only. Not financial advice.
```

Use Template C only when ≥1 ticker was skipped due to Alpha Vantage errors but ≥1 ticker was analysed successfully.
If ALL tickers failed, use Template B with reason `Alpha Vantage data unavailable — all tickers skipped.`
