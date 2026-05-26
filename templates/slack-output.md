# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO* | VIX: {VIX} | SPY: {SPY_PCT}% | QQQ: {QQQ_PCT}% | DXY: {DXY} | 10Y: {YIELD}%

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*{TICKER_1}* : BUY — {CONFIDENCE_1} | Score: {SCORE_1}/6
Entry zone: ${ENTRY_LOW_1}–${ENTRY_HIGH_1} | Stop: ${STOP_1} | Target: ${TARGET_1} | R:R 1:{RR_1}
Confluence: Trend ✅ | Momentum ✅ | Volume {VOL_1} | Price Action ✅ | Macro {MACRO_1} | Sentiment {SENT_1}
_{PATTERN_NOTE_1}_
_{TOP_HEADLINE_1}_

*{TICKER_2}* : BUY — {CONFIDENCE_2} | Score: {SCORE_2}/6
Entry zone: ${ENTRY_LOW_2}–${ENTRY_HIGH_2} | Stop: ${STOP_2} | Target: ${TARGET_2} | R:R 1:{RR_2}
Confluence: Trend ✅ | Momentum ✅ | Volume {VOL_2} | Price Action ✅ | Macro {MACRO_2} | Sentiment {SENT_2}
_{PATTERN_NOTE_2}_
_{TOP_HEADLINE_2}_

*{TICKER_3}* : BUY — {CONFIDENCE_3} | Score: {SCORE_3}/6
Entry zone: ${ENTRY_LOW_3}–${ENTRY_HIGH_3} | Stop: ${STOP_3} | Target: ${TARGET_3} | R:R 1:{RR_3}
Confluence: Trend ✅ | Momentum ✅ | Volume {VOL_3} | Price Action ✅ | Macro {MACRO_3} | Sentiment {SENT_3}
_{PATTERN_NOTE_3}_
_{TOP_HEADLINE_3}_

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*ALSO WATCHING* (scored but below threshold)
{WATCHING_LIST}

_Earnings blackout: {EARNINGS_EXCLUSIONS}_
_Data errors: {FAILURES}_
_⚠️ Research use only. Not financial advice. Size all positions within your own risk envelope._
```

### Confluence icon key
- ✅ = confirms BUY
- ❌ = does not confirm
- ⚠️ = caution / partial

### CONFIDENCE labels
- HIGH = 5–6/6 categories confirmed
- MEDIUM = 4/6 confirmed (note smaller position sizing)

### WATCHING_LIST format (one ticker per line)
```
• {TICKER} — {SCORE}/6 ({MISSING_CATEGORIES} not confirming)
```

### Notes for Claude when filling this template
- Omit ticker blocks that have no signal (e.g. if only 2 BUYs, remove the third block)
- If WATCHING_LIST is empty, write "None today — all below threshold or in earnings blackout"
- If EARNINGS_EXCLUSIONS is empty, write "None"
- If FAILURES is empty, write "None"
- Use actual price values rounded to 2 decimal places
- RR_n = target ATR multiple ÷ stop ATR multiple (default 3.0 ÷ 1.5 = 2.0, write as "2.0")
- Confluence line: replace {VOL_n}, {MACRO_n}, {SENT_n} with ✅ or ❌ based on actual scoring

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Reason detail: {DETAIL}_
_Next scheduled run: tomorrow pre-market_

_⚠️ Research use only. Not financial advice._
```

### REASON values
- `VIX above threshold` — include current VIX level in DETAIL
- `FOMC announcement day` — include meeting time in DETAIL
- `CPI release day` — include release time in DETAIL
- `NFP release day` — include release time in DETAIL

---

## Template C — No signals (all tickers scored WAIT, no kill-switch)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO* | VIX: {VIX} | SPY: {SPY_PCT}% | QQQ: {QQQ_PCT}%

No ticker reached the ≥{MIN_SCORE}/6 confluence threshold today.

*Closest setups (monitoring):*
{WATCHING_LIST}

_Earnings blackout: {EARNINGS_EXCLUSIONS}_
_⚠️ Research use only. Not financial advice._
```
