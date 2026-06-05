# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

Use when at least one ticker scores ≥ 4/6 confluence AND passes the R:R gate.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE}  {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{REPEAT THE BLOCK BELOW FOR EACH SIGNAL — MAX 3}

*{TICKER}* : BUY
Confidence: {HIGH|MEDIUM}  ·  Score: {N}/6  ·  R:R 1:{RR}
Entry: ${ENTRY}  |  Stop: ${STOP}  |  Target: ${TARGET}
Key confluences: {FACTOR_1}  ·  {FACTOR_2}  ·  {FACTOR_3}

{END REPEAT}
─────────────────────────────────────────
👀 Also watching: {TICKER} ({N}/6){, {TICKER} ({N}/6)}
🚫 Earnings blackout: {TICKER}{, {TICKER}}
─────────────────────────────────────────
⚠️ _Signals only — not financial advice. Risk ≤1–2% per trade._
```

### Field guide

| Placeholder | Source |
|---|---|
| `{DATE}` | Today's date in `DD Mon YYYY` format, e.g. `05 Jun 2026` |
| `{TIME}` | Wall-clock time of signal post in the timezone set in `config/runtime.json` |
| `{TICKER}` | Exact ticker symbol, e.g. `NVDA` |
| `{HIGH\|MEDIUM}` | HIGH when score is 5–6/6; MEDIUM when score is 4/6 |
| `{N}/6` | Count of confluence categories confirmed for this ticker |
| `{RR}` | Risk:reward ratio rounded to one decimal, e.g. `2.4` |
| `{ENTRY}` | Midpoint of hourly entry zone from module 09, to 2 d.p. |
| `{STOP}` | Entry − (1.5 × ATR14), to 2 d.p. |
| `{TARGET}` | Entry + (3.0 × ATR14), to 2 d.p. |
| `{FACTOR_1..3}` | 2–4 word label for each confirming confluence category, e.g. `Bull EMA stack`, `MACD crossover`, `Volume surge` |
| Also watching | Tickers that scored exactly 3/6 (close but not triggering) |
| Earnings blackout | Tickers excluded because earnings fall within 3 trading days |

### Example rendered output

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — 05 Jun 2026  06:05 GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*NVDA* : BUY
Confidence: HIGH  ·  Score: 5/6  ·  R:R 1:2.8
Entry: $1,142.50  |  Stop: $1,107.30  |  Target: $1,248.10
Key confluences: Bull EMA stack  ·  MACD crossover  ·  Volume surge

*META* : BUY
Confidence: MEDIUM  ·  Score: 4/6  ·  R:R 1:2.1
Entry: $512.40  |  Stop: $498.10  |  Target: $542.60
Key confluences: Support hold  ·  RSI reset  ·  Positive sentiment

─────────────────────────────────────────
👀 Also watching: AMZN (3/6), V (3/6)
🚫 Earnings blackout: CRWD, AAPL
─────────────────────────────────────────
⚠️ _Signals only — not financial advice. Risk ≤1–2% per trade._
```

---

## Template B — Kill-switch fired (no signals today)

Use when a macro kill-switch fires: VIX > 30, FOMC day, CPI day, or NFP day.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE}  {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*
```

`{REASON}` examples:
- `VIX at 32.4 — above kill-switch threshold of 30`
- `FOMC rate decision today — elevated volatility risk`
- `US CPI release today — signal reliability compromised`
- `Non-Farm Payrolls today — extreme intraday volatility expected`

---

## Template C — Analysis complete but no signals qualify

Use when kill-switches do not fire but no ticker reaches the 4/6 confluence threshold (or all pass earnings blackout). This is NOT a kill-switch day — the market is open, conditions just don't support a signal.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 NO SIGNALS TODAY — {DATE}  {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No tickers reached the 4/6 confluence threshold today.

👀 Closest setups: {TICKER} ({N}/6){, {TICKER} ({N}/6)}
🚫 Earnings blackout: {TICKER}{, {TICKER}}
─────────────────────────────────────────
⚠️ _Signals only — not financial advice. Risk ≤1–2% per trade._
```

If every ticker is in earnings blackout, replace the body with:
`All active tickers are within the earnings blackout window. No analysis run.`
