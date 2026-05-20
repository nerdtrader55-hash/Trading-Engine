# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

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

Rules for filling the template:
- List only tickers with a confirmed BUY signal (4–6 confluence). Maximum 3 lines.
- If only 1 or 2 tickers qualify, list only those — do not pad with WAIT tickers.
- Ticker must be the exact symbol from `config/stocks.json` (e.g. `NVDA`, not `Nvidia`).
- Signal field is always `BUY` (this engine does not issue SHORT signals).
- No extra commentary, scores, or levels inside the signal block itself — keep it clean.

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: {REASON}
```

Reason strings (pick the most specific one that applies):
- `VIX > 30 — standing aside`
- `FOMC announcement day`
- `US CPI release day`
- `US Non-Farm Payrolls day`

---

## Template C — No signals (all tickers scored < 4/6 or blocked)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No setups met the 4/6 confluence threshold today.
```

---

## Footer (append to Template A or C when applicable)

```
Earnings blackout (next date): {TICKER} {DATE}, {TICKER} {DATE}
Data errors — skipped: {TICKER}, {TICKER}
```

Only include lines that have content. Omit blank footer lines entirely.

---

## Formatting rules

- Use plain text only — no HTML tags, no markdown headers inside the Slack message.
- The `━` separator line is exactly 40 characters wide.
- All times are in the timezone set in `config/runtime.json → timezone` (default: Europe/London / GMT).
- Never post raw JSON, indicator values, or debug output to Slack.
- One message per run. Do not send multiple messages.
