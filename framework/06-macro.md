# 06 — Macro & Market Conditions

**Output:** macro snapshot block (used in Slack header) + a single confluence verdict (`SUPPORTIVE`, `NEUTRAL`, `HEADWIND`).

## Inputs
- SPY and QQQ pre-market futures direction (use web_search for "SPY futures premarket today" / "QQQ futures premarket today" or Alpha Vantage `GLOBAL_QUOTE` on `ES=F` / `NQ=F`)
- VIX current level
- DXY (dollar index) current level + 5-day change
- 10-year Treasury yield + 5-day change
- Today's US economic calendar (web_search "US economic calendar today")

## Checks

1. **Futures direction**
   - Both SPY and QQQ futures green pre-market = supportive
   - Mixed = neutral
   - Both red = headwind, raise confluence bar to 5/6 for any BUY

2. **VIX trend**
   - VIX falling AND <20 = supportive
   - VIX rising even if <20 = caution
   - VIX >20 = headwind

3. **Dollar (DXY)**
   - Strong dollar (DXY up >0.5% in last 5d) hurts multinationals (AAPL, GOOG, META) — note in output
   - Weak dollar is supportive for big tech with international revenue

4. **Rates (10Y yield)**
   - Yields spiking up >15bps in a week = headwind for growth (NVDA, ARM, CRWD, AMZN, META)
   - Yields falling = tailwind for growth

5. **Economic calendar**
   - FOMC day → kill-switch (handled in SKILL.md step 1)
   - CPI/PCE/jobs day → kill-switch
   - Fed speakers on schedule → caution flag, not a kill switch
   - Major retail earnings (WMT, COST) before open → caution for consumer names (AMZN)

## Contributes to confluence
Feeds the **Macro** confluence category. Confirm BUY when:
- Futures both green or mixed (not both red) AND
- VIX <20 AND
- No major scheduled event today other than minor data
