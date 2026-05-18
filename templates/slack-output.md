# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
VIX: {VIX_LEVEL} | S&P Futures: {SPY_FUTURES_PCT}% | QQQ Futures: {QQQ_FUTURES_PCT}%
DXY: {DXY_LEVEL} ({DXY_5D_CHANGE}) | 10Y Yield: {YIELD_LEVEL}% ({YIELD_5D_CHANGE}bps)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER_1} : BUY  [{CONFIDENCE_1}] — {CONFLUENCE_SCORE_1}/6
{TICKER_2} : BUY  [{CONFIDENCE_2}] — {CONFLUENCE_SCORE_2}/6
{TICKER_3} : BUY  [{CONFIDENCE_3}] — {CONFLUENCE_SCORE_3}/6

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*ALSO WATCHING (3/6 — below threshold today)*
{WATCHING_LIST}

*EXCLUDED (earnings blackout)*
{EARNINGS_EXCLUDED_LIST}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Research only. Not financial advice. Size within your own risk envelope.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Field notes:**
- `{CONFIDENCE_1}` = `HIGH` (5–6/6) or `MEDIUM` (4/6). MEDIUM gets a note to use smaller size.
- `{WATCHING_LIST}` = comma-separated tickers that scored exactly 3/6, or `None` if empty.
- `{EARNINGS_EXCLUDED_LIST}` = format `AAPL (next: 2025-07-31), NVDA (next: 2025-05-28)`, or `None`.
- Omit ticker rows that didn't fire — never show WAIT entries in the signal block.
- If zero BUY signals fire, use Template C instead.

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*
```

**Reason examples:** `VIX above 30 (current: {VIX_LEVEL})`, `FOMC announcement day`, `CPI release day`, `NFP release day`

---

## Template C — No signals (confluence insufficient, no kill-switch)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No ticker reached the 4/6 confluence threshold today.

*Closest setups (highest scores):*
{TOP_SCORER_1}: {SCORE_1}/6 — missing: {MISSING_CATEGORIES_1}
{TOP_SCORER_2}: {SCORE_2}/6 — missing: {MISSING_CATEGORIES_2}

*Excluded (earnings blackout):*
{EARNINGS_EXCLUDED_LIST}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Research only. Not financial advice.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
