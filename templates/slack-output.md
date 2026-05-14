# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
SPY futures: {SPY_FUTURES_DIRECTION} {SPY_FUTURES_PCT}%
QQQ futures: {QQQ_FUTURES_DIRECTION} {QQQ_FUTURES_PCT}%
VIX: {VIX_LEVEL} ({VIX_TREND})
DXY: {DXY_LEVEL} ({DXY_5D_CHANGE})
10Y yield: {YIELD_LEVEL}% ({YIELD_5D_CHANGE}bps)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*SIGNALS*
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER_1} : BUY  [{CONFIDENCE_1} | {SCORE_1}/6]
{TICKER_2} : BUY  [{CONFIDENCE_2} | {SCORE_2}/6]
{TICKER_3} : BUY  [{CONFIDENCE_3} | {SCORE_3}/6]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*ALSO WATCHING* (scored but did not meet threshold)
{WATCHING_LIST}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*EXCLUDED — EARNINGS BLACKOUT*
{EARNINGS_EXCLUSIONS}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Research only. Not financial advice. Never risk more than you can afford to lose.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Placeholder rules

| Placeholder | What to fill in |
|---|---|
| `{DATE}` | e.g. `Wed 14 May 2026` |
| `{TIME}` | e.g. `06:00` |
| `{SPY_FUTURES_DIRECTION}` | `▲` or `▼` |
| `{SPY_FUTURES_PCT}` | pre-market % change, e.g. `+0.42` |
| `{QQQ_FUTURES_DIRECTION}` | `▲` or `▼` |
| `{QQQ_FUTURES_PCT}` | pre-market % change |
| `{VIX_LEVEL}` | numeric, e.g. `17.4` |
| `{VIX_TREND}` | `falling` / `flat` / `rising` |
| `{DXY_LEVEL}` | numeric |
| `{DXY_5D_CHANGE}` | e.g. `+0.3%` or `-0.2%` |
| `{YIELD_LEVEL}` | 10Y yield, e.g. `4.31` |
| `{YIELD_5D_CHANGE}` | bps change, e.g. `+8` or `-5` |
| `{TICKER_N}` | uppercase ticker symbol |
| `{CONFIDENCE_N}` | `HIGH` or `MEDIUM` |
| `{SCORE_N}` | number of confirming categories, e.g. `5` |
| `{WATCHING_LIST}` | comma-separated tickers with score, e.g. `NVDA (3/6), AAPL (3/6)` — or `None` |
| `{EARNINGS_EXCLUSIONS}` | e.g. `NVDA (next: 2026-05-20), CRWD (next: 2026-06-09)` — or `None` |

**Signal count rules:**
- Omit `{TICKER_2}` and `{TICKER_3}` lines if fewer than 2 or 3 signals fired.
- Max 3 BUY lines. Highest confluence score wins when more than 3 qualify.
- If ZERO signals fired (but no kill-switch), use Template C below.

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

Reason detail: {REASON_DETAIL}

Next scheduled run: tomorrow pre-market.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Research only. Not financial advice.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

| Placeholder | Examples |
|---|---|
| `{REASON}` | `VIX > 30`, `FOMC announcement day`, `CPI release day`, `NFP release day` |
| `{REASON_DETAIL}` | e.g. `VIX at 32.1 — market-wide risk-off. All signals paused.` |

---

## Template C — No kill-switch, but no tickers met the threshold

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Macro is clear. No ticker in the universe reached 4/6 confluence today.

*ALSO WATCHING* (highest scorers)
{WATCHING_LIST}

*EXCLUDED — EARNINGS BLACKOUT*
{EARNINGS_EXCLUSIONS}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Research only. Not financial advice.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
