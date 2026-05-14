# 01 — Trend Analysis

**Output:** one of `STRONG_BULL`, `BULL`, `NEUTRAL`, `BEAR`, `STRONG_BEAR` for the ticker.

## Inputs
- Daily closes (last 260 sessions)
- EMA 20, EMA 50, EMA 200 (daily)
- SPY and QQQ daily closes

## Checks

1. **EMA stack**
   - `STRONG_BULL` if `close > EMA20 > EMA50 > EMA200` and EMA200 slope (last 20d) is positive
   - `BULL` if `close > EMA50 > EMA200` but EMA20 still catching up
   - `NEUTRAL` if EMAs are tangled (any two within 0.5% of each other)
   - `BEAR` / `STRONG_BEAR` for the mirror conditions

2. **Structure**
   - Tag higher-high / higher-low sequence over the last 30 sessions. Two HH+HL pairs = uptrend confirmed.
   - Tag lower-high / lower-low for downtrend.

3. **Market alignment**
   - Note SPY and QQQ position vs. their own EMA50. If both are above, market tailwind is on. If both below, headwind.

## Contributes to confluence
This module feeds the **Trend** confluence category. Confirm BUY only when:
- Output ∈ {`BULL`, `STRONG_BULL`} AND
- HH/HL structure intact AND
- Market alignment is neutral or supportive (not both indices below their EMA50)
