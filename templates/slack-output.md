# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
• SPY/QQQ futures: {FUTURES_DIRECTION}
• VIX: {VIX_LEVEL} ({VIX_TREND})
• DXY: {DXY_LEVEL} ({DXY_TREND})
• 10Y yield: {YIELD_LEVEL} ({YIELD_TREND})

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*SIGNALS*
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*{TICKER}* — BUY  |  Confidence: {HIGH/MEDIUM}  |  Score: {N}/6
• Trend: {TREND_LABEL} | Momentum: RSI {RSI_VALUE}, MACD {MACD_STATUS}
• Entry: ~${ENTRY_PRICE}  |  Stop: ${STOP_PRICE} (−{STOP_PCT}%)  |  Target: ${TARGET_PRICE} (+{TARGET_PCT}%)
• R:R = 1:{RR_RATIO}  |  ATR14: ${ATR_VALUE}
• Key confirms: {CONFIRM_LIST}
• Sentiment: {SENTIMENT_LABEL} — "{TOP_HEADLINE}"

{REPEAT BLOCK FOR EACH SIGNAL — MAX 3}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*ALSO WATCHING* (3–4 confirms, not yet signalling)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
{TICKER} — {SCORE}/6 — {ONE_LINE_REASON}
{TICKER} — {SCORE}/6 — {ONE_LINE_REASON}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
_⚠️ Signals are informational only. Never risk more than 1–2% per trade. Past patterns do not guarantee future results._
{FOOTER_EXCLUSIONS}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Variable reference:**
- `{DATE}` — e.g. `Thu 05 Jun 2026`
- `{TIME}` — e.g. `06:00`
- `{FUTURES_DIRECTION}` — e.g. `Both green ▲` / `Mixed` / `Both red ▼`
- `{VIX_TREND}` — `falling` / `rising` / `flat`
- `{DXY_TREND}` — `+0.3% 5d` / `−0.5% 5d`
- `{TREND_LABEL}` — `STRONG_BULL` / `BULL` / `NEUTRAL`
- `{MACD_STATUS}` — `bullish cross` / `above signal` / `below signal`
- `{CONFIRM_LIST}` — comma-separated list of confirmed categories, e.g. `Trend, Momentum, Volume, Price Action`
- `{SENTIMENT_LABEL}` — `STRONG_POSITIVE` / `POSITIVE` / `NEUTRAL`
- `{FOOTER_EXCLUSIONS}` — e.g. `Excluded for earnings (next: 2026-06-09): CRWD` — omit line if none

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*
```

**Reason examples:**
- `VIX = 32.4 (above 30 threshold)`
- `FOMC rate decision today — all signals paused`
- `US CPI release today — extreme volatility expected`
- `US Non-Farm Payrolls today — extreme volatility expected`

---

## Template C — All tickers in blackout or data failure (no signals, no kill-switch)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚪ NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No setup met the 4/6 confluence threshold today.

*Also watching (closest setups):*
{TICKER} — {SCORE}/6 — {ONE_LINE_REASON}
{TICKER} — {SCORE}/6 — {ONE_LINE_REASON}

{FOOTER_EXCLUSIONS}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
_⚠️ Signals are informational only. Never risk more than 1–2% per trade._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
