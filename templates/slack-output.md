# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.
Replace every `{PLACEHOLDER}` with the actual value computed during the run.
Use Slack mrkdwn syntax (asterisks for bold, backticks for mono, no HTML).

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
VIX `{VIX_LEVEL}` ({VIX_TREND}) | SPY {SPY_DIRECTION} | QQQ {QQQ_DIRECTION} | DXY `{DXY_LEVEL}` ({DXY_TREND})

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{SIGNAL_BLOCK}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{WATCHING_BLOCK}

{EXCLUDED_BLOCK}

⚠️ _Research signals only. Not financial advice. Max 1–2% account risk per trade. Always confirm before acting._
```

### {SIGNAL_BLOCK} — one entry per BUY signal (max 3)

For each HIGH confidence signal (score 5–6/6):
```
*{TICKER}* : BUY  🟢 HIGH CONFIDENCE — {SCORE}/6
Stop `{STOP_PRICE}` | Target `{TARGET_PRICE}` | R:R 1:{RR_RATIO}
{PATTERN_NOTE}
```

For each MEDIUM confidence signal (score 4/6):
```
*{TICKER}* : BUY  🟡 MEDIUM — {SCORE}/6 (reduce size)
Stop `{STOP_PRICE}` | Target `{TARGET_PRICE}` | R:R 1:{RR_RATIO}
{PATTERN_NOTE}
```

`{PATTERN_NOTE}` is the one-line historical-analog note from framework/10, e.g.:
`Bull flag after EMA50 touch — hit rate 3/3 last 12mo, avg +2.1% over 5d`
Omit this line entirely if no pattern qualifies.

### {WATCHING_BLOCK}

Omit if no tickers are close but below the signal threshold.
```
*ALSO WATCHING* (≤3 confluence — do not act yet)
{TICKER_A}, {TICKER_B}
```

### {EXCLUDED_BLOCK}

Omit if no tickers are excluded.
```
_Excluded (earnings blackout): {TICKER_C} (next: {EARNINGS_DATE}), {TICKER_D} (next: {EARNINGS_DATE})_
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

{DETAIL}

Next scheduled run: tomorrow pre-market.
⚠️ _Not financial advice._
```

`{REASON}` — one of:
- `VIX above threshold (current: {VIX_LEVEL}, limit: 30)`
- `FOMC announcement day`
- `CPI release day`
- `NFP (non-farm payrolls) release day`

`{DETAIL}` — one sentence of context, e.g.:
- `VIX at 32.4 signals elevated uncertainty. Standing aside until volatility normalises.`
- `Fed decision at 19:00 GMT. Markets typically whipsaw ±2% around FOMC. No pre-announcement edge.`
- `CPI print at 13:30 GMT. Inflation data creates binary risk — no directional signal valid pre-release.`

---

## Template C — No signals (thresholds not met, no kill-switch)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SCAN — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
VIX `{VIX_LEVEL}` ({VIX_TREND}) | SPY {SPY_DIRECTION} | QQQ {QQQ_DIRECTION} | DXY `{DXY_LEVEL}` ({DXY_TREND})

No tickers scored ≥4/6 confluence today. All 12 return *WAIT*.

{EXCLUDED_BLOCK}

⚠️ _Research signals only. Not financial advice._
```

---

## Placeholder reference

| Placeholder | Example value | Source |
|---|---|---|
| `{DATE}` | `Tue 10 Jun 2026` | System date in Europe/London |
| `{TIME}` | `06:02` | System time in Europe/London |
| `{VIX_LEVEL}` | `18.4` | Alpha Vantage `GLOBAL_QUOTE ^VIX` |
| `{VIX_TREND}` | `falling` / `rising` / `flat` | 5d direction |
| `{SPY_DIRECTION}` | `+0.3% pre-mkt` / `-0.4% pre-mkt` | web_search or AV futures |
| `{QQQ_DIRECTION}` | `+0.5% pre-mkt` | web_search or AV futures |
| `{DXY_LEVEL}` | `104.2` | Alpha Vantage `GLOBAL_QUOTE DX-Y.NYB` |
| `{DXY_TREND}` | `+0.2% 5d` / `-0.5% 5d` | 5d change |
| `{TICKER}` | `NVDA` | from stocks.json |
| `{SCORE}` | `5` | confluence count (0–6) |
| `{STOP_PRICE}` | `$127.40` | entry − (1.5 × ATR14) |
| `{TARGET_PRICE}` | `$142.60` | entry + (3.0 × ATR14) |
| `{RR_RATIO}` | `2.0` | target_distance / stop_distance |
| `{PATTERN_NOTE}` | see above | framework/10 output |
| `{REASON}` | `FOMC announcement day` | kill-switch trigger |
| `{DETAIL}` | see above | one contextual sentence |
| `{EARNINGS_DATE}` | `2026-06-18` | Alpha Vantage EARNINGS_CALENDAR |
