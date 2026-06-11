# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
{TICKER} : BUY
```

Repeat one line per signal (maximum 3). Example:

```
NVDA : BUY

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

Multiple signals example:

```
NVDA : BUY
META : BUY

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

If no ticker reaches the 4/6 confluence threshold, post Template C instead.

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*
```

Reasons: `VIX > 30`, `FOMC day`, `CPI release day`, `NFP release day`

---

## Template C — Run completed, no qualifying signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No ticker reached 4/6 confluence today. Standing aside.
```

---

## Formatting rules

- Use plain text only — no HTML tags
- Separator line: `━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━` (copy exactly)
- Ticker in ALL CAPS
- Signal in ALL CAPS (`BUY`)
- Format: `{TICKER} : {SIGNAL}` — colon with single space either side
- Maximum 3 BUY signals per message; highest confluence score wins ties
- Never add price targets, stop levels, or confidence labels to this message
