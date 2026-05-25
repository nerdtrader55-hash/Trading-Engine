# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome and fill every `{PLACEHOLDER}` with live data. Do not send partial messages or multiple messages in one run.

---

## Template A — Normal run with BUY signals

Use this when at least one ticker reaches the confluence threshold.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
SPY: {SPY_DIRECTION}  |  QQQ: {QQQ_DIRECTION}  |  VIX: {VIX_LEVEL} ({VIX_CHANGE})  |  DXY: {DXY_LEVEL}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟢 TODAY'S SIGNALS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*{TICKER}* — BUY  ·  {CONFIDENCE} CONFIDENCE  [{SCORE}/6]
Entry zone: ${ENTRY_LOW} – ${ENTRY_HIGH}
Stop: ${STOP}  |  Target: ${TARGET}  |  R:R 1:{RR}
✅ {CONFIRMING_CATEGORIES}
Pattern: {PATTERN_TAG}
_{TOP_REASON}_

[Repeat the signal block above for each BUY, up to the max_signals_per_run limit in config/runtime.json]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
👀 ALSO WATCHING (close but not triggered)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
• {TICKER}: {SCORE}/6 — waiting on {MISSING_CATEGORIES}

[List up to 3 near-miss tickers that scored exactly confluence_threshold − 1. Omit section entirely if none.]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
_Max 1–2% account risk per trade. Signal engine only — not financial advice._
_Earnings blackout (next date): {EARNINGS_EXCLUSIONS}_
_Data unavailable (skipped): {API_FAILURES}_
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Placeholder reference for Template A

| Placeholder | What to fill in |
|---|---|
| `{DATE}` | e.g. `Mon 26 May 2025` |
| `{TIME}` | e.g. `06:02` |
| `{SPY_DIRECTION}` | `▲ +0.4%` or `▼ −0.3%` futures |
| `{QQQ_DIRECTION}` | same format |
| `{VIX_LEVEL}` | numeric, e.g. `18.4` |
| `{VIX_CHANGE}` | `falling` / `rising` / `flat` |
| `{DXY_LEVEL}` | numeric + 5-day direction, e.g. `104.1 ▼` |
| `{TICKER}` | e.g. `NVDA` |
| `{CONFIDENCE}` | `HIGH` (5–6/6) or `MEDIUM` (4/6) |
| `{SCORE}` | numeric, e.g. `5` |
| `{ENTRY_LOW}` | lower bound of hourly entry zone |
| `{ENTRY_HIGH}` | upper bound (pre-market price or last close) |
| `{STOP}` | entry_low − (atr_stop_multiplier × ATR14) |
| `{TARGET}` | entry_high + (atr_target_multiplier × ATR14) |
| `{RR}` | computed R:R ratio, e.g. `2.3` |
| `{CONFIRMING_CATEGORIES}` | comma-separated confirmed cats, e.g. `Trend · Momentum · Volume · Macro` |
| `{PATTERN_TAG}` | e.g. `Bull flag`, `EMA50 touch`, `None` |
| `{TOP_REASON}` | one-sentence plain-English rationale |
| `{EARNINGS_EXCLUSIONS}` | e.g. `AAPL (Jun 5), NVDA (Aug 20)` or `None` |
| `{API_FAILURES}` | e.g. `ARM (rate-limited)` or `None` |

---

## Template B — Kill-switch fired (no signals today)

Use this when a macro kill-switch triggers in SKILL.md step 1. Stop immediately after posting — do not analyse individual tickers.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*
```

`{REASON}` examples:
- `VIX at 32.1 — above kill-switch threshold of 30`
- `FOMC rate decision today`
- `US CPI release today`
- `US Non-Farm Payrolls release today`

---

## Template C — No signals (confluence threshold not met)

Use this when all tickers were analysed but none reached the minimum confluence score.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏳ NO SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No ticker reached {MIN_SCORE}/6 confluence today.

Closest setups:
• {TICKER}: {SCORE}/6 — needs {MISSING_CATEGORIES}
• {TICKER}: {SCORE}/6 — needs {MISSING_CATEGORIES}

_Check back tomorrow._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
_Earnings blackout (next date): {EARNINGS_EXCLUSIONS}_
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

`{MIN_SCORE}` = value of `confluence.min_categories_for_buy` from `config/runtime.json`.

---

## Formatting rules (all templates)

- Use `*bold*` for ticker symbols and kill-switch reasons — Slack renders these correctly.
- Use `_italics_` for the rationale line and disclaimer.
- Do NOT use `**double-asterisk**` or `__double-underscore__` — Slack does not render these.
- Do NOT use HTML tags.
- Keep the `━` divider lines exactly as shown — they create visual structure in Slack.
- All prices in USD with `$` prefix. All percentages with `%` suffix.
- Timestamps always in GMT.
