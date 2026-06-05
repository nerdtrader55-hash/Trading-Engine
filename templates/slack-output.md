# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
VIX: {VIX_LEVEL} | SPY Futures: {SPY_FUTURES} | QQQ Futures: {QQQ_FUTURES}
DXY: {DXY_LEVEL} | 10Y Yield: {YIELD_LEVEL}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY

Confidence: {SCORE}/6 — {HIGH/MEDIUM}
Entry zone: ${ENTRY_LOW}–${ENTRY_HIGH}
Stop: ${STOP} (−{STOP_PCT}%)
Target: ${TARGET} (+{TARGET_PCT}%) | R:R {RR}
ATR(14): ${ATR}

Confluence confirms ({SCORE}/6):
✅ {CATEGORY_1}
✅ {CATEGORY_2}
✅ {CATEGORY_3}
✅ {CATEGORY_4}
[✅/⬜] {CATEGORY_5}
[✅/⬜] {CATEGORY_6}

Pattern: {PATTERN_NAME} — {HIT_RATE}% hit rate (last 12 months)
Sector: {SECTOR} | Relative strength vs SPY (5d): {RS_5D}
Key news: {HEADLINE_1}

---

{REPEAT BLOCK ABOVE FOR EACH SIGNAL — MAX 3}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*ALSO WATCHING* (scored 3/6 — not yet confirmed)
• {TICKER_W1} — needs {MISSING_CATEGORY} to confirm
• {TICKER_W2} — earnings blackout expires {DATE}
• {TICKER_W3}

*Economic events today:* {EVENTS_OR_NONE}

⚠️ _Signals are informational only. Never risk more than 1–2% per trade. Past patterns do not guarantee future results._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

VIX: {VIX_LEVEL} | Threshold: 30
Next scheduled run: tomorrow pre-market

⚠️ _Signals are informational only. Never risk more than 1–2% per trade._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Template C — No signals (all tickers scored ≤3/6)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
VIX: {VIX_LEVEL} | SPY Futures: {SPY_FUTURES} | QQQ Futures: {QQQ_FUTURES}

No ticker reached the 4/6 confluence threshold today.

*Highest scoring setups (WAIT):*
• {TICKER_1} — {SCORE_1}/6 — blocked by: {REASON_1}
• {TICKER_2} — {SCORE_2}/6 — blocked by: {REASON_2}
• {TICKER_3} — {SCORE_3}/6 — blocked by: {REASON_3}

*Earnings blackouts active:*
• {TICKER} — earnings {DATE}

⚠️ _Signals are informational only. Never risk more than 1–2% per trade._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Formatting rules

- Use `*bold*` for ticker names and section headers (Slack markdown)
- Use `_italic_` for the disclaimer
- Use ✅ for confirmed categories, ⬜ for not confirmed
- Round all prices to 2 decimal places
- Express stop and target as both dollar value AND percentage from entry
- R:R expressed as `1:{RATIO}` (e.g. `1:2.1`)
- All timestamps in GMT
- Never add HTML tags — Slack does not render them
