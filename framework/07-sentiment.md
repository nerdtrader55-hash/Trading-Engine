# 07 — Sentiment & News

**Output:** sentiment score (`STRONG_POSITIVE`, `POSITIVE`, `NEUTRAL`, `NEGATIVE`, `STRONG_NEGATIVE`) + headline list (max 3).

## Inputs
- Alpha Vantage `NEWS_SENTIMENT` for the ticker (last 24h)
- Quick `web_search` for "{ticker} news today" (only if AV returns nothing useful)

## Checks

1. **Alpha Vantage sentiment score**
   - The MCP returns a `overall_sentiment_score` per article and an aggregated `ticker_sentiment_score` per ticker
   - Map:
     - ≥ 0.35 → `STRONG_POSITIVE`
     - 0.15 to 0.35 → `POSITIVE`
     - −0.15 to 0.15 → `NEUTRAL`
     - −0.35 to −0.15 → `NEGATIVE`
     - ≤ −0.35 → `STRONG_NEGATIVE`

2. **Analyst actions**
   - Note any upgrades/downgrades in the news feed (24h window)
   - Upgrades from major banks (Morgan Stanley, Goldman, JPM, Bank of America, Wells Fargo, Citi) carry extra weight

3. **Headline sanity check**
   - Surface up to 3 most-relevant headlines in the output
   - If any headline contains keywords like "investigation", "lawsuit", "guidance cut", "miss", "recall", "fraud", "halted" → flag and downgrade sentiment by one band

4. **CNN Fear & Greed Index (once per run, not per ticker)**
   - Use `web_search` for "CNN Fear and Greed Index today" to get the current reading
   - Extreme Fear (<25): caution — market breadth is poor; raise confluence bar to 5/6 for any BUY
   - Fear (25–45): neutral macro sentiment backdrop
   - Greed (55–75): supportive for momentum longs
   - Extreme Greed (>75): caution — mean-reversion risk; note in output but do not block signals
   - Record the reading in the Slack footer (e.g. "CNN F&G: 62 — Greed")

5. **Options flow & put/call ratio (best-effort)**
   - Quick web_search for "{ticker} unusual options activity"
   - Heavy call buying = note in output, not primary rationale
   - Heavy put buying = caution flag
   - web_search for "CBOE equity put/call ratio today" — ratio <0.7 = bullish sentiment, >1.0 = bearish sentiment; note in Slack footer

## Contributes to confluence
Feeds the **Sentiment** confluence category. Confirm BUY when output ∈ {`POSITIVE`, `STRONG_POSITIVE`} AND no flagged negative headline keywords present.
