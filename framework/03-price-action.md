# 03 — Price Action

**Output:** support/resistance levels, candle pattern (if any), key event (breakout, retest, gap).

## Inputs
- Daily OHLC, last 90 sessions
- Weekly OHLC, last 52 weeks
- Pre-market gap data (today's pre-market range vs. yesterday's close)

## Checks

1. **Support / resistance**
   - Identify 3 nearest daily S/R levels above and below current price (pivot highs/lows from last 90d)
   - Identify 2 weekly S/R levels above and below
   - Confirm BUY when price is bouncing off support OR breaking out above a clean resistance with conviction

2. **Candle patterns (last 3 sessions)**
   - **Bullish:** engulfing, hammer, morning star, piercing line, bullish marubozu
   - **Bearish:** engulfing, shooting star, evening star, dark cloud cover, bearish marubozu
   - **Neutral / caution:** doji at extreme (top or bottom)
   - Pattern must form at a meaningful level (support, EMA50, prior breakout level) to count

3. **Fibonacci**
   - Compute fib retracement from the most recent meaningful swing (30–90 day window)
   - Note current price's position vs. 38.2%, 50%, 61.8% levels
   - Holding 38.2% or 50% on a pullback is a BUY confirm

4. **VWAP (daily session, intraday)**
   - Note today's pre-market VWAP relative to yesterday's close
   - Price reclaiming VWAP after a dip is a short-term bullish tell

5. **Gap analysis**
   - Pre-market gap up >2%: note for fill-probability discussion in output
   - Pre-market gap down >2%: caution — may be news-driven, double-check sentiment module

6. **Prior day/week levels**
   - PDH (prior day high), PDL (prior day low), PWH (prior week high), PWL (prior week low) are magnets
   - Price approaching PDH with momentum = potential breakout setup

## Contributes to confluence
Feeds the **Price Action** confluence category. Confirm BUY when at least ONE of:
- Bullish candle pattern at support/EMA50/fib level
- Breakout above identified resistance with no immediate higher resistance within 1 ATR
- Holding key fib (38.2% / 50%) on pullback in established uptrend
