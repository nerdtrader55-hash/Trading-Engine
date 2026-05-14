# 02 — Momentum

**Output:** `BULLISH`, `NEUTRAL`, `BEARISH`, plus a divergence flag if present.

## Inputs
- RSI(14) daily — last 30 sessions
- MACD(12,26,9) daily — last 30 sessions
- Stochastic(14,3,3) daily — last 30 sessions (optional confirmation)

## Checks

1. **RSI band**
   - Sweet spot for BUY: **40–65** (trending up, not overbought)
   - Above 70: overbought — downgrade to NEUTRAL even if other signals fire
   - Below 30: oversold — possible reversal setup, but only counts as BUY confirm when paired with bullish MACD cross

2. **MACD**
   - Bullish cross in last 5 sessions = BUY confirm
   - MACD line above signal AND histogram rising = BUY confirm
   - Bearish cross or histogram falling = downgrade

3. **Divergence (high-value signal)**
   - Price makes lower low BUT RSI/MACD makes higher low → bullish divergence, set flag
   - Price makes higher high BUT RSI/MACD makes lower high → bearish divergence, set flag and downgrade

4. **Stochastic confirmation (optional)**
   - %K crossing %D from below in the 20–40 zone strengthens the BUY case

## Contributes to confluence
Feeds the **Momentum** confluence category. Confirm BUY when:
- RSI in 40–65 AND
- MACD bullish (cross in last 5 sessions, OR line above signal with rising histogram)
- Bearish divergence flag → automatic non-confirm even if the above pass
