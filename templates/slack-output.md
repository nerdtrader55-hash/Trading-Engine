# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*{TICKER}* : BUY   ✅ {CONFIDENCE} confidence  ({SCORE}/6)
*{TICKER}* : BUY   ✅ {CONFIDENCE} confidence  ({SCORE}/6)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Also watching (WAIT): {ALSO_WATCHING_LIST}

{EARNINGS_EXCLUSIONS_LINE}

_⚠️ Risk: Never risk more than 1-2% per trade. Signals are not guaranteed. Always apply your own judgement._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Substitution rules:**
- `{DATE}` — today's date in DD-Mon-YYYY format (e.g. `18-May-2026`)
- `{TIME}` — signal generation time in GMT (e.g. `05:47`)
- `{TICKER}` — exact ticker from config/stocks.json
- `{CONFIDENCE}` — `HIGH` (5-6/6) or `MEDIUM` (4/6)
- `{SCORE}` — numeric confluence score out of 6
- `{ALSO_WATCHING_LIST}` — comma-separated tickers that scored 3/6 (closest to threshold); omit if none
- `{EARNINGS_EXCLUSIONS_LINE}` — only include this line if tickers are excluded:
  `Excluded (earnings blackout): AAPL (30-Apr), NVDA (20-May)`
  Omit the line entirely if no tickers are excluded.
- Repeat the BUY line once per signal, max 3 lines total.

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Next run: tomorrow pre-market._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**{REASON} examples:**
- `VIX at 32.4 — above 30 kill-switch threshold`
- `FOMC announcement day — no signals on Fed days`
- `US CPI release today — extreme volatility expected`
- `US Non-Farm Payrolls today — extreme volatility expected`

---

## Template C — No signals (all tickers scored ≤ 3/6)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No tickers reached the 4/6 confluence threshold today.

Closest: {TICKER} ({SCORE}/6), {TICKER} ({SCORE}/6)

{EARNINGS_EXCLUSIONS_LINE}

_Next run: tomorrow pre-market._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
