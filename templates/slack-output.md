# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
DAILY BUY SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY  [{CONFIDENCE} confidence — {SCORE}/6 categories]

...repeat for each signal up to max 3...

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Market: S&P futures {SPY_DIRECTION} | QQQ futures {QQQ_DIRECTION} | VIX {VIX_LEVEL}
{EXCLUSIONS_LINE}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Signals only — not financial advice. Size within your own risk envelope.
```

### Field notes
- `{CONFIDENCE}` = HIGH (5–6/6) or MEDIUM (4/6)
- `{SCORE}/6` = number of confluence categories confirmed
- `{EXCLUSIONS_LINE}` = `Excluded (earnings ±3d): AAPL, NVDA` — omit this line if no exclusions
- `{SPY_DIRECTION}` / `{QQQ_DIRECTION}` = green ▲ / red ▼ / flat –
- `{VIX_LEVEL}` = numeric value, e.g. `18.4`

---

## Template A (no signals) — All surviving tickers scored WAIT

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
DAILY SCAN — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No setups meet the ≥4/6 confluence threshold today. WAIT.

Market: S&P futures {SPY_DIRECTION} | QQQ futures {QQQ_DIRECTION} | VIX {VIX_LEVEL}
{EXCLUSIONS_LINE}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Signals only — not financial advice. Size within your own risk envelope.
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: {REASON}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Kill-switch reasons
- `VIX {VIX_LEVEL} > 30 — macro environment too volatile`
- `FOMC announcement day — no signals`
- `CPI release day — no signals`
- `Non-farm payrolls day — no signals`
