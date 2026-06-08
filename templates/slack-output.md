# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*{TICKER}* : BUY  ·  {HIGH|MEDIUM} confidence  ({SCORE}/6)
> Entry ~${ENTRY}  |  Stop ${STOP}  |  Target ${TARGET}  |  R:R 1:{RR}
> ✅ {CAT1}  ✅ {CAT2}  ✅ {CAT3}  ✅ {CAT4}  [{✅|⚠️} {CAT5}]  [{✅|⚠️} {CAT6}]

[Repeat the block above for each BUY signal — maximum 3 total]

─────────────────────────────────────────
👀 *Also watching (3/6):* {TICKER_LIST or "None"}
🚫 *Earnings blackout:* {TICKER_LIST or "None"}
⏭ *Data failure / skipped:* {TICKER_LIST or "None"}
─────────────────────────────────────────
_Signal engine only — not financial advice. Risk max 1-2% per trade._
```

**Filling the template:**

- `{DATE}` — e.g. `Mon 09 Jun 2026`
- `{TIME}` — 24h GMT, e.g. `05:47`
- `{TICKER}` — exact symbol from `config/stocks.json`
- `{HIGH|MEDIUM}` — `HIGH` if confluence score 5–6, `MEDIUM` if 4
- `{SCORE}` — integer out of 6 (number of confirming categories)
- `{ENTRY}` — last daily close (used as indicative entry reference)
- `{STOP}` — entry − (1.5 × ATR14), rounded to 2 decimal places
- `{TARGET}` — entry + (3.0 × ATR14), rounded to 2 decimal places
- `{RR}` — computed ratio, display as `2.0` (always ≥ 2.0 to reach this template)
- Category checkmarks — list each of the 6 confluence categories; use ✅ if it confirmed BUY, ⚠️ if it did not
- The 6 categories in order: `Trend` `Momentum` `Volume` `Price Action` `Macro` `Sentiment`
- `Also watching` — tickers that scored exactly 3/6 (close but no signal)
- `Earnings blackout` — tickers excluded due to the 3-day earnings window
- `Data failure / skipped` — tickers where Alpha Vantage returned null/error

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_{REASON_DETAIL}_
```

**Kill-switch reasons:**

| `{REASON}` | `{REASON_DETAIL}` |
|---|---|
| `VIX > 30` | `VIX at {LEVEL} — all signals suspended until VIX returns below 30.` |
| `FOMC day` | `Federal Reserve meeting / announcement today. No signals on FOMC days.` |
| `CPI release` | `US CPI data releasing today. Extreme volatility expected — standing aside.` |
| `NFP release` | `Non-Farm Payrolls releasing today. Extreme volatility expected — standing aside.` |

---

## Template C — No BUY signals (all stocks WAIT after full analysis)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SCAN — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No signals today. {N} stocks scanned — none reached the 4/6 confluence threshold.

👀 *Closest (3/6):* {TICKER_LIST or "None"}
🚫 *Earnings blackout:* {TICKER_LIST or "None"}
─────────────────────────────────────────
_Signal engine only — not financial advice._
```
