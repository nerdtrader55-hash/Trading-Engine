# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome. Never deviate from these formats.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY
{TICKER} : BUY

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Signal engine output only. Not financial advice. Always size within your own risk envelope.
```

Rules for Template A:
- One `{TICKER} : BUY` line per signal. Max 3 lines (cap at `runtime.max_concurrent_signals`).
- `{DATE}` = YYYY-MM-DD, `{TIME}` = HH:MM in the timezone from `runtime.timezone`.
- Do not add price targets, stop levels, or confidence scores to the Slack message — keep it clean.
- If you have both HIGH and MEDIUM confidence signals and must pick ≤3, rank by confluence score (higher first).

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: {REASON}
```

Use when any macro kill-switch fires (VIX > 30, FOMC day, CPI release, NFP release).

---

## Template C — All tickers scored WAIT (no kill-switch, just no qualifying setups)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No ticker reached {MIN_CONFLUENCE}/6 confluence today. Market is watchable — no high-probability setups at this time.
```

Use when no ticker scores ≥ `runtime.min_confluence` across the 6 categories, but no hard kill-switch was triggered.
