# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome. Render using Slack markdown (asterisks for bold, backticks for code). No HTML tags.

---

## Template A — Normal run (one or more BUY signals)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY  [{CONFIDENCE}]
{TICKER} : BUY  [{CONFIDENCE}]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

👀 *Also watching (close calls):* {ALSO_WATCHING_LIST}

📋 *Skipped (earnings blackout):* {EARNINGS_BLACKOUT_LIST}

⚠️ _Signals are not financial advice. Always manage your own risk. Never risk more than 1–2% of account per trade._
```

**Substitution rules:**
- `{DATE}` — today's date in `DD-MMM-YYYY` format (e.g. `04-Jun-2026`)
- `{TIME}` — signal generation time in `HH:MM` GMT (e.g. `06:15`)
- `{TICKER}` — exact symbol from `config/stocks.json`
- `{CONFIDENCE}` — `HIGH` (5–6/6) or `MEDIUM` (4/6)
- `{ALSO_WATCHING_LIST}` — comma-separated tickers that scored 3/6 (close but not triggered). Write `none` if empty.
- `{EARNINGS_BLACKOUT_LIST}` — comma-separated tickers excluded for earnings proximity. Write `none` if empty.
- Repeat the `{TICKER} : BUY  [{CONFIDENCE}]` line for each signal, up to 3 maximum.
- If zero signals after filtering, use Template C (no signals, no kill-switch) instead.

---

## Template B — Kill-switch fired (macro event blocks all signals)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Resume normal analysis next trading day._
```

**Substitution rules:**
- `{REASON}` — plain-English reason (e.g. `VIX at 32 — above 30 threshold`, `FOMC rate decision today`, `US CPI release today`, `NFP release today`)

---

## Template C — Clean run, no signals (no kill-switch, just no qualifying setups)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

*WAIT* — No setups met the 4/6 confluence threshold today.

👀 *Also watching:* {ALSO_WATCHING_LIST}

📋 *Skipped (earnings blackout):* {EARNINGS_BLACKOUT_LIST}

⚠️ _Patience is a position. Resume analysis tomorrow._
```
