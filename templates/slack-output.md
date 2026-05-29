# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run (one block per BUY signal, max 3)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE}  {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO*  VIX {VIX} | S&P {SPY_DIR} | Nasdaq {QQQ_DIR} | DXY {DXY_TREND}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY
Confidence: {HIGH|MEDIUM} ({SCORE}/6)  |  Entry: {ENTRY_LOW}–{ENTRY_HIGH}
Stop: {STOP}  |  Target: {TARGET}  |  R:R 1:{RR}
{PATTERN_NOTE}

[repeat block above for each BUY signal, up to 3]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Also watching (close but below threshold): {WATCHING_LIST or "none"}
Excluded — earnings blackout: {EXCLUDED_LIST or "none"}

⚠️ Not financial advice. Signals are for review only. Max 1–2% risk per trade.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Field guide:**

| Placeholder | What to put |
|---|---|
| `{DATE}` | e.g. `Fri 29 May 2026` |
| `{TIME}` | Current time in Europe/London, e.g. `06:12` |
| `{VIX}` | Current VIX level, e.g. `16.4` |
| `{SPY_DIR}` | `▲ +0.3%` or `▼ −0.4%` (pre-market futures direction) |
| `{QQQ_DIR}` | Same format |
| `{DXY_TREND}` | `↑ rising` / `↓ falling` / `→ flat` |
| `{TICKER}` | Exact ticker from `config/stocks.json`, e.g. `NVDA` |
| `{HIGH\|MEDIUM}` | HIGH = 5–6/6 confirm, MEDIUM = 4/6 |
| `{SCORE}` | Integer 4–6 |
| `{ENTRY_LOW}` | Lower bound of hourly entry zone (nearest hourly support) |
| `{ENTRY_HIGH}` | Upper bound (current pre-market price or last close) |
| `{STOP}` | `entry − (1.5 × ATR14)`, rounded to 2 d.p. |
| `{TARGET}` | `entry + (3.0 × ATR14)`, rounded to 2 d.p. |
| `{RR}` | Actual R:R ratio, e.g. `2.1` |
| `{PATTERN_NOTE}` | One line from module 10, e.g. `Bull flag — hit rate 3/3 last 12mo, avg +2.1% over 5d` — omit if no pattern |
| `{WATCHING_LIST}` | Tickers that scored 3/6 or were close — comma-separated |
| `{EXCLUDED_LIST}` | Tickers in earnings blackout with their next date, e.g. `AAPL (next: 2026-07-30)` |

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE}  {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*
```

Reasons: `VIX above 30`, `FOMC announcement day`, `CPI release day`, `NFP release day`

---

## Template C — Analysis complete, no tickers qualified

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ WAIT — {DATE}  {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO*  VIX {VIX} | S&P {SPY_DIR} | Nasdaq {QQQ_DIR}

No tickers reached the 4/6 confluence threshold today.
Best setup: {BEST_TICKER} scored {BEST_SCORE}/6.

Excluded — earnings blackout: {EXCLUDED_LIST or "none"}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
