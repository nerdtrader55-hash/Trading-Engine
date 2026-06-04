# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.
Use Slack mrkdwn syntax. Do not use HTML tags. Replace every `{PLACEHOLDER}` with the real value before posting.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*{TICKER_1}* : BUY  |  {CONFIDENCE_1} confidence  |  Score {SCORE_1}/6
Entry zone: ${ENTRY_LOW_1}–${ENTRY_HIGH_1}  ·  Stop: ${STOP_1}  ·  Target: ${TARGET_1}  ·  R:R 1:{RR_1}
Confirms: {CONFIRMS_1}
Pattern: {PATTERN_1}

*{TICKER_2}* : BUY  |  {CONFIDENCE_2} confidence  |  Score {SCORE_2}/6
Entry zone: ${ENTRY_LOW_2}–${ENTRY_HIGH_2}  ·  Stop: ${STOP_2}  ·  Target: ${TARGET_2}  ·  R:R 1:{RR_2}
Confirms: {CONFIRMS_2}
Pattern: {PATTERN_2}

*{TICKER_3}* : BUY  |  {CONFIDENCE_3} confidence  |  Score {SCORE_3}/6
Entry zone: ${ENTRY_LOW_3}–${ENTRY_HIGH_3}  ·  Stop: ${STOP_3}  ·  Target: ${TARGET_3}  ·  R:R 1:{RR_3}
Confirms: {CONFIRMS_3}
Pattern: {PATTERN_3}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*Also watching (3–4/6, below signal threshold):* {NEAR_MISS_LIST}
*Excluded — earnings blackout:* {EARNINGS_EXCLUSION_LIST}
*Data failures (skipped):* {FAILURES_LIST}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
_Not financial advice. Risk ≤1–2% of account per trade. Max 3 open positions. This engine never executes trades._
```

### Field reference

| Placeholder | What to put |
|---|---|
| `{DATE}` | Today's date in DD-MMM-YYYY, e.g. `04-Jun-2026` |
| `{TIME}` | Signal generation time in HH:MM GMT |
| `{TICKER_n}` | Ticker symbol, e.g. `NVDA` |
| `{CONFIDENCE_n}` | `HIGH` (5–6/6) or `MEDIUM` (4/6) |
| `{SCORE_n}` | Integer 4–6 — number of confluence categories confirmed |
| `{ENTRY_LOW_n}` | Lower bound of hourly entry zone (from module 09) |
| `{ENTRY_HIGH_n}` | Upper bound of hourly entry zone |
| `{STOP_n}` | Entry − (1.5 × ATR14), rounded to 2 d.p. |
| `{TARGET_n}` | Entry + (3.0 × ATR14), rounded to 2 d.p. |
| `{RR_n}` | Actual R:R ratio, e.g. `2.1` (must be ≥ 2.0) |
| `{CONFIRMS_n}` | Comma-separated confirmed categories, e.g. `Trend, Momentum, Volume, Macro` |
| `{PATTERN_n}` | Short pattern note, e.g. `Bull flag retest of EMA20` or `Breakout above 52-wk high` |
| `{NEAR_MISS_LIST}` | Tickers that scored exactly 3/6 — worth monitoring; use `None` if empty |
| `{EARNINGS_EXCLUSION_LIST}` | `TICKER (next earnings: YYYY-MM-DD)` for each excluded ticker; use `None` if empty |
| `{FAILURES_LIST}` | Tickers where Alpha Vantage returned an error or null; use `None` if empty |

> **Omit signal blocks for unused slots.** If only 1 or 2 BUY signals fire, remove the unused `{TICKER_2}` / `{TICKER_3}` blocks entirely rather than leaving empty placeholders.

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Reason codes: VIX>{vix_max} | FOMC day | CPI release | NFP release_
_Resume normal analysis next trading day unless the condition persists._
```

### Kill-switch reason codes

| Code | Triggered when |
|---|---|
| `VIX > {vix_max}` | VIX quote exceeds `macro_kill_switches.vix_max` (default 30) |
| `FOMC day` | Today is a Federal Reserve rate-decision announcement day |
| `CPI release` | Today is a US Consumer Price Index release day |
| `NFP release` | Today is a US Non-Farm Payrolls release day |

---

## Template C — No signals (all tickers scored < 4/6)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No tickers reached the 4/6 confluence threshold today.

*Highest scorers:* {TOP_SCORERS_LIST}
*Excluded — earnings blackout:* {EARNINGS_EXCLUSION_LIST}
*Data failures (skipped):* {FAILURES_LIST}

_Resume analysis tomorrow. No action required._
```

### Field reference (Template C additions)

| Placeholder | What to put |
|---|---|
| `{TOP_SCORERS_LIST}` | Up to 3 tickers with their scores, e.g. `NVDA 3/6, AAPL 3/6, META 2/6` |

---

## Formatting rules

1. **One message per run** — do not send multiple Slack messages in a single run.
2. **No HTML** — Slack uses mrkdwn, not HTML. Use `*bold*`, `_italic_`, backticks for code.
3. **Dollar signs** — use plain `$` for prices; do not escape.
4. **Decimal places** — prices to 2 d.p.; R:R to 1 d.p.; ATR to 2 d.p.
5. **Timezone** — all times in Europe/London (GMT/BST depending on DST); label every time with `GMT` or `BST`.
6. **Never fabricate** — if a data field could not be fetched, write `N/A` for that field rather than guessing.
7. **Never post outside the template** — do not add commentary, disclaimers, or analysis prose beyond what the template specifies.
