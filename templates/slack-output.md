# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*Macro:* SPY {SPY_DIRECTION} | QQQ {QQQ_DIRECTION} | VIX {VIX_LEVEL} | DXY {DXY_CHANGE_5D}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟢 BUY SIGNALS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*{TICKER}* — BUY | {HIGH/MEDIUM} confidence ({SCORE}/6)
Confluence: {CATEGORY_1} ✅ | {CATEGORY_2} ✅ | {CATEGORY_3} ✅ | {CATEGORY_4} ✅ | {FAILING_CATEGORY} ❌
Entry zone: {ENTRY_LOW}–{ENTRY_HIGH} | Stop: {STOP} | Target: {TARGET} | R:R {RR}
_{PATTERN_NOTE}_

[Repeat block above for each BUY signal, up to 3 total]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
👀 ALSO WATCHING (scored 3/6 — not enough to signal today)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} — {BRIEF_REASON}
[Repeat for each near-miss ticker, max 4]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
_Excluded (earnings blackout): {TICKER_LIST} — next dates: {DATES}_
_Skipped (data error): {TICKER_LIST}_
⚠️ _Signals are research output, not financial advice. Never risk more than 1–2% of account per trade._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Field guide for Template A:**

| Placeholder | What to put here |
|---|---|
| `{DATE}` | e.g. `Thu 22 May 2026` |
| `{TIME}` | e.g. `05:58` |
| `{SPY_DIRECTION}` | `▲ +0.4%` or `▼ −0.3%` (pre-market futures) |
| `{QQQ_DIRECTION}` | same format |
| `{VIX_LEVEL}` | e.g. `18.4` |
| `{DXY_CHANGE_5D}` | e.g. `DXY 104.2 (5d: −0.3%)` |
| `{TICKER}` | e.g. `NVDA` |
| `{HIGH/MEDIUM}` | `HIGH` (5–6/6) or `MEDIUM` (4/6) |
| `{SCORE}` | integer 4–6 |
| `{CATEGORY_N}` | name of each confirming category |
| `{ENTRY_LOW/HIGH}` | hourly-refined entry zone prices |
| `{STOP}` | entry − (1.5 × ATR14), rounded to 2dp |
| `{TARGET}` | entry + (3.0 × ATR14), rounded to 2dp |
| `{RR}` | e.g. `1:2.0` |
| `{PATTERN_NOTE}` | statistical pattern or sector-rotation note, or omit line if none |
| `{BRIEF_REASON}` | one short phrase, e.g. `3/6 — volume weak` |

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*
```

**`{REASON}` examples:**
- `VIX at 32.1 — above 30 threshold`
- `FOMC announcement day`
- `US CPI release day`
- `US Non-Farm Payrolls release day`

---

## Template C — No signals, no kill-switch (all tickers scored ≤ 3/6)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

All 12 tickers scored ≤ 3/6 confluence. Highest: {BEST_TICKER} at {BEST_SCORE}/6.
*Macro:* SPY {SPY_DIRECTION} | QQQ {QQQ_DIRECTION} | VIX {VIX_LEVEL}

_Excluded (earnings blackout): {TICKER_LIST}_
⚠️ _Signals are research output, not financial advice._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
