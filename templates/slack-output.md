# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.
Never change the structure of these templates during a run — substitute placeholders only.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNAL REPORT — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🌍 MACRO SNAPSHOT
• SPY futures: {SPY_FUTURES_DIRECTION} ({SPY_FUTURES_PCT}%)
• QQQ futures: {QQQ_FUTURES_DIRECTION} ({QQQ_FUTURES_PCT}%)
• VIX: {VIX_LEVEL} ({VIX_CHANGE}) — {VIX_REGIME}
• DXY: {DXY_LEVEL} ({DXY_5D_CHANGE}% / 5d)
• 10Y yield: {YIELD_10Y}% ({YIELD_CHANGE}bps / 5d)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟢 SIGNALS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER_1} : BUY  [{CONFIDENCE_1} — {SCORE_1}/6]
  Entry zone : {ENTRY_LOW_1} – {ENTRY_HIGH_1}
  Stop       : {STOP_1}  (-{STOP_PCT_1}% / 1.5×ATR)
  Target     : {TARGET_1}  (+{TARGET_PCT_1}% / 3×ATR)
  R:R        : 1:{RR_1}
  Confluence : {TREND_1} | {MOMENTUM_1} | {VOLUME_1} | {PRICE_ACTION_1} | {MACRO_1} | {SENTIMENT_1}
  Edge       : {PATTERN_NOTE_1}

{TICKER_2} : BUY  [{CONFIDENCE_2} — {SCORE_2}/6]
  Entry zone : {ENTRY_LOW_2} – {ENTRY_HIGH_2}
  Stop       : {STOP_2}  (-{STOP_PCT_2}% / 1.5×ATR)
  Target     : {TARGET_2}  (+{TARGET_PCT_2}% / 3×ATR)
  R:R        : 1:{RR_2}
  Confluence : {TREND_2} | {MOMENTUM_2} | {VOLUME_2} | {PRICE_ACTION_2} | {MACRO_2} | {SENTIMENT_2}
  Edge       : {PATTERN_NOTE_2}

{TICKER_3} : BUY  [{CONFIDENCE_3} — {SCORE_3}/6]
  Entry zone : {ENTRY_LOW_3} – {ENTRY_HIGH_3}
  Stop       : {STOP_3}  (-{STOP_PCT_3}% / 1.5×ATR)
  Target     : {TARGET_3}  (+{TARGET_PCT_3}% / 3×ATR)
  R:R        : 1:{RR_3}
  Confluence : {TREND_3} | {MOMENTUM_3} | {VOLUME_3} | {PRICE_ACTION_3} | {MACRO_3} | {SENTIMENT_3}
  Edge       : {PATTERN_NOTE_3}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
👁 ALSO WATCHING  (scored 3/6 — below threshold)
  {WATCHING_LIST}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚫 EXCLUDED
  {EXCLUDED_EARNINGS}   ← earnings blackout (next date shown)
  {EXCLUDED_DATA}       ← data unavailable

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Not financial advice. Size positions within your own risk envelope.
   Max risk per trade: 1–2% of account. Max 3 concurrent positions.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Rendering notes
- Omit signal blocks for TICKER_2 and TICKER_3 if fewer than 2 or 3 signals fired.
- If only 1 signal, remove the second and third signal blocks entirely — do not show empty blocks.
- For ALSO WATCHING: list tickers only, e.g. `AAPL (3/6), META (3/6)`. Omit if none.
- For EXCLUDED: list as `NVDA (earnings {DATE})`. Omit row if the list is empty.
- Confluence row format: each cell is ✅ (confirms BUY) or ❌ (does not confirm). Order: Trend | Momentum | Volume | Price Action | Macro | Sentiment.
- CONFIDENCE label: `HIGH` for 5–6/6, `MEDIUM` for 4/6.
- {VIX_REGIME}: `LOW` if <15, `NORMAL` if 15–20, `ELEVATED` if 20–25, `HIGH` if 25–30.

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

{KILL_SWITCH_DETAIL}

Next scheduled run: tomorrow pre-market.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Not financial advice.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Kill-switch REASON values
- `VIX > {VIX_MAX} (current: {VIX_LEVEL})` — extreme fear, all signals paused
- `FOMC announcement day` — Federal Reserve rate decision scheduled today
- `CPI release day` — US Consumer Price Index published today
- `NFP release day` — US Non-Farm Payrolls published today

### KILL_SWITCH_DETAIL
One sentence of context, e.g.:
- "VIX at 34.2 signals extreme market fear. All signals suspended until VIX drops below 30."
- "The Fed announces its rate decision today at 14:00 ET. Signals resume tomorrow."

---

## Template C — No signals (all tickers WAIT, no kill-switch)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏸ NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No ticker reached the 4/6 confluence threshold today.

🌍 Macro: VIX {VIX_LEVEL} | SPY futures {SPY_FUTURES_DIRECTION} | QQQ futures {QQQ_FUTURES_DIRECTION}

👁 CLOSEST SETUPS  (highest scorers, all below threshold)
  {WATCHING_LIST}

🚫 EXCLUDED
  {EXCLUDED_EARNINGS}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Not financial advice.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
