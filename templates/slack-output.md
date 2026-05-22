# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

List each signal on its own line, then a separator. Replace `{TICKER}` with the ticker symbol and `{CONFIDENCE}` with HIGH or MEDIUM.

```
{TICKER} : BUY
```

Example — three signals in one message:

```
NVDA : BUY
META : BUY
AMZN : BUY

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

If there are no signals today (all tickers scored ≤3/6), post:

```
No signals today — all tickers below confluence threshold.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*
```

Valid `{REASON}` values:
- `VIX above 30 ({CURRENT_VIX})`
- `FOMC announcement day`
- `CPI release day`
- `NFP (non-farm payrolls) release day`
- `Outside signal window (02:30–08:30 GMT)`
