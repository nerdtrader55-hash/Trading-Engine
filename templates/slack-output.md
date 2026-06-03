# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
Futures: {FUTURES_DIRECTION}
VIX: {VIX_LEVEL} | DXY: {DXY_LEVEL} | 10Y Yield: {YIELD_LEVEL}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SIGNALS ({SIGNAL_COUNT}/3 max)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*{TICKER}* : BUY  [{CONFIDENCE} confidence — {SCORE}/6]
Entry zone: {ENTRY_LOW} – {ENTRY_HIGH}
Stop: {STOP} | Target: {TARGET} | R:R 1:{RR}
Confluence: {CATEGORY_LIST}
{PATTERN_OR_ANALOG_NOTE}
_{TOP_HEADLINE}_

[repeat signal block for each BUY ticker, up to 3]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Also watching (scored 3/6 — need one more confirm):
{WAIT_TICKERS_LIST}

Excluded — earnings blackout:
{BLACKOUT_TICKER_LIST}

{DATA_ERRORS_IF_ANY}

⚠️ Signal engine only — not financial advice. Size within your own risk envelope.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Field reference**

| Placeholder | Source |
|---|---|
| `{DATE}` | Today's date in `DD MMM YYYY` format |
| `{TIME}` | Current GMT time at point of posting |
| `{FUTURES_DIRECTION}` | SPY/QQQ pre-market direction, e.g. "SPY +0.3% QQQ +0.5% 🟢" |
| `{VIX_LEVEL}` | Current VIX quote |
| `{DXY_LEVEL}` | DXY index current level |
| `{YIELD_LEVEL}` | US 10-year Treasury yield |
| `{CONFIDENCE}` | HIGH (5–6/6) or MEDIUM (4/6) |
| `{SCORE}` | Number of confluence categories confirmed (4, 5, or 6) |
| `{ENTRY_LOW}` / `{ENTRY_HIGH}` | Entry zone from hourly support analysis (module 09) |
| `{STOP}` | Entry − (1.5 × ATR14) |
| `{TARGET}` | Entry + (3.0 × ATR14) |
| `{RR}` | Risk:reward ratio (always ≥ 2.0) |
| `{CATEGORY_LIST}` | Comma-separated confirmed categories, e.g. "Trend ✅ Momentum ✅ Volume ✅ Price Action ✅" |
| `{PATTERN_OR_ANALOG_NOTE}` | From module 10, e.g. "Bull flag — 3/3 hit rate last 12mo, avg +2.1% 5d" |
| `{TOP_HEADLINE}` | Most relevant news headline (module 07) |
| `{WAIT_TICKERS_LIST}` | Tickers that scored 3/6 exactly, comma-separated |
| `{BLACKOUT_TICKER_LIST}` | Tickers excluded for earnings, with next date |
| `{DATA_ERRORS_IF_ANY}` | Tickers skipped due to AV errors, or omit line entirely |

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

{OPTIONAL_BRIEF_CONTEXT}

⚠️ Signal engine only — not financial advice.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**`{REASON}` values**

| Trigger | Reason string |
|---|---|
| VIX ≥ 30 | `VIX at {VIX_LEVEL} — extreme fear, standing aside` |
| FOMC day | `FOMC rate decision today — no signals on Fed days` |
| CPI release | `US CPI data today — no signals on major macro releases` |
| NFP release | `US Non-Farm Payrolls today — no signals on jobs day` |

---

## Template C — No signals but no kill-switch (all WAIT)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SCAN — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*Macro:* {FUTURES_DIRECTION} | VIX {VIX_LEVEL}

No setups reached the 4/6 confluence threshold today.

Best scores: {TOP_3_TICKERS_WITH_SCORES}

Excluded — earnings: {BLACKOUT_TICKER_LIST}

⚠️ Signal engine only — not financial advice.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
