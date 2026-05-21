# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.
Populate every `{PLACEHOLDER}` with real data — never leave placeholders in the live message.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY  [{CONFIDENCE} — {SCORE}/6]
Entry zone: ${ENTRY_LOW}–${ENTRY_HIGH}  |  Stop: ${STOP}  |  Target: ${TARGET}  |  R:R 1:{RR}
Edge: {ONE_LINE_REASON}

[Repeat the block above for each BUY signal — max 3 total]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📡 MACRO SNAPSHOT
VIX: {VIX} ({VIX_TREND})  |  SPY futures: {SPY_FUTURES}  |  QQQ futures: {QQQ_FUTURES}
DXY: {DXY_LEVEL} ({DXY_5D_CHANGE})  |  10Y yield: {YIELD}% ({YIELD_5D_CHANGE})

📋 ALSO WATCHING  (scored 3/6 — one confirm short)
{TICKER}: {MISSING_CATEGORY}
[List any tickers at 3/6. Omit section if none.]

🚫 EXCLUDED TODAY
Earnings blackout (next date): {EXCLUDED_EARNINGS_LIST}
Data unavailable: {EXCLUDED_DATA_FAIL_LIST}
[Omit a line if the list is empty.]

⚠️  Research / educational use only — not financial advice.
    Never risk more than 1–2% of account per trade.
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: {REASON}
[REASON examples: "VIX at 32.4 (> 30 threshold)" / "FOMC rate decision today" / "US CPI release day" / "NFP jobs data today"]

⚠️  Research / educational use only — not financial advice.
```

---

## Template C — No kill-switch but all tickers scored below threshold

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏳ NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

All tickers scored below the 4/6 confluence threshold. WAIT.

📋 CLOSEST TO THRESHOLD
{TICKER}: {SCORE}/6 — missing {MISSING_CATEGORIES}
[List up to 3 highest-scoring tickers with what's holding them back.]

📡 MACRO: VIX {VIX}  |  SPY {SPY_FUTURES}  |  QQQ {QQQ_FUTURES}

⚠️  Research / educational use only — not financial advice.
```

---

## Field reference

| Placeholder | Source |
|---|---|
| `{DATE}` | Today's date in DD-MMM-YYYY format |
| `{TIME}` | Current time HH:MM (24h) GMT |
| `{TICKER}` | Exact ticker from `config/stocks.json` |
| `{CONFIDENCE}` | `HIGH` if score ≥ 5/6, `MEDIUM` if 4/6 |
| `{SCORE}` | Number of confirming confluence categories (4–6) |
| `{ENTRY_LOW}` | Hourly support level from module 09 (entry_zone_low) |
| `{ENTRY_HIGH}` | Pre-market price or last close (entry_zone_high) |
| `{STOP}` | entry − (1.5 × ATR14), rounded to 2 d.p. |
| `{TARGET}` | entry + (3.0 × ATR14), rounded to 2 d.p. |
| `{RR}` | Actual R:R ratio (always ≥ 2.0 to reach this template) |
| `{ONE_LINE_REASON}` | Most important confirming factor in plain English |
| `{VIX}` | Current VIX level |
| `{VIX_TREND}` | `falling` / `rising` / `flat` vs. prior session |
| `{SPY_FUTURES}` | Pre-market SPY futures direction (+X.XX% or −X.XX%) |
| `{QQQ_FUTURES}` | Pre-market QQQ futures direction |
| `{DXY_LEVEL}` | Current DXY value |
| `{DXY_5D_CHANGE}` | 5-day DXY % change, e.g. `+0.3%` |
| `{YIELD}` | 10-year Treasury yield |
| `{YIELD_5D_CHANGE}` | 5-day change in bps, e.g. `+8bps` |
| `{MISSING_CATEGORY}` | Name of the one category blocking a signal |
| `{EXCLUDED_EARNINGS_LIST}` | Tickers + next earnings date, comma-separated |
| `{EXCLUDED_DATA_FAIL_LIST}` | Tickers where Alpha Vantage returned null/error |
| `{REASON}` | Plain-English kill-switch reason |
