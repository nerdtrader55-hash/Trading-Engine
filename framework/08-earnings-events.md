# 08 — Earnings & Events Calendar

**Output:** PASS / BLACKOUT for the ticker, plus next earnings date.

> Most of this is already handled in `SKILL.md` step 2. This file documents the rules and edge cases.

## Inputs
- Alpha Vantage `EARNINGS_CALENDAR` with `horizon=3month`
- `EARNINGS` function for historical earnings dates (useful for context)

## Rules

1. **Hard blackout window**
   - 3 trading days BEFORE next earnings → BLACKOUT
   - 1 trading day AFTER earnings → BLACKOUT (avoid post-earnings whipsaw)
   - Configurable in `config/runtime.json` under `earnings.blackout_trading_days_before` / `_after`

2. **What counts as a "trading day"**
   - Mon–Fri excluding US market holidays
   - Use NYSE calendar; when in doubt, web_search "NYSE holidays {year}"

3. **Post-earnings re-entry**
   - The day after the after-earnings blackout ends, the ticker is eligible again
   - First post-earnings signal needs +1 confluence point (raise bar to 5/6) for one trading day, to avoid catching gap reversals

4. **Edge cases**
   - Earnings preview/whisper number leaks → if `NEWS_SENTIMENT` flags an "EARNINGS_PREVIEW" topic in the 5 days before earnings, treat as already inside the blackout
   - Pre-announcement (guidance updates outside earnings cycle) → if news flags "GUIDANCE_REVISION", apply same blackout rules to the announcement date

5. **Recording**
   - When excluding a ticker for earnings, capture the date and put it in the Slack footer:
     `Excluded for earnings (next: YYYY-MM-DD): AAPL, NVDA, CRWD`

## Contributes to confluence
This is a **gate**, not a confluence vote. Tickers in blackout never get scored.
