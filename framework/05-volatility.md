# 05 — Volatility

**Output:** volatility regime (`SQUEEZE`, `NORMAL`, `EXPANDING`, `EXTREME`), plus ATR-based stop and target levels.

## Inputs
- Bollinger Bands (20, 2σ) daily, last 30 sessions
- ATR(14) daily, last 30 sessions
- VIX current level (already pulled in macro)

## Checks

1. **Bollinger Band width**
   - Compute current BB width = (upper − lower) / middle
   - Compare to the 30-day average BB width
   - <70% of 30d avg = `SQUEEZE` (big move coming — direction TBD by other modules)
   - 70–130% = `NORMAL`
   - >130% = `EXPANDING`

2. **Position in band**
   - Price riding the upper band in an uptrend = strong trend, not a sell signal (don't confuse with overbought)
   - Price walking the lower band in a downtrend = strong downtrend, AVOID BUYS
   - Reverting to middle band after squeeze = good entry zone when momentum aligns

3. **ATR-based risk levels**
   - **Stop:** entry price − (1.5 × ATR14)
   - **Target:** entry price + (3.0 × ATR14)
   - These feed directly into the risk gate (R:R ≥ 2:1 required)

4. **VIX context**
   - VIX <15: complacency — BUYs work but use tighter stops
   - VIX 15–20: normal — full ATR-based stops fine
   - VIX 20–25: caution — only HIGH-confidence signals (5–6 confluence)
   - VIX 25–30: tighten further, position sizing note
   - VIX >30: macro kill-switch fires, no signals (handled in SKILL.md step 1)

## Contributes to confluence
Volatility doesn't directly confirm or deny — it **shapes** the trade. But it can disqualify:
- `EXTREME` volatility regime (BB width >2x avg AND ATR >2x 90d avg) → automatic WAIT
- Walking the lower band in confirmed downtrend → automatic WAIT
