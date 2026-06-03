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

Also watching (WAIT): {WAIT_LIST}
Excluded (earnings): {EXCLUSION_LIST}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Research use only. Not financial advice. Always size within your own risk envelope.
```

**Variable guide:**
- `{DATE}` — today's date, e.g. `03 Jun 2026`
- `{TIME}` — time of run, e.g. `06:00`
- `{TICKER}` — repeat one line per BUY signal, e.g. `NVDA : BUY`
- `{WAIT_LIST}` — comma-separated tickers that scored <4/6, e.g. `AAPL, ARM, V`
- `{EXCLUSION_LIST}` — tickers skipped due to earnings blackout, or `none`

If there are no exclusions, omit that line entirely.
If the WAIT list is empty (all 12 either signalled or excluded), omit that line.

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

Reason detail: {DETAIL}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Research use only. Not financial advice.
```

**Variable guide:**
- `{REASON}` — one of: `VIX > 30`, `FOMC day`, `CPI release day`, `NFP release day`
- `{DETAIL}` — e.g. `VIX at 34.2` or `FOMC rate decision at 14:00 ET`

---

## Template C — Run completed, no ticker reached the signal threshold

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ NO SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No ticker reached the 4/6 confluence threshold today.

Also watching (WAIT): {WAIT_LIST}
Excluded (earnings): {EXCLUSION_LIST}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Research use only. Not financial advice. Always size within your own risk envelope.
```

---

## Template selection rules

| Situation | Template |
|---|---|
| ≥1 BUY signal issued | A |
| Macro kill-switch fired (VIX/FOMC/CPI/NFP) | B |
| Run completed, all tickers scored <4/6 confluence | C |

Never send more than one Slack message per run.
