# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.
Render with Slack markdown. Never use HTML tags. Use `*bold*`, `_italic_`, ` ```code``` `.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📡 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
• Futures: {SPY_FUTURES_DIRECTION} SPY / {QQQ_FUTURES_DIRECTION} QQQ
• VIX: {VIX_LEVEL} ({VIX_REGIME})
• DXY: {DXY_LEVEL} ({DXY_5D_CHANGE}% 5d)
• 10Y Yield: {YIELD_10Y}% ({YIELD_5D_CHANGE}bps 5d)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{SIGNAL_BLOCKS}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*ALSO WATCHING* _(scored 3/6 — below threshold)_
{WATCHING_LIST}

*EXCLUDED — EARNINGS BLACKOUT*
{EARNINGS_EXCLUDED_LIST}

_⚠️ Research use only. Not financial advice. Always size within your own risk limits._
```

---

### Signal block — one per BUY ticker (repeat inside {SIGNAL_BLOCKS})

```
*{TICKER}* — {SIGNAL_TYPE}  |  Confidence: {CONFIDENCE_LABEL} ({SCORE}/6)

Entry zone: {ENTRY_LOW} – {ENTRY_HIGH}
Stop:       {STOP_PRICE}  ({STOP_PCT}% risk | {ATR_MULTIPLE}× ATR)
Target:     {TARGET_PRICE}  (R:R {RR_RATIO}:1)

Confluence:
  ✅ Trend      — {TREND_DETAIL}
  ✅ Momentum   — {MOMENTUM_DETAIL}
  ✅ Volume     — {VOLUME_DETAIL}
  ✅ Price Actn — {PRICE_ACTION_DETAIL}
  {MACRO_ICON} Macro      — {MACRO_DETAIL}
  {SENTIMENT_ICON} Sentiment  — {SENTIMENT_DETAIL}

{PATTERN_NOTE}
{SECTOR_ROTATION_NOTE}
{ANALOG_NOTE}
```

**Substitution guide:**

| Placeholder | What to put |
|---|---|
| `{SIGNAL_TYPE}` | `BUY` (only BUY signals are posted) |
| `{CONFIDENCE_LABEL}` | `HIGH` (5–6/6) or `MEDIUM` (4/6) |
| `{SCORE}` | Integer 4–6 |
| `{ENTRY_LOW}` – `{ENTRY_HIGH}` | Hourly support level to current pre-market price |
| `{STOP_PRICE}` | Entry midpoint − 1.5 × ATR14 |
| `{STOP_PCT}` | Stop distance as % of entry midpoint |
| `{ATR_MULTIPLE}` | Always `1.5` |
| `{TARGET_PRICE}` | Entry midpoint + 3.0 × ATR14 |
| `{RR_RATIO}` | Always ≥ 2.0 (format to 1 decimal) |
| `{TREND_DETAIL}` | e.g. "Price > EMA20 > EMA50 > EMA200, STRONG_BULL" |
| `{MOMENTUM_DETAIL}` | e.g. "RSI 58, MACD bullish cross 2 sessions ago" |
| `{VOLUME_DETAIL}` | e.g. "Vol 142% of 20d avg, accumulation on up-days" |
| `{PRICE_ACTION_DETAIL}` | e.g. "Hammer at EMA50, holding 50% fib retracement" |
| `{MACRO_DETAIL}` | e.g. "Both futures green, VIX 17.4 falling" |
| `{SENTIMENT_DETAIL}` | e.g. "AV score +0.28 POSITIVE, no red-flag keywords" |
| `{MACRO_ICON}` / `{SENTIMENT_ICON}` | `✅` if confirms, `⚠️` if neutral, `❌` if non-confirm |
| `{PATTERN_NOTE}` | e.g. "📐 Bull flag pattern — hit rate 3/4 last 12mo, avg +2.1% over 5d" — or omit if no pattern |
| `{SECTOR_ROTATION_NOTE}` | e.g. "🔄 Semiconductor sector showing emerging rotation (top-2 strength 5d vs. SPY)" — or omit |
| `{ANALOG_NOTE}` | e.g. "📊 2 correlated peers (NVDA, ARM) also scoring 4+/6 today — signal reinforced" — or omit |

If `{PATTERN_NOTE}`, `{SECTOR_ROTATION_NOTE}`, or `{ANALOG_NOTE}` have nothing to say, omit those lines entirely. No blank lines in their place.

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

{KILL_SWITCH_DETAIL}

_Next run: tomorrow pre-market. All 12 tickers will be re-evaluated._
```

**{REASON} options:**
- `VIX above 30 ({VIX_LEVEL})` — include current VIX value
- `FOMC announcement day` — include the Fed decision time in GMT
- `CPI release day` — include release time in GMT
- `NFP release day` — include release time in GMT

**{KILL_SWITCH_DETAIL}:** one sentence explaining the risk, e.g.:
- "VIX at 31.4 signals elevated systemic risk. Holding cash until volatility normalises."
- "Fed rate decision at 7:00 PM GMT. Binary event — no directional edge."

---

## Template C — All tickers WAIT (no kill-switch, just no qualifying signals)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📡 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
• Futures: {SPY_FUTURES_DIRECTION} SPY / {QQQ_FUTURES_DIRECTION} QQQ
• VIX: {VIX_LEVEL} ({VIX_REGIME})
• DXY: {DXY_LEVEL} ({DXY_5D_CHANGE}% 5d)
• 10Y Yield: {YIELD_10Y}% ({YIELD_5D_CHANGE}bps 5d)

*RESULT: WAIT* — No ticker reached 4/6 confluence threshold today.

Highest scorers:
{TOP_SCORERS_LIST}

*EXCLUDED — EARNINGS BLACKOUT*
{EARNINGS_EXCLUDED_LIST}

_⚠️ Research use only. Not financial advice. Always size within your own risk limits._
```

**{TOP_SCORERS_LIST}:** list up to 3 tickers with their score, e.g.:
```
  NVDA — 3/6 (missing: Volume, Sentiment)
  META — 3/6 (missing: Momentum, Price Action)
  AAPL — 2/6 (missing: Trend, Volume, Sentiment)
```

---

## Formatting rules

1. Use exactly the `━` separator lines — copy-paste them, do not substitute dashes.
2. All prices in USD to 2 decimal places.
3. All percentages to 1 decimal place.
4. GMT timestamp format: `26 May 2026 06:04 GMT`
5. Slack bold = `*text*`. Slack italic = `_text_`. Slack code = ` `text` `.
6. No nested bold inside italic. Slack doesn't render it reliably.
7. Maximum 3 signal blocks (Template A). If >3 qualify, post the 3 with highest confluence score (ties broken by pattern-tag count, then analog hit rate).
8. Never fabricate prices, levels, or indicator values. If data is unavailable for a field, replace with `N/A` and add a footer note.
