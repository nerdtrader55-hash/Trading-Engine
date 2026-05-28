# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY

_(repeat one line per signal, up to 3 max)_

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

_⚠️ For informational purposes only. Not financial advice. All trading involves risk._

---

## Template B — Kill-switch fired (no signals today)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

---

## Template C — No signals (all tickers scored < 4/6 confluence)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸️ WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No setups met the minimum 4/6 confluence threshold today.

_⚠️ For informational purposes only. Not financial advice. All trading involves risk._

---

## Placeholder reference

| Placeholder | Value |
|---|---|
| `{DATE}` | Today's date in DD-Mon-YYYY format (e.g. 28-May-2026) |
| `{TIME}` | Current time in HH:MM format (runtime.json `timezone`) |
| `{TICKER}` | Exact ticker symbol from config/stocks.json (e.g. NVDA) |
| `{REASON}` | Short description of which kill-switch fired (e.g. "VIX = 32.4 (above 30 threshold)") |
