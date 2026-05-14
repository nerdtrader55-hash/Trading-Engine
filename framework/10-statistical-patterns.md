# 10 — Statistical Pattern Recognition (Simons-style)

**Output:** pattern tags (0+ per ticker) and historical-analog note for the Slack message.

> This is the "edge" layer. It doesn't add or remove confluence votes by itself, but it surfaces high-information context that can break ties and shape position-sizing notes.

## Inputs
- Daily OHLCV, last 260 sessions (1 year)
- Sector peers' daily OHLCV (from `config/stocks.json` sector field)

## Patterns to check

1. **Bullish base patterns**
   - Cup-and-handle (12–26 week base, handle <15% of cup depth)
   - Flat base (5+ weeks, <15% depth)
   - Ascending triangle (rising lows, flat highs, 5+ weeks)
   - Bull flag (sharp move + tight consolidation 5–15 sessions)

2. **Reversal patterns**
   - Double bottom (two lows within 5% of each other, separated by 4+ weeks)
   - Inverse head and shoulders (3 troughs, middle deepest, clean neckline)

3. **Continuation signals**
   - 50-day MA touch in established uptrend (price within 1 ATR of EMA50 in last 3 sessions)
   - Higher-low into rising EMA20 (bounce off the line)

## Correlation & sector rotation

1. **Pairwise correlation (60-day)**
   - Compute return correlation between this ticker and the other 11 in the universe
   - Flag the 3 most-correlated and 3 least-correlated tickers
   - If 2+ of the most-correlated peers also score 4+/6 today, the signal is reinforced (note this)

2. **Sector rotation**
   - Compute 5-day and 20-day relative strength (vs. SPY) for each sector represented in `stocks.json`
   - If the ticker's sector is the top-2 strongest sectors over 5d AND was bottom-half 20d ago → emerging rotation, BUY case strengthened
   - If the ticker's sector is dropping in 5d relative strength → caution flag

## Anomaly detection

Look for "this is unusual" tells from the last 20 sessions:
- Volume spike >3× 20d average with no clear news → flag for "smart-money positioning?" note
- Tight range (daily range < 0.5× ATR14) for 3+ sessions while broader market moves >1% → coiling for breakout
- Reversal at a level the stock has touched and rejected 3+ times in past 90d → significance increases

## Historical analog

When the current setup matches a known winning pattern (e.g. "bull flag after EMA50 touch in uptrend"):
- Look at the last 3 occurrences of this same pattern on the same ticker in the past 12 months
- Compute hit rate (closed higher 5d later) and average 5d return
- Include in Slack note: "Pattern hit rate on AAPL last 12mo: 3/3, avg +2.1% over 5d"

If <3 prior occurrences in the lookback, omit the analog (sample too small).

## Contributes to confluence
Does NOT add confluence votes. Instead:
- Can break ties when more than `max_signals_per_run` tickers qualify (top by confluence score, then by pattern-tag count, then by analog hit rate)
- Adds the historical-analog and sector-rotation notes to the Slack message body
