# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.
Render all placeholders in curly braces before posting. Use Slack mrkdwn formatting only — no HTML.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 *PRE-MARKET SIGNALS — {DATE} {TIME} GMT*
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{FOR EACH HIGH CONFIDENCE SIGNAL (score 5–6/6) — render one block per ticker}
*{TICKER}* — BUY  🟢 HIGH CONFIDENCE  ({SCORE}/6)
Entry zone: ${ENTRY_LOW} – ${ENTRY_HIGH}  |  Stop: ${STOP}  |  Target: ${TARGET}  |  R:R 1:{RR}
Confirmed: {CONFIRMED_CATEGORIES}
{PATTERN_NOTE — one line if Module 10 found a named pattern, else omit this line}

{FOR EACH MEDIUM CONFIDENCE SIGNAL (score 4/6) — render one block per ticker}
*{TICKER}* — BUY  🟡 MEDIUM CONFIDENCE  ({SCORE}/6)
Entry zone: ${ENTRY_LOW} – ${ENTRY_HIGH}  |  Stop: ${STOP}  |  Target: ${TARGET}  |  R:R 1:{RR}
Confirmed: {CONFIRMED_CATEGORIES}
⚠️ Smaller position size — 4/6 confluence only

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 *ALSO WATCHING* (3/6 — not signalling)
{TICKER}: {MISSING_CATEGORIES}
{... one line per near-miss ticker, or "None" if no near misses}

⏳ *EARNINGS BLACKOUT*: {BLACKOUT_TICKERS_WITH_DATES — or "None today"}
{DATA_FAILURES — "⚠️ Data unavailable: {TICKERS}" — omit line if no failures}

_Max open positions: 3 · Never risk >2% per trade · Not financial advice_
```

**Placeholder reference for Template A:**

| Placeholder | Source |
|---|---|
| `{DATE}` | Today's date in DD-Mon-YYYY (e.g. 10-Jun-2026) |
| `{TIME}` | Signal post time in HH:MM (e.g. 05:47) |
| `{TICKER}` | Ticker symbol from config/stocks.json |
| `{SCORE}` | Number of categories confirmed (4, 5, or 6) |
| `{ENTRY_LOW}` | Nearest hourly support within 2× ATR14 below pre-market price |
| `{ENTRY_HIGH}` | Current pre-market ask price |
| `{STOP}` | Entry − (1.5 × ATR14), rounded to 2 d.p. |
| `{TARGET}` | Entry + (3.0 × ATR14), rounded to 2 d.p. |
| `{RR}` | Actual risk:reward ratio (e.g. 2.1) |
| `{CONFIRMED_CATEGORIES}` | Comma-separated list of confirmed category names (e.g. Trend, Momentum, Volume, Price Action) |
| `{PATTERN_NOTE}` | Short pattern label from Module 10 (e.g. "Bull flag, 8-session base") |
| `{BLACKOUT_TICKERS_WITH_DATES}` | e.g. "AAPL (earnings 15-Jun), NVDA (earnings 18-Jun)" |
| `{DATA_FAILURES}` | Tickers skipped due to Alpha Vantage errors |

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 *NO SIGNALS TODAY — {DATE} {TIME} GMT*
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

{KILL_SWITCH_DETAIL — one line of context, e.g. "VIX at 34.2 (threshold: 30)" or "FOMC rate decision at 19:00 GMT"}

_Routine will run normally tomorrow unless conditions persist._
```

**Kill-switch reasons (use exact strings):**

| Condition | `{REASON}` string |
|---|---|
| VIX above threshold | `VIX {VIX_VALUE} exceeds limit of {VIX_MAX}` |
| FOMC announcement day | `FOMC rate decision today` |
| CPI release day | `US CPI data release today` |
| NFP release day | `US Non-Farm Payrolls release today` |
| Fed speaker (caution only — do not kill, just note) | Add to footer of Template A instead |

---

## Template C — No signals (confluence threshold not met, no kill-switch)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ *NO SIGNALS TODAY — {DATE} {TIME} GMT*
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No ticker reached the 4/6 confluence threshold today.

*Closest setups (3/6):*
{TICKER}: missing {MISSING_CATEGORIES}
{... one line per near-miss, or "None" if all scored ≤2}

⏳ *EARNINGS BLACKOUT*: {BLACKOUT_TICKERS_WITH_DATES — or "None today"}
{DATA_FAILURES — omit if none}

_Waiting is a position. Not financial advice._
```

---

## Formatting rules

1. Always open with the separator line and bold header.
2. HIGH confidence blocks come before MEDIUM confidence blocks.
3. List "ALSO WATCHING" only when at least one ticker scored 3/6.
4. Never post prices or levels you did not fetch from Alpha Vantage this run — no fabricated values.
5. Post Template A when ≥1 BUY signal exists, Template B when a kill-switch fired, Template C when no signal and no kill-switch.
6. Never post more than one Slack message per run.
