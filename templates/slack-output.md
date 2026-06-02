# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
• S&P / Nasdaq futures: {SPY_FUTURES_DIR} / {QQQ_FUTURES_DIR}
• VIX: {VIX_LEVEL} ({VIX_CHANGE})
• DXY: {DXY_LEVEL} ({DXY_5D_CHANGE})
• 10Y yield: {YIELD_10Y} ({YIELD_5D_CHANGE})

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
BUY SIGNALS ({SIGNAL_COUNT}/3 max)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*{TICKER}* — BUY  [{CONFIDENCE} confidence · {SCORE}/6]
Entry zone: {ENTRY_LOW} – {ENTRY_HIGH}
Stop: {STOP_PRICE}  |  Target: {TARGET_PRICE}  |  R:R {RR_RATIO}
Confluence: {CONFLUENCE_LIST}
Pattern: {PATTERN_NOTE}
> {TOP_HEADLINE}

[Repeat block for each BUY signal, up to 3]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ALSO WATCHING (scored 3/6 — not triggered)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
{WATCH_TICKER}: {WATCH_REASON}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
_Excluded for earnings (next date): {EARNINGS_EXCLUSIONS}_
_Data failures (skipped): {DATA_FAILURES}_
_⚠️ Not financial advice. Size positions within your own risk envelope._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Resume normal signals next trading day unless condition persists._
_⚠️ Not financial advice._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Template C — No signals (all tickers scored ≤3/6)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No ticker reached the 4/6 confluence threshold today.

*Best setups approaching (not triggered):*
{WATCH_LIST}

_⚠️ Not financial advice._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
