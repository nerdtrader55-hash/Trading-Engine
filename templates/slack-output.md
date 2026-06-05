# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : {SIGNAL}

{TICKER} : {SIGNAL}

{TICKER} : {SIGNAL}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 ALSO WATCHING (insufficient confluence): {WATCHING_LIST}

⚠️ Signals are for research only. Not financial advice. Always manage risk.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Rendering notes:**
- Replace each `{TICKER} : {SIGNAL}` line with actual ticker and signal type (e.g. `NVDA : BUY`)
- Omit signal lines if fewer than 3 signals fire — never pad with empty lines
- `{WATCHING_LIST}` = comma-separated tickers that scored 3/6 (close but not confirmed)
- If no signals fire at all, use Template C instead

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*
```

Reasons: `VIX > 30`, `FOMC day`, `CPI release day`, `NFP release day`, `Outside signal window`

---

## Template C — Run completed, no signals (all WAIT)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SCAN — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No signals today — confluence insufficient across all 12 tickers.

📋 Closest setups: {WATCHING_LIST}

⚠️ Signals are for research only. Not financial advice. Always manage risk.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
