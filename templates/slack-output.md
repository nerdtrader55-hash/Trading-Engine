# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run (one or more BUY signals)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Macro: VIX {VIX_LEVEL} | Futures {FUTURES_DIRECTION} | {MACRO_NOTE}

{TICKER} : BUY  ← {CONFIDENCE} ({SCORE}/6)
{TICKER} : BUY  ← {CONFIDENCE} ({SCORE}/6)

Also watching (3/6 — not enough to signal today):
{TICKER}, {TICKER}

Excluded for earnings: {TICKER} (next: {DATE})

⚠️ Research use only. Not financial advice. Size within your own risk rules.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Field definitions:**
- `{DATE}` — today's date, e.g. `Wed 04 Jun 2026`
- `{TIME}` — signal generation time in GMT, e.g. `06:00`
- `{VIX_LEVEL}` — current VIX reading, e.g. `17.4`
- `{FUTURES_DIRECTION}` — `GREEN ▲`, `RED ▼`, or `MIXED ↔`
- `{MACRO_NOTE}` — one-line macro context, e.g. `DXY flat, yields steady`
- `{TICKER}` — exact ticker from config/stocks.json
- `{CONFIDENCE}` — `HIGH` (5–6/6) or `MEDIUM` (4/6)
- `{SCORE}` — number of confluence categories confirmed (4, 5, or 6)
- "Also watching" — tickers scoring exactly 3/6, worth monitoring
- "Excluded for earnings" — tickers in the 3-day pre/1-day post earnings blackout; omit line if none

**Rules:**
- Maximum 3 BUY lines. If more than 3 score ≥4/6, emit only the top 3 by confluence score; remaining go in "Also watching".
- Keep each BUY line to one line — no multi-line blocks per ticker.
- Do not add emojis or extra formatting beyond this template.
- Replace all `{PLACEHOLDER}` tokens — never leave curly braces in the sent message.

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: {REASON}
```

**Reason examples:**
- `VIX at 32.1 — above 30 threshold`
- `FOMC rate decision today`
- `US CPI release today`
- `US Non-Farm Payrolls today`

---

## Template C — No signals (all tickers scored < 4/6, no kill-switch)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No signals today — insufficient confluence across all 12 tickers.

Also watching (3/6):
{TICKER}, {TICKER}

⚠️ Research use only. Not financial advice.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
