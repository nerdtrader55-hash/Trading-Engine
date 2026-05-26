# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🌍 MACRO SNAPSHOT
• S&P 500 futures: {SPY_FUTURES_DIRECTION} ({SPY_FUTURES_PCT}%)
• Nasdaq futures:  {QQQ_FUTURES_DIRECTION} ({QQQ_FUTURES_PCT}%)
• VIX:             {VIX_LEVEL} — {VIX_STATUS}
• DXY:             {DXY_LEVEL} ({DXY_5D_CHANGE}% 5d)
• 10Y yield:       {YIELD_10Y}% ({YIELD_5D_CHANGE}bps 5d)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔔 SIGNALS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY  [{CONFIDENCE} — {SCORE}/6]
  Entry zone : {ENTRY_LOW} – {ENTRY_HIGH}
  Stop       : {STOP_PRICE}  (−{STOP_PCT}%)
  Target     : {TARGET_PRICE}  (+{TARGET_PCT}%)
  R:R        : 1:{RR_RATIO}
  Confluence : {CONFLUENCE_FLAGS}
  Note       : {PATTERN_OR_ANALOG_NOTE}

{REPEAT_BLOCK_PER_SIGNAL}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
👁 ALSO WATCHING (scored 3/6 — WAIT)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
{WATCHING_TICKERS}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ℹ️  FOOTNOTES
• Excluded for earnings (next date): {EARNINGS_EXCLUDED_LIST}
• Data errors / skipped: {SKIPPED_LIST}
• ⚠️ Not financial advice. Always apply your own risk management.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Field guide:**

| Placeholder | How to populate |
|---|---|
| `{CONFIDENCE}` | HIGH (5–6/6) or MEDIUM (4/6) |
| `{CONFLUENCE_FLAGS}` | Comma-separated: Trend ✓, Momentum ✓, Volume ✓, Price Action ✓, Macro ✓, Sentiment ✓ — use ✗ for non-confirms |
| `{PATTERN_OR_ANALOG_NOTE}` | From framework/10. E.g. "Bull flag; hit rate 3/3, avg +2.1% 5d" — omit if no analog |
| `{WATCHING_TICKERS}` | Tickers that scored 3/6 with their missing confirm category noted. If none, write "None today." |
| `{EARNINGS_EXCLUDED_LIST}` | e.g. "AAPL (Jun 5), NVDA (Jun 12)" — write "None" if no exclusions |
| `{SKIPPED_LIST}` | Tickers where Alpha Vantage returned null/error. Write "None" if clean run. |

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

{REASON_DETAIL}

Next scheduled run: tomorrow pre-market.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Kill-switch reasons and their `{REASON_DETAIL}` text:**

| Reason | Detail text |
|---|---|
| VIX > 30 | "VIX at {VIX_LEVEL} — extreme fear, all signals suspended." |
| FOMC day | "Fed rate decision day. Market moves will be unpredictable." |
| CPI release | "CPI data releases today. Avoiding signals ahead of print." |
| NFP release | "Non-Farm Payrolls release today. Macro volatility expected." |

---

## Template C — No signals (clean run, but nothing qualified)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No signals today — no ticker reached 4/6 confluence.

🌍 Macro: VIX {VIX_LEVEL} | SPY futures {SPY_FUTURES_DIRECTION} | QQQ futures {QQQ_FUTURES_DIRECTION}

👁 CLOSEST SETUPS (scored 3/6)
{WATCHING_TICKERS}

• Excluded for earnings: {EARNINGS_EXCLUDED_LIST}
• ⚠️ Not financial advice.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
