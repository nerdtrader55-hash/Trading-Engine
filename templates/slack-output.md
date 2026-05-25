# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.
Render as Slack mrkdwn. No HTML. Replace all `{PLACEHOLDERS}` before sending.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
• S&P 500 futures: {SPY_FUTURES_DIRECTION} ({SPY_FUTURES_CHANGE})
• Nasdaq futures: {QQQ_FUTURES_DIRECTION} ({QQQ_FUTURES_CHANGE})
• VIX: {VIX_LEVEL} — {VIX_STATUS}
• DXY: {DXY_LEVEL} ({DXY_5D_CHANGE} 5d)
• 10Y yield: {YIELD_LEVEL}% ({YIELD_5D_CHANGE}bps 5d)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟢 BUY SIGNALS ({SIGNAL_COUNT}/3 max)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{SIGNAL_BLOCKS}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
👁 ALSO WATCHING (scored 3/6 — not signalling)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
{WATCHING_LIST}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
{FOOTER}
⚠️ Not financial advice. Size within your risk rules. Max 1-2% per trade.
```

### Signal block format (repeat once per BUY signal)

```
*{TICKER}* — BUY  |  {CONFIDENCE} confidence  |  Score: {SCORE}/6

  Trend: {TREND_VERDICT}  |  Momentum: {MOMENTUM_VERDICT}  |  Volume: {VOLUME_VERDICT}
  Price action: {PRICE_ACTION_VERDICT}  |  Macro: {MACRO_VERDICT}  |  Sentiment: {SENTIMENT_VERDICT}

  Entry zone: {ENTRY_LOW} – {ENTRY_HIGH}
  Stop: {STOP_PRICE} (−{STOP_PCT}%)
  Target: {TARGET_PRICE} (+{TARGET_PCT}%)   R:R {RR_RATIO}

  Key level: {KEY_LEVEL_NOTE}
  {PATTERN_NOTE}
  {ANALOG_NOTE}
  Headlines: {TOP_HEADLINE}
```

### Watching list format (one line per ticker)

```
• {TICKER} — {SCORE}/6 — {ONE_LINE_REASON_IT_MISSED}
```

### Footer placeholders

- `{FOOTER}` — compose from these lines (include only applicable ones):
  - `📵 Earnings blackout: {BLACKOUT_TICKERS} (next: {EARNINGS_DATES})`
  - `⚡ Data errors — skipped: {FAILED_TICKERS}`
  - `📌 Caution flags: {CAUTION_NOTES}`

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

{KILL_SWITCH_DETAIL}

Next run: tomorrow pre-market (unless event extends).
⚠️ Not financial advice.
```

Kill-switch reason strings:
- VIX above threshold: `VIX at {VIX_LEVEL} — above {VIX_MAX} kill-switch level`
- FOMC day: `FOMC announcement day — no signals on Fed days`
- CPI day: `CPI release day — extreme volatility expected, standing aside`
- NFP day: `Non-Farm Payrolls day — extreme volatility expected, standing aside`

---

## Template C — No signals (all tickers WAIT, no kill-switch)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
• S&P 500 futures: {SPY_FUTURES_DIRECTION} ({SPY_FUTURES_CHANGE})
• Nasdaq futures: {QQQ_FUTURES_DIRECTION} ({QQQ_FUTURES_CHANGE})
• VIX: {VIX_LEVEL} — {VIX_STATUS}

No ticker scored ≥{MIN_SCORE}/6 today. Highest scorer: *{TOP_TICKER}* at {TOP_SCORE}/6.

{FOOTER}
⚠️ Not financial advice.
```
