# 04 — Volume

**Output:** `STRONG_CONFIRM`, `CONFIRM`, `NEUTRAL`, `WEAK`.

## Inputs
- Daily volume, last 30 sessions
- 20-day average volume
- Today's pre-market volume relative to typical pre-market

## Checks

1. **Volume relative to average**
   - Today's volume (or pre-market run-rate scaled) >150% of 20d avg = `STRONG_CONFIRM`
   - 100–150% = `CONFIRM`
   - 70–100% = `NEUTRAL`
   - <70% = `WEAK` — flag as potential fakeout if a move is in progress

2. **Volume on up-days vs. down-days (last 10 sessions)**
   - Sum volume on green candles vs. red candles
   - Up-day volume > down-day volume = institutional accumulation signal

3. **Breakout confirmation**
   - If price is breaking a key resistance, the breakout candle must have volume >120% of 20d avg
   - Low-volume breakouts → downgrade and flag as fakeout risk

4. **Dark pool / unusual options activity (best-effort)**
   - Use `web_search` for "{ticker} unusual options activity today" — only if quick and unambiguous
   - Surface any clearly reported large block prints in the output footer
   - Do NOT let this be the primary BUY rationale

## Contributes to confluence
Feeds the **Volume** confluence category. Confirm BUY when output is `CONFIRM` or `STRONG_CONFIRM`. `WEAK` is an automatic non-confirm regardless of other signals.
