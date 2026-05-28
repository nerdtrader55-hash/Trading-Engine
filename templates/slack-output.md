# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🌍 MACRO SNAPSHOT
• SPY futures: {SPY_FUTURES_DIRECTION} ({SPY_FUTURES_PCT}%)
• QQQ futures: {QQQ_FUTURES_DIRECTION} ({QQQ_FUTURES_PCT}%)
• VIX: {VIX_LEVEL} ({VIX_TREND})
• DXY: {DXY_LEVEL} ({DXY_5D_CHANGE})
• 10Y yield: {YIELD_LEVEL} ({YIELD_5D_CHANGE})

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟢 BUY SIGNALS ({SIGNAL_COUNT}/3 max)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*{TICKER}* — BUY | {CONFIDENCE} confidence ({SCORE}/6)
• Entry zone: {ENTRY_LOW} – {ENTRY_HIGH}
• Stop: {STOP_PRICE} (−{STOP_PCT}% / 1.5×ATR)
• Target: {TARGET_PRICE} (+{TARGET_PCT}% / 3.0×ATR)
• R:R {RR_RATIO}:1
• Confluence: ✅ Trend · ✅ Momentum · ✅ Volume · ✅ Price Action · {MACRO_TICK} Macro · {SENTIMENT_TICK} Sentiment
• Pattern: {PATTERN_TAG}
• Key levels: S {SUPPORT} / R {RESISTANCE}
• Note: {PATTERN_ANALOG_NOTE}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
👀 ALSO WATCHING (scored 3/6 — below threshold)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
{WATCHING_LIST}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ℹ️ FOOTER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Excluded for earnings (next date): {EARNINGS_EXCLUSIONS}
Data failures (skipped): {DATA_FAILURES}
⚠️ Research only. Not financial advice. Size within your risk envelope.
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

🌍 MACRO SNAPSHOT
• VIX: {VIX_LEVEL}
• SPY futures: {SPY_FUTURES_DIRECTION}
• QQQ futures: {QQQ_FUTURES_DIRECTION}

⚠️ Research only. Not financial advice.
```

---

## Template C — No signals (all tickers scored below threshold)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No tickers reached 4/6 confluence today.

🌍 MACRO SNAPSHOT
• SPY futures: {SPY_FUTURES_DIRECTION} | QQQ: {QQQ_FUTURES_DIRECTION}
• VIX: {VIX_LEVEL} ({VIX_TREND})

👀 CLOSEST TO SIGNAL (highest scores):
{WATCHING_LIST}

Excluded for earnings: {EARNINGS_EXCLUSIONS}
⚠️ Research only. Not financial advice.
```
