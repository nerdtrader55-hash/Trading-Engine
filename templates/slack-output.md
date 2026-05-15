# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.
Fill every `{PLACEHOLDER}` from the data computed during the run. Never leave a placeholder unfilled — if data is unavailable, replace it with `N/A`.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Macro: SPY {SPY_DIRECTION} · QQQ {QQQ_DIRECTION} · VIX {VIX_LEVEL} · DXY {DXY_DIRECTION}

*{TICKER_1}* : BUY   [{CONFIDENCE_1} — {SCORE_1}/6]
Entry zone: {ENTRY_LOW_1} – {ENTRY_HIGH_1}
Stop: {STOP_1}  |  Target: {TARGET_1}  |  R:R 1:{RR_1}
Why: {REASON_1A} · {REASON_1B} · {REASON_1C}
{PATTERN_NOTE_1}

*{TICKER_2}* : BUY   [{CONFIDENCE_2} — {SCORE_2}/6]
Entry zone: {ENTRY_LOW_2} – {ENTRY_HIGH_2}
Stop: {STOP_2}  |  Target: {TARGET_2}  |  R:R 1:{RR_2}
Why: {REASON_2A} · {REASON_2B} · {REASON_2C}
{PATTERN_NOTE_2}

*{TICKER_3}* : BUY   [{CONFIDENCE_3} — {SCORE_3}/6]
Entry zone: {ENTRY_LOW_3} – {ENTRY_HIGH_3}
Stop: {STOP_3}  |  Target: {TARGET_3}  |  R:R 1:{RR_3}
Why: {REASON_3A} · {REASON_3B} · {REASON_3C}
{PATTERN_NOTE_3}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Also watching (scored but no signal): {WATCHING_LIST}
Excluded (earnings blackout): {EXCLUDED_EARNINGS}
Data unavailable: {FAILURES}
⚠️ Research only. Not financial advice. Never risk more than you can afford to lose.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Placeholder guide**

| Placeholder | Where it comes from |
|---|---|
| `{DATE}` | Today's date, format `DD MMM YYYY` |
| `{TIME}` | Time of run completion (timezone from `runtime.json`) |
| `{SPY_DIRECTION}` | `▲ +X.X%` or `▼ -X.X%` pre-market vs. prior close |
| `{QQQ_DIRECTION}` | Same for QQQ |
| `{VIX_LEVEL}` | Current VIX level (1 decimal place) |
| `{DXY_DIRECTION}` | `strengthening` / `weakening` / `flat` (5-day change) |
| `{TICKER_N}` | Ticker symbol, e.g. `NVDA` |
| `{CONFIDENCE_N}` | `HIGH` (5–6/6) or `MEDIUM` (4/6) |
| `{SCORE_N}` | Integer 4–6 (confluence categories confirmed) |
| `{ENTRY_LOW_N}` | Lower bound of hourly-refined entry zone (from module 09) |
| `{ENTRY_HIGH_N}` | Upper bound — current pre-market price or last close |
| `{STOP_N}` | Entry − (1.5 × ATR14), rounded to 2 decimal places |
| `{TARGET_N}` | Entry + (3.0 × ATR14), rounded to 2 decimal places |
| `{RR_N}` | Risk:reward ratio, e.g. `2.1` |
| `{REASON_NA/B/C}` | Three short phrases: one per confirming module (e.g. `EMA bull stack`, `RSI 54 rising`, `volume 140% avg`) |
| `{PATTERN_NOTE_N}` | One-line note from module 10 if a named pattern is present, e.g. `Pattern: bull flag — hit rate 3/3 last 12mo, avg +2.1% over 5d`. Omit line entirely if no pattern. |
| `{WATCHING_LIST}` | Tickers that were analysed but scored ≤3/6, e.g. `AAPL, META, UBER` |
| `{EXCLUDED_EARNINGS}` | Tickers skipped due to earnings blackout, e.g. `CRWD (next: 2026-06-09)`. Write `none` if none. |
| `{FAILURES}` | Tickers skipped due to data errors. Write `none` if none. |

**Signal count rule:** Include only as many ticker blocks as there are BUY signals (max 3). Remove unused `{TICKER_2}` / `{TICKER_3}` blocks entirely rather than leaving them with placeholder text.

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

{KILL_SWITCH_DETAIL}

⚠️ Research only. Not financial advice.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Kill-switch reasons and detail strings**

| `{REASON}` | `{KILL_SWITCH_DETAIL}` |
|---|---|
| `VIX too high` | `VIX at {VIX_LEVEL} — above the {VIX_MAX} threshold. Resuming analysis when VIX drops.` |
| `FOMC announcement day` | `Fed rate decision due today. No signals on FOMC days per risk rules.` |
| `CPI release day` | `US CPI data due today. Extreme volatility expected — standing aside.` |
| `NFP release day` | `US Non-Farm Payrolls due today. Extreme volatility expected — standing aside.` |

---

## Template C — No signals (all tickers scored ≤3/6, no kill-switch)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Analysed {TICKERS_ANALYSED} tickers. No setup reached the 4/6 confluence threshold.

Macro: SPY {SPY_DIRECTION} · QQQ {QQQ_DIRECTION} · VIX {VIX_LEVEL}
Closest: {CLOSEST_TICKER} scored {CLOSEST_SCORE}/6 — missing: {MISSING_CATEGORIES}

Excluded (earnings): {EXCLUDED_EARNINGS}
⚠️ Research only. Not financial advice.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
