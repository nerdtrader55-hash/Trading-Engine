# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome and fill every `{PLACEHOLDER}` before posting. Never post raw placeholders.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
SPY futures: {SPY_FUTURES_PCT}% | QQQ futures: {QQQ_FUTURES_PCT}%
VIX: {VIX_LEVEL} ({VIX_CHANGE}) | DXY: {DXY_LEVEL} ({DXY_CHANGE})
10Y yield: {YIELD_LEVEL}% ({YIELD_CHANGE}bps)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*SIGNALS*
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER_1} : BUY
Confidence: {CONFIDENCE_1} ({SCORE_1}/6) | Entry zone: {ENTRY_LOW_1}–{ENTRY_HIGH_1}
Stop: {STOP_1} | Target: {TARGET_1} | R:R {RR_1}
Key reason: {REASON_1}
{PATTERN_NOTE_1}

{TICKER_2} : BUY
Confidence: {CONFIDENCE_2} ({SCORE_2}/6) | Entry zone: {ENTRY_LOW_2}–{ENTRY_HIGH_2}
Stop: {STOP_2} | Target: {TARGET_2} | R:R {RR_2}
Key reason: {REASON_2}
{PATTERN_NOTE_2}

{TICKER_3} : BUY
Confidence: {CONFIDENCE_3} ({SCORE_3}/6) | Entry zone: {ENTRY_LOW_3}–{ENTRY_HIGH_3}
Stop: {STOP_3} | Target: {TARGET_3} | R:R {RR_3}
Key reason: {REASON_3}
{PATTERN_NOTE_3}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*ALSO WATCHING* (scored 3/6 — not enough to signal)
{WATCHING_LIST}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*EXCLUDED*
{EXCLUDED_EARNINGS}
{EXCLUDED_DATA_FAILURES}

⚠️ _Research signals only. Not financial advice. Always size within your own risk envelope._
```

### Filling rules for Template A

- Omit entire signal blocks for tickers 2 and/or 3 if fewer than 2 or 3 signals fired.
- `{CONFIDENCE_N}`: write `HIGH` (5–6/6) or `MEDIUM` (4/6).
- `{REASON_N}`: one concise phrase — e.g. "Bull flag breakout above $132 on above-avg volume; RSI 58 with MACD cross."
- `{PATTERN_NOTE_N}`: only include if a statistical pattern was matched (module 10). E.g. "Pattern hit rate last 12mo: 3/3, avg +2.1% over 5d." Omit the line entirely if no match.
- `{WATCHING_LIST}`: comma-separated tickers, or "None" if no ticker reached 3/6.
- `{EXCLUDED_EARNINGS}`: e.g. "Earnings blackout (next date): AAPL (2026-07-31), NVDA (2026-08-20)" — omit line if no exclusions.
- `{EXCLUDED_DATA_FAILURES}`: e.g. "Data unavailable: GEV, MCO" — omit line if none.
- All prices in USD to 2 decimal places.
- All times in GMT.

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

Next scheduled run: tomorrow at 02:30 GMT (weekdays only).

⚠️ _Research signals only. Not financial advice._
```

### Kill-switch reason strings

| Condition | `{REASON}` text |
|---|---|
| VIX above threshold | `VIX at {VIX_LEVEL} — above the {VIX_MAX} threshold. Standing aside until volatility normalises.` |
| FOMC announcement day | `FOMC rate decision today. No signals on Fed days.` |
| CPI release day | `US CPI data releases today. No signals on major inflation print days.` |
| NFP / jobs day | `US Non-Farm Payrolls release today. No signals on jobs days.` |
| PCE release day | `US PCE inflation data releases today. No signals on major data days.` |

---

## Template C — All WAIT (no kill-switch, but no ticker reached 4/6)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
SPY futures: {SPY_FUTURES_PCT}% | QQQ futures: {QQQ_FUTURES_PCT}%
VIX: {VIX_LEVEL} ({VIX_CHANGE}) | DXY: {DXY_LEVEL} ({DXY_CHANGE})

No ticker in the universe reached the 4/6 confluence threshold today.

*CLOSEST SETUPS* (highest scorers — not signals)
{TOP_SCORERS}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*EXCLUDED*
{EXCLUDED_EARNINGS}
{EXCLUDED_DATA_FAILURES}

⚠️ _Research signals only. Not financial advice._
```

### Filling rules for Template C

- `{TOP_SCORERS}`: list up to 3 tickers with their score and the missing confluence factor, e.g.:
  ```
  • NVDA (3/6) — blocked by: Sentiment NEGATIVE (headline: "US export controls expanded")
  • META (3/6) — blocked by: Volume WEAK (below 70% avg)
  ```
- If all tickers were excluded for earnings or data failures, use Template B language instead.

---

## General formatting rules

1. Use Slack markdown: `*bold*`, `_italic_`, backticks for code/tickers — no HTML.
2. The `━` divider line is 40 characters. Keep it consistent.
3. Never include raw `{PLACEHOLDER}` text in the posted message.
4. Prices: always include `$` prefix and 2 decimal places.
5. Percentages: always include `%` suffix and 1 decimal place (e.g. `+1.2%`).
6. R:R format: `1:2.0` (always show one decimal place on the reward side).
7. Dates: `YYYY-MM-DD`. Times: `HH:MM GMT`.
