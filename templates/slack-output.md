# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO*  VIX {VIX} | SPY {SPY_DIRECTION} | QQQ {QQQ_DIRECTION} | DXY {DXY_NOTE}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
{TICKER} : BUY — {CONFIDENCE} confidence ({SCORE}/6)
  Entry zone:  {ENTRY_LOW} – {ENTRY_HIGH}
  Stop:        {STOP}  |  Target: {TARGET}  |  R:R {RR}
  ✅ Confirms: {CONFIRMED_CATEGORIES}
  📌 Key reason: {ONE_LINE_THESIS}
  📰 Headlines: {HEADLINE_1} / {HEADLINE_2}

[repeat block for each BUY signal, max 3]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*ALSO WATCHING* — scored 3/6, not actionable today
{WATCHER_LIST}

*EXCLUDED*
{EXCLUSION_LIST}

⚠️ Research signals only. Not financial advice. Manage your own risk.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Field reference

| Placeholder | What to fill in |
|---|---|
| `{DATE}` | Today's date, e.g. `Thu 21 May 2026` |
| `{TIME}` | Signal post time in GMT, e.g. `06:00` |
| `{VIX}` | VIX level to one decimal, e.g. `17.4` |
| `{SPY_DIRECTION}` | `▲ +0.3%` or `▼ -0.4%` pre-market |
| `{QQQ_DIRECTION}` | same format |
| `{DXY_NOTE}` | e.g. `flat` / `+0.4% (headwind for GOOG/META/AAPL)` |
| `{TICKER}` | Exact ticker symbol, e.g. `NVDA` |
| `{CONFIDENCE}` | `HIGH` (5–6/6) or `MEDIUM` (4/6) |
| `{SCORE}` | Number of confluence categories confirmed |
| `{ENTRY_LOW}` | Lower bound from hourly support (module 09) |
| `{ENTRY_HIGH}` | Pre-market price or last close |
| `{STOP}` | Entry − (1.5 × ATR14) |
| `{TARGET}` | Entry + (3.0 × ATR14) |
| `{RR}` | Actual R:R to two decimal places, e.g. `1:2.0` |
| `{CONFIRMED_CATEGORIES}` | Short names of confirmed categories, comma-separated |
| `{ONE_LINE_THESIS}` | e.g. `Bull flag breakout above $892 on 1.4× volume` |
| `{HEADLINE_1/2}` | Top 2 headlines from sentiment module (max 80 chars each) |
| `{WATCHER_LIST}` | Tickers that scored 3/6, one per line with reason |
| `{EXCLUSION_LIST}` | Tickers excluded for earnings or data failure, with next date |

### Example (filled)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — Thu 21 May 2026 06:00 GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO*  VIX 16.2 | SPY ▲ +0.4% | QQQ ▲ +0.6% | DXY flat

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
NVDA : BUY — HIGH confidence (5/6)
  Entry zone:  $1,038 – $1,052
  Stop:        $1,008  |  Target: $1,112  |  R:R 1:2.4
  ✅ Confirms: Trend, Momentum, Volume, Price Action, Macro
  📌 Key reason: EMA50 touch retest in uptrend, RSI 52, volume 1.3× avg
  📰 Headlines: "NVDA raises data-centre revenue outlook" / "Blackwell demand ahead of estimates"

META : BUY — MEDIUM confidence (4/6)
  Entry zone:  $582 – $591
  Stop:        $565  |  Target: $623  |  R:R 1:2.1
  ✅ Confirms: Trend, Momentum, Price Action, Sentiment
  📌 Key reason: Breakout above $588 resistance, MACD bullish cross
  📰 Headlines: "Meta AI assistant reaches 1B users" / "Q1 beat, raised FY guide"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*ALSO WATCHING* — scored 3/6, not actionable today
• AMZN — trend + momentum confirm but volume WEAK
• V — macro + sentiment confirm but price at resistance

*EXCLUDED*
• AAPL — earnings blackout (next: 01 May 2026)
• CRWD — earnings blackout (next: 09 Jun 2026)

⚠️ Research signals only. Not financial advice. Manage your own risk.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Next scheduled run: tomorrow pre-market._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

`{REASON}` examples:
- `VIX at 32.1 — above 30 threshold. Standing aside.`
- `FOMC rate decision today — no signals on announcement days.`
- `US CPI release today — extreme volatility risk. Standing aside.`
- `NFP release today — extreme volatility risk. Standing aside.`
