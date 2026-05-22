# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
VIX: {VIX_LEVEL} | SPY futures: {SPY_FUTURES} | QQQ futures: {QQQ_FUTURES}
DXY: {DXY_LEVEL} | 10Y yield: {YIELD_LEVEL}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*SIGNALS*

{TICKER} : BUY   [{CONFIDENCE} — {N}/6 confluence]
  Entry zone: {ENTRY_LOW}–{ENTRY_HIGH}
  Stop: {STOP} ({STOP_ATR_MULT}× ATR14)
  Target: {TARGET} ({TARGET_ATR_MULT}× ATR14)  R:R {RR}
  Key confirms: {CONFIRM_LIST}
  Pattern: {PATTERN_NOTE}

[repeat block per signal, up to 3]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
*ALSO WATCHING* (3/6 — not enough confluence today)
{WATCHING_LIST}

*EXCLUDED* (earnings blackout)
{EXCLUDED_LIST}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Research only. Not financial advice. Size within your risk envelope. Max 1–2% account risk per trade.
```

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

VIX: {VIX_LEVEL} | SPY futures: {SPY_FUTURES} | QQQ futures: {QQQ_FUTURES}

Resume normal analysis tomorrow unless the condition persists.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Template C — No signals (kill-switch did NOT fire, but no ticker scored ≥4/6)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*MACRO SNAPSHOT*
VIX: {VIX_LEVEL} | SPY futures: {SPY_FUTURES} | QQQ futures: {QQQ_FUTURES}

No ticker reached the 4/6 confluence threshold today.

*BEST SETUPS DEVELOPING* (highest scorers, not yet actionable)
{WATCHING_LIST}

*EXCLUDED* (earnings blackout)
{EXCLUDED_LIST}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Field reference

| Placeholder | What to fill in |
|---|---|
| `{DATE}` | e.g. `Thursday 22 May 2026` |
| `{TIME}` | e.g. `06:15` |
| `{VIX_LEVEL}` | e.g. `18.4 ↓` |
| `{SPY_FUTURES}` | e.g. `+0.3%` |
| `{QQQ_FUTURES}` | e.g. `+0.5%` |
| `{DXY_LEVEL}` | e.g. `104.2 →` |
| `{YIELD_LEVEL}` | e.g. `4.41% ↑` |
| `{TICKER}` | e.g. `NVDA` |
| `{CONFIDENCE}` | `HIGH` (5–6/6) or `MEDIUM` (4/6) |
| `{N}` | confluence score (4, 5, or 6) |
| `{ENTRY_LOW}` / `{ENTRY_HIGH}` | entry zone from module 09 |
| `{STOP}` / `{TARGET}` | ATR-based levels from module 05 |
| `{STOP_ATR_MULT}` | from `runtime.risk.stop_atr_multiplier` |
| `{TARGET_ATR_MULT}` | from `runtime.risk.target_atr_multiplier` |
| `{RR}` | e.g. `1:3.0` |
| `{CONFIRM_LIST}` | comma-separated categories that confirmed |
| `{PATTERN_NOTE}` | from module 10 historical analog (omit if <3 prior occurrences) |
| `{WATCHING_LIST}` | tickers that scored 3/6 — comma-separated with score |
| `{EXCLUDED_LIST}` | e.g. `AAPL (next earnings: 2026-07-31), NVDA (next: 2026-08-20)` |
| `{REASON}` | kill-switch reason, e.g. `VIX 32.1 — above 30 threshold` |
