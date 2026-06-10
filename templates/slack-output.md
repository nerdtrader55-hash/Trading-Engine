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
```

Rules:
- One line per signal. Exact format: `TICKER : BUY`
- Maximum 3 signals per run (highest confluence wins ties)
- HIGH confidence (5–6/6) listed before MEDIUM confidence (4/6)
- Add a footer line for each ticker excluded due to earnings blackout: `{TICKER} — EARNINGS BLACKOUT ({DATE})`
- Close with the standard risk disclaimer on its own line: `⚠️ Not financial advice. Always size within your own risk envelope.`

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: {REASON}

⚠️ Not financial advice. Always size within your own risk envelope.
```

Kill-switch reasons (fill in {REASON}):
- `VIX above {LEVEL} (kill threshold: {VIX_MAX})`
- `FOMC announcement day`
- `US CPI release day`
- `US Non-Farm Payrolls day`

---

## Template C — Outside signal window

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ OUTSIDE SIGNAL WINDOW — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Signal window: 02:30–08:30 GMT (pre-market only).
Current time is outside this window. No signals generated.
```

---

## Template D — All signals WAIT (no qualifying setups)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No qualifying signals today. All 12 tickers scored ≤3/6 confluence or failed the R:R gate.

⚠️ Not financial advice. Always size within your own risk envelope.
```
