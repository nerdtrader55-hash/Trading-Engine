# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.
Use Slack markdown only — no HTML tags. Render bold with *asterisks*, italics with _underscores_, code with `backticks`.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNAL REPORT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
SPY Futures: {SPY_DIRECTION} ({SPY_PCT}%)  |  QQQ Futures: {QQQ_DIRECTION} ({QQQ_PCT}%)
VIX: {VIX_LEVEL} ({VIX_CHANGE})  |  DXY: {DXY_LEVEL} ({DXY_CHANGE})  |  10Y: {YIELD_LEVEL} ({YIELD_CHANGE})
Macro verdict: {MACRO_VERDICT}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟢 SIGNAL{SIGNAL_COUNT_S}: {SIGNAL_COUNT} of {MAX_SIGNALS} MAX

{SIGNAL_BLOCK}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
👁 ALSO WATCHING (WAIT — close but not confirmed)
{WATCH_LIST}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚫 EARNINGS BLACKOUT
{BLACKOUT_LIST}

⚠️ _Not financial advice. All signals carry risk. Never risk more than 1-2% of account per trade._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Signal block format — repeat this block for each BUY signal:

```
*{TICKER}* : BUY  [{CONFIDENCE} CONFIDENCE — {SCORE}/6]
Entry zone:  ${ENTRY_LOW} – ${ENTRY_HIGH}  (hourly support band)
Stop:        ${STOP}  (entry − {ATR_STOP_MULT}× ATR14 = {ATR_VALUE})
Target:      ${TARGET}  (entry + {ATR_TARGET_MULT}× ATR14)
R:R ratio:   1:{RR_RATIO}
Confluence:  ✅ Trend  ✅ Momentum  ✅ Volume  ✅ Price Action  {MACRO_CHECK} Macro  {SENTIMENT_CHECK} Sentiment
Key reason:  {ONE_LINE_REASON}
```

### Placeholder reference for Template A:

| Placeholder | What to fill |
|---|---|
| `{DATE}` | e.g. Mon 02 Jun 2026 |
| `{TIME}` | e.g. 06:02 |
| `{SPY_DIRECTION}` | Flat / Up / Down |
| `{SPY_PCT}` | e.g. +0.4 |
| `{QQQ_DIRECTION}` | Flat / Up / Down |
| `{QQQ_PCT}` | e.g. +0.6 |
| `{VIX_LEVEL}` | numeric e.g. 18.3 |
| `{VIX_CHANGE}` | e.g. −0.8 / +1.2 |
| `{DXY_LEVEL}` | e.g. 104.2 |
| `{DXY_CHANGE}` | 5-day change |
| `{YIELD_LEVEL}` | e.g. 4.28% |
| `{YIELD_CHANGE}` | 5-day bps change e.g. −6bps |
| `{MACRO_VERDICT}` | SUPPORTIVE / NEUTRAL / HEADWIND |
| `{SIGNAL_COUNT}` | 1, 2, or 3 |
| `{SIGNAL_COUNT_S}` | "S" if count >1, else "" |
| `{MAX_SIGNALS}` | from runtime.json confluence.max_signals_per_run |
| `{SIGNAL_BLOCK}` | one signal sub-block per BUY (see above) |
| `{WATCH_LIST}` | comma-separated tickers that scored 3/6 — close but not enough |
| `{BLACKOUT_LIST}` | e.g. AAPL (earnings Jun 5), NVDA (earnings Jun 20) — or "None" |
| `{TICKER}` | e.g. NVDA |
| `{CONFIDENCE}` | HIGH or MEDIUM |
| `{SCORE}` | 4, 5, or 6 |
| `{ENTRY_LOW}` | lower bound of hourly support zone |
| `{ENTRY_HIGH}` | upper bound of hourly support zone |
| `{STOP}` | computed stop level |
| `{ATR_STOP_MULT}` | from runtime.json risk.atr_stop_multiplier |
| `{ATR_TARGET_MULT}` | from runtime.json risk.atr_target_multiplier |
| `{ATR_VALUE}` | current ATR14 daily value |
| `{TARGET}` | computed target level |
| `{RR_RATIO}` | e.g. 2.0 |
| `{MACRO_CHECK}` | ✅ or ❌ |
| `{SENTIMENT_CHECK}` | ✅ or ❌ |
| `{ONE_LINE_REASON}` | e.g. "Bull flag breakout above $127 on 1.8× avg volume, EMA stack intact" |

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Reason detail: {REASON_DETAIL}_

Next run: tomorrow pre-market (target {TARGET_DELIVERY_GMT} GMT).

⚠️ _Not financial advice. All signals carry risk._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Kill-switch reason values:
- `VIX > {VIX_MAX}` — market in extreme fear, standing aside
- `FOMC announcement day` — Fed decision expected, extreme intraday volatility likely
- `CPI release day` — inflation print due, unpredictable gaps
- `NFP / Non-Farm Payrolls day` — jobs report, extreme volatility likely

---

## Template C — No kill-switch, but all tickers score WAIT

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ NO SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Macro: {MACRO_VERDICT}
VIX: {VIX_LEVEL}  |  SPY: {SPY_DIRECTION}  |  QQQ: {QQQ_DIRECTION}

No ticker in the universe reached the 4/6 confluence threshold today.

Closest setups (3/6):
{WATCH_LIST}

Earnings blackout today:
{BLACKOUT_LIST}

⚠️ _Not financial advice. All signals carry risk._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Formatting rules

- Always use `━━━` horizontal rules as section dividers (copy-paste the exact character)
- Bold ticker names with `*TICKER*`
- Never include HTML tags
- Currency values always prefixed with `$`
- Percentages always include sign: `+0.4%` or `−0.8%` (use minus sign `−`, not hyphen `-`)
- Dates in format: `Mon 02 Jun 2026`
- Times always suffixed with `GMT`
- Max 3 signal blocks per message (runtime.confluence.max_signals_per_run)
- If 0 signals but no kill-switch, use Template C not Template B
