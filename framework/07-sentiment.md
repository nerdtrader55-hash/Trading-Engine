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

4. **Options flow & Put/Call ratio (best-effort)**
   - Quick web_search for "{ticker} unusual options activity today"
   - Heavy call buying (put/call ratio < 0.7) = note in output, not primary rationale
   - Heavy put buying (put/call ratio > 1.3) = caution flag, downgrade sentiment one band

5. **CNN Fear & Greed Index (once per run, not per ticker)**
   - web_search "CNN Fear and Greed Index today"
   - Extreme Fear (0–24): market-wide risk-off — note in macro snapshot, raise confluence bar to 5/6
   - Fear (25–44): caution, but do not automatically block signals
   - Greed (55–74) / Extreme Greed (75–100): note in output as sentiment tailwind
   - This feeds the macro snapshot header in the Slack message; it does not count as a separate confluence vote

6. **Social media sentiment (best-effort)**
   - web_search "{ticker} Reddit WallStreetBets today" and "{ticker} trending Twitter site:twitter.com"
   - Flag if ticker is trending positively on retail forums — note in output as supporting colour
   - Flag if a negative Reddit/X thread is clearly gaining traction — downgrade sentiment one band
   - Do NOT let social media be the primary BUY rationale; it is supporting context only

## Contributes to confluence
Feeds the **Sentiment** confluence category. Confirm BUY when output ∈ {`POSITIVE`, `STRONG_POSITIVE`} AND no flagged negative headline keywords present AND put/call ratio is not signalling heavy put buying.
