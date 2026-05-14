# 09 — Multi-Timeframe Confirmation

**Output:** weekly trend agrees / disagrees with daily; refined entry zone from hourly.

## Inputs
- Weekly candles (last 52 weeks)
- Hourly candles (last 5 trading days)

## Rules

1. **Weekly chart — broader trend check**
   - Compute weekly EMA10 and EMA40
   - Bullish: weekly price > weekly EMA10 > weekly EMA40
   - If weekly is bearish but daily is bullish → counter-trend setup, raise confluence bar to 5/6 to issue BUY
   - If weekly is bullish and daily is bullish → with-trend, normal 4/6 threshold

2. **Hourly chart — entry refinement only**
   - Hourly chart is NEVER used to generate or veto the signal — only to refine the price level
   - Find the most recent hourly support level within 2 ATR (daily) of current price
   - Use that level as the lower bound of the "entry zone" in the Slack output
   - Upper bound of entry zone = current pre-market price (or last close if pre-market is flat)

3. **Output fields fed downstream**
   - `entry_zone_low`, `entry_zone_high`
   - `weekly_trend`: bull / bear / neutral
   - Counter-trend flag if applicable

## Contributes to confluence
Multi-timeframe doesn't add a confluence vote — it **adjusts the bar**:
- With-trend (weekly bull + daily bull) → 4/6 confluence threshold (default)
- Counter-trend (weekly bear + daily bull) → 5/6 confluence threshold
- Counter-counter-trend (weekly bear + daily bear + reversal setup) → NO BUY allowed
