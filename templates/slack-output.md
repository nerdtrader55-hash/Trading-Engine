# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome. Use Slack mrkdwn formatting only — no HTML, no standard Markdown headers.

---

## Template A — Normal run with BUY signals

Use this when at least one ticker scores ≥ 4/6 confluence AND passes the risk gate.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*{TICKER}* — BUY  ·  *{CONFIDENCE}* confidence  [{CONFLUENCE_SCORE}/6]
> *Entry zone:* ${ENTRY_LOW} – ${ENTRY_HIGH}
> *Stop:* ${STOP}  ·  *Target:* ${TARGET}  ·  *R:R* 1:{RR_RATIO}
> *Confirmed:* {CONFIRMED_CATEGORIES}
> {PATTERN_NOTE}
> {SECTOR_ROTATION_NOTE}
> {ANALOG_NOTE}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{REPEAT THE SIGNAL BLOCK ABOVE FOR EACH BUY SIGNAL — MAX 3 TOTAL}

─────────────────────────────────────────
*Also watching* (scored 3/6 — no signal today):
{TICKER_LIST or "None"}

*Earnings blackout* (excluded from today's run):
{TICKER: next earnings DATE — e.g. "AAPL: 2026-07-31"  or "None"}

*Data failures* (skipped — API error or missing data):
{TICKER: REASON  or "None"}
─────────────────────────────────────────
⚠️ _Research signals only. Not financial advice. Past signals do not guarantee future results. Always apply your own judgement and position-sizing rules before trading._
```

### Field reference

| Placeholder | What to put here |
|---|---|
| `{DATE}` | Today's date in YYYY-MM-DD format, Europe/London timezone |
| `{TIME}` | Current time HH:MM in GMT |
| `{TICKER}` | Exact ticker symbol from `config/stocks.json` |
| `{CONFIDENCE}` | `HIGH` (5–6/6) or `MEDIUM` (4/6) |
| `{CONFLUENCE_SCORE}` | Number of confirmed categories out of 6 (e.g. `5`) |
| `{ENTRY_LOW}` | Lower bound of hourly entry zone from module 09 |
| `{ENTRY_HIGH}` | Current pre-market price or daily close |
| `{STOP}` | entry − (1.5 × ATR14), rounded to 2 decimal places |
| `{TARGET}` | entry + (3.0 × ATR14), rounded to 2 decimal places |
| `{RR_RATIO}` | Actual ratio (e.g. `2.0`, `2.4`) — always ≥ 2.0 |
| `{CONFIRMED_CATEGORIES}` | Comma-separated list: e.g. `Trend ✅ Momentum ✅ Volume ✅ Price Action ✅ Macro ✅` |
| `{PATTERN_NOTE}` | From module 10 — e.g. `Bull flag off EMA50, 3rd touch` or omit line if none |
| `{SECTOR_ROTATION_NOTE}` | From module 10 — e.g. `Semiconductors top sector 5d RS vs. SPY` or omit if not relevant |
| `{ANALOG_NOTE}` | From module 10 — e.g. `Pattern hit rate last 12mo: 3/3, avg +2.1% over 5d` or omit if <3 prior instances |

---

## Template B — Kill-switch fired (no signals today)

Use this when any macro kill-switch triggers (VIX > 30, FOMC day, CPI day, NFP day). Post this message and stop immediately — do not analyse tickers.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Next signal run: tomorrow pre-market._

⚠️ _Research signals only. Not financial advice._
```

| Placeholder | Example values |
|---|---|
| `{REASON}` | `VIX at 34.2 (threshold: 30)` / `FOMC announcement day` / `US CPI release day` / `US Non-Farm Payrolls release day` |

---

## Template C — No signals (scan ran clean, nothing qualified)

Use this when macro kill-switches did NOT fire but no ticker reached ≥ 4/6 confluence (or all qualifying tickers failed the R:R gate). Never go silent — always post a message.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SCAN — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*No signals today.* Confluence threshold (4/6) not met across the 12-ticker universe.

*Closest setups* (scored 3/6 — worth monitoring):
{TICKER_LIST with brief reason  or "None within 3/6"}

*Earnings blackout* (excluded):
{TICKER: next earnings DATE  or "None"}

*Data failures* (skipped):
{TICKER: REASON  or "None"}
─────────────────────────────────────────
⚠️ _Research signals only. Not financial advice._
```

---

## Formatting rules (applies to all templates)

- Use `*text*` for bold — not `**text**`
- Use `_text_` for italic
- Use `` `text` `` for inline code / values
- No HTML tags anywhere
- Divider lines: `━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━` (heavy) or `─────────────────────────────────────────` (light)
- Max 3 BUY signal blocks per run — if more qualify, pick top 3 by confluence score, then pattern-tag count, then analog hit rate
- Omit `{PATTERN_NOTE}`, `{SECTOR_ROTATION_NOTE}`, and `{ANALOG_NOTE}` lines entirely when there is nothing meaningful to report — do not leave blank `>` lines
- Risk disclaimer is non-negotiable — it appears in every message, every run
