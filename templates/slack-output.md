# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run (one or more BUY signals)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER_1} : BUY   [{CONFIDENCE_1}]
{TICKER_2} : BUY   [{CONFIDENCE_2}]
{TICKER_3} : BUY   [{CONFIDENCE_3}]

*Also watching (fell short of signal threshold):*
{ALSO_WATCHING_LIST}

*Excluded — earnings blackout:*
{EXCLUDED_LIST}

*Macro snapshot:* SPY {SPY_DIRECTION} · QQQ {QQQ_DIRECTION} · VIX {VIX_LEVEL} · DXY {DXY_NOTE}

⚠️ _Not financial advice. Size within your own risk envelope (max 1–2% per trade). Max 3 positions open at once._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Field notes:**
- `{CONFIDENCE}` = `HIGH (5–6/6)` or `MEDIUM (4/6)` — never omit
- List only signals that scored ≥ 4/6 AND passed the R:R gate; omit the field entirely if fewer than 3 fire
- `{ALSO_WATCHING_LIST}` = tickers that scored exactly 3/6, comma-separated; write `none` if empty
- `{EXCLUDED_LIST}` = tickers in earnings blackout window; write `none` if empty
- `{SPY_DIRECTION}` / `{QQQ_DIRECTION}` = `▲ green` or `▼ red` or `— flat`
- `{VIX_LEVEL}` = numeric value, e.g. `17.4`
- `{DXY_NOTE}` = brief note, e.g. `strong (+0.6% 5d)` or `stable`

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Reason detail: {DETAIL}_

_Next signal run: next trading day pre-market._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**`{REASON}` values** (pick the first that applies):
- `VIX above kill-switch threshold ({VIX_LEVEL} > {VIX_MAX})`
- `FOMC announcement day — no signals on Fed decision days`
- `CPI release day — extreme volatility expected`
- `Non-Farm Payrolls day — extreme volatility expected`

---

## Template C — All tickers scored WAIT (no kill-switch, no signals)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No setup reached the ≥{MIN_CONFLUENCE}/6 confluence threshold today.

*Nearest misses (3/6):*
{ALSO_WATCHING_LIST}

*Excluded — earnings blackout:*
{EXCLUDED_LIST}

*Macro snapshot:* SPY {SPY_DIRECTION} · QQQ {QQQ_DIRECTION} · VIX {VIX_LEVEL}

_Check back tomorrow._
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
