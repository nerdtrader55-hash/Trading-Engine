# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome and fill every `{PLACEHOLDER}` before posting. Never modify this file during a run.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY
{TICKER} : BUY
{TICKER} : BUY

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Repeat the `{TICKER} : BUY` line once per signal (1–3 lines maximum).** If there is only one signal, output one line. Never output more than three.

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: {REASON}
```

**Valid `{REASON}` values:** `VIX > 30`, `FOMC announcement day`, `CPI release day`, `NFP release day`, `outside signal window (02:30–08:30 GMT)`

---

## Template C — Run completed, no signals qualified

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No setups met the 4/6 confluence threshold today.
```

---

## Placeholder reference

| Placeholder | Example | Notes |
|---|---|---|
| `{DATE}` | `Thu 22 May 2025` | Format: `Ddd DD Mon YYYY` |
| `{TIME}` | `05:47` | 24h clock, Europe/London timezone |
| `{TICKER}` | `NVDA` | Exact ticker from config/stocks.json |
| `{REASON}` | `VIX > 30` | Kill-switch reason string |

---

## Rules

- Post **one message** per run. Never post multiple Slack messages in a single run.
- Never include prices, stop levels, R:R ratios, or commentary in the signal line itself — the format is `{TICKER} : BUY` and nothing else on that line.
- Never claim guaranteed profits. Never omit the separator lines.
- If any tickers were excluded for earnings, append this line to any template after the bottom separator:
  `Excluded (earnings blackout): {TICKER_LIST}`
- If any tickers failed data fetch, append:
  `Data unavailable (skipped): {TICKER_LIST}`
