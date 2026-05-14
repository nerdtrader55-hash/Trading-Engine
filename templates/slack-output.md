# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

Placeholders in `{CURLY_BRACES}` are filled by the routine at runtime. Remove any section whose data is empty (e.g. no exclusions, no errors).

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*Macro snapshot*
• VIX: {VIX_LEVEL} ({VIX_DIRECTION})
• Futures: SPY {SPY_FUTURES} / QQQ {QQQ_FUTURES}
• DXY: {DXY_LEVEL} ({DXY_5D_CHANGE})
• 10Y yield: {YIELD_LEVEL} ({YIELD_5D_CHANGE})

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
{TICKER} : BUY  ·  {CONFIDENCE} confidence  ({SCORE}/6)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Entry zone:  {ENTRY_LOW} – {ENTRY_HIGH}
Stop:        {STOP}  (−{STOP_PCT}% / 1.5× ATR)
Target:      {TARGET}  (+{TARGET_PCT}% / 3.0× ATR)
R:R          1 : {RR_RATIO}

Confluence:
  ✅ Trend      {TREND_DETAIL}
  ✅ Momentum   {MOMENTUM_DETAIL}
  ✅ Volume     {VOLUME_DETAIL}
  ✅ Price action  {PRICE_ACTION_DETAIL}
  ✅ Macro      {MACRO_DETAIL}
  ✅ Sentiment  {SENTIMENT_DETAIL}

Key levels:  S {SUPPORT_1} / {SUPPORT_2}  ·  R {RESISTANCE_1} / {RESISTANCE_2}
Pattern:     {PATTERN_NOTE}
Headlines:   {HEADLINE_1}
             {HEADLINE_2}

⚠️ Not financial advice. Size within your 1-2% account-risk rule.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Also watching (3/6 — below threshold today):
  • {WATCHING_TICKER_1} — {WATCHING_REASON_1}
  • {WATCHING_TICKER_2} — {WATCHING_REASON_2}

Excluded (earnings blackout): {EXCLUDED_TICKERS}
Data errors (skipped):        {ERROR_TICKERS}
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

Resume normal analysis tomorrow unless conditions persist.
```

---

## Template C — Engine ran, no tickers qualified

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*Macro snapshot*
• VIX: {VIX_LEVEL} ({VIX_DIRECTION})
• Futures: SPY {SPY_FUTURES} / QQQ {QQQ_FUTURES}

No ticker reached the 4/6 confluence threshold today.

Also watching (highest scores):
  • {WATCHING_TICKER_1} — {WATCHING_SCORE_1}/6 — {WATCHING_REASON_1}
  • {WATCHING_TICKER_2} — {WATCHING_SCORE_2}/6 — {WATCHING_REASON_2}
  • {WATCHING_TICKER_3} — {WATCHING_SCORE_3}/6 — {WATCHING_REASON_3}

Excluded (earnings blackout): {EXCLUDED_TICKERS}
Data errors (skipped):        {ERROR_TICKERS}
```

---

## Rendering notes

- Use `*text*` for bold in Slack mrkdwn (not `**text**`)
- Use `━` (U+2501) for divider lines — renders cleanly in Slack
- ✅ for confirmed categories, ❌ for non-confirmed — makes the confluence block scannable at a glance
- If a confluence category did NOT confirm, replace ✅ with ❌ and keep the detail so the trader can see why
- If there are multiple BUY signals (up to 3), repeat the signal block for each ticker; keep the macro snapshot header only once at the top
- Omit the "Excluded" line entirely if no tickers were excluded; same for "Data errors"
