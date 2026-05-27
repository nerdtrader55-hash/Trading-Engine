# Slack Output Templates

The routine sends **ONE** Slack message per run. Match the outcome to the template below and fill in every `{PLACEHOLDER}`. Send as a single message using the Slack connector — do not split into multiple posts.

Use Slack-compatible markdown only: `*bold*`, `_italic_`, `` `code` ``, `>blockquote`. No HTML.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
SPY futures: {+/-X.XX%}  |  QQQ futures: {+/-X.XX%}
VIX: {XX.X} ({rising / falling / stable})  |  DXY: {XX.X}  |  10Y yield: {X.XX}%

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY  ·  {HIGH / MEDIUM} CONFIDENCE  ({X}/6 confluence)

📈  Entry zone:   ${ENTRY_LOW} – ${ENTRY_HIGH}
🛑  Stop:         ${STOP_PRICE}  (–{STOP_PCT}%)
🎯  Target:       ${TARGET_PRICE}  (+{TARGET_PCT}%)
⚖️  R:R           1:{RR_RATIO}

*Confluence*
Trend ✅  |  Momentum ✅  |  Volume ✅  |  Price Action ✅  |  Macro {✅/❌}  |  Sentiment {✅/❌}

_{PRIMARY_REASON — one concise line explaining the key setup, e.g. "Bull flag breakout above $X resistance on above-avg volume with RSI 54 and MACD bullish cross"}_

{PATTERN_NOTE — optional. Only include if analog hit rate is available (≥3 prior occurrences):
"📐 Pattern: Bull flag. Last 3 occurrences on {TICKER} → hit rate 3/3, avg +2.1% over 5 days."}

{POSITION_SIZE_NOTE — include when MEDIUM confidence or VIX is elevated:
"⚠️ MEDIUM confidence — consider half normal size."}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{Repeat the signal block above for each BUY signal. Maximum 3 signals total.}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*Also watching* _(near signal — not yet 4/6 today)_
• {TICKER}: {X}/6  ·  {one-line reason, e.g. "RSI 62 + bull stack but volume weak (0.8× avg)"}
• {TICKER}: {X}/6  ·  {one-line reason}

{Omit this section entirely if no tickers reached 3/6.}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*Excluded — earnings blackout:*  {TICKER (next: YYYY-MM-DD), TICKER (next: YYYY-MM-DD)}  _(or "none")_
*Skipped — data failure:*  {TICKER, TICKER}  _(or "none")_

_⚠️ Research use only. Not financial advice. Never risk money you cannot afford to lose. Always confirm the signal before acting._
```

---

## Template B — Macro kill-switch fired (no analysis performed)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_{Exact reason — one of:_
_• VIX at {XX.X} — above kill-switch threshold of 30_
_• FOMC announcement day — signals suspended_
_• CPI release day — signals suspended_
_• Non-Farm Payrolls release day — signals suspended}_

_No ticker analysis was performed today. Resume tomorrow unless the condition persists._

_⚠️ Research use only. Not financial advice._
```

---

## Template C — All tickers checked, none reached 4/6 threshold (WAIT day)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ WAIT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
SPY futures: {+/-X.XX%}  |  QQQ futures: {+/-X.XX%}
VIX: {XX.X} ({rising / falling / stable})

No ticker in the universe reached the 4/6 confluence threshold today.

*Closest setups:*
• {TICKER}: {X}/6  ·  {one-line reason for the gap}
• {TICKER}: {X}/6  ·  {one-line reason for the gap}

*Excluded — earnings blackout:*  {TICKER (next: YYYY-MM-DD)}  _(or "none")_
*Skipped — data failure:*  {TICKER}  _(or "none")_

_⚠️ Research use only. Not financial advice._
```

---

## Placeholder reference

| Placeholder | What to fill |
|---|---|
| `{DATE}` | e.g. `Wed 28 May 2026` |
| `{TIME}` | e.g. `06:14` (24-hour, GMT) |
| `{TICKER}` | Exact ticker from `config/stocks.json` |
| `{ENTRY_LOW}` / `{ENTRY_HIGH}` | Hourly support level (low) and current pre-market price (high) |
| `{STOP_PRICE}` | Entry midpoint − (1.5 × ATR14) |
| `{STOP_PCT}` | `((entry − stop) / entry) × 100`, 1 decimal place |
| `{TARGET_PRICE}` | Entry midpoint + (3.0 × ATR14) |
| `{TARGET_PCT}` | `((target − entry) / entry) × 100`, 1 decimal place |
| `{RR_RATIO}` | `(target − entry) / (entry − stop)`, 1 decimal place |
| `{X}/6` | Count of confluence categories that confirmed BUY |
| `{REASON}` | Kill-switch trigger text |

---

## Rules for the Slack post

1. **One message per run** — never split into multiple posts.
2. **Never fabricate numbers** — if a value cannot be calculated from fetched data, write `N/A` rather than estimating.
3. **Never modify this template file** — it is source of truth; the routine reads it at runtime.
4. **Template A takes priority** — use it whenever at least one BUY signal fires, even if some tickers are excluded or failed.
5. **Template B takes priority over C** — if the macro kill-switch fires, use B and stop; never proceed to ticker analysis.
6. **"Also watching" max 3 tickers** — only include tickers that reached 3/6, ordered by confluence score descending.
