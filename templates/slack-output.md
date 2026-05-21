# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
{TICKER} : BUY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Repeat the block above once per BUY signal (max 3). Example with two signals:

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
NVDA : BUY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
META : BUY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

---

## Template B — Kill-switch fired (no signals today)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

---

## Template C — No qualifying signals (analysis ran, nothing passed threshold)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No ticker reached ≥{CONFLUENCE_THRESHOLD}/6 confluence today. Stand aside.

---

## Formatting rules

- Use plain text only. No HTML.
- The `━` divider line must appear above and below every signal block.
- Signal type is always uppercase: BUY
- DATE format: DD-MMM-YYYY (e.g. 21-May-2026)
- TIME format: HH:MM (e.g. 06:00)
- Never include stop-loss or target prices in the Slack message.
- Never include explanatory prose — just the signal blocks and the template footer.
