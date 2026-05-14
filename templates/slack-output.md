# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY
[repeat one line per signal, max 3]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

_Confidence: {HIGH|MEDIUM} ({N}/6 categories)_
_Risk note: signals are research output only — not financial advice. Always size inside your own risk envelope._

[Optional footer — only include lines that apply:]
_Also watching (near-miss, 3/6): {TICKER}, {TICKER}_
_Skipped (earnings blackout): {TICKER}_
_Skipped (data error): {TICKER}_


## Template B — Kill-switch fired (no signals today)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Risk note: signals are research output only — not financial advice._


## Template C — No qualifying signals (all tickers scored ≤3/6)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏳ WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No ticker reached the 4/6 confluence threshold today.

_Risk note: signals are research output only — not financial advice._