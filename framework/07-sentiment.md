# 07 — Sentiment & News

**Output:** sentiment score (`STRONG_POSITIVE`, `POSITIVE`, `NEUTRAL`, `NEGATIVE`, `STRONG_NEGATIVE`) + headline list (max 3).

## Inputs
- Alpha Vantage `NEWS_SENTIMENT` for the ticker (last 24h)
- Quick `web_search` for "{ticker} news today" (only if AV returns nothing useful)
- `web_search` for "CNN Fear Greed Index today" — market-wide sentiment gauge
- `web_search` for "{ticker} unusual options activity" — put/call flow
- `web_search` for "{ticker} reddit OR twitter sentiment today" — social media pulse

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

4. **Options flow / put-call ratio (best-effort)**
   - `web_search` for "{ticker} unusual options activity"
   - Heavy call buying (put/call ratio < 0.7) = note in output, strengthens BUY case
   - Heavy put buying (put/call ratio > 1.3) = caution flag, weakens BUY case
   - Do not let this be the sole primary rationale

5. **CNN Fear & Greed Index (market-wide, once per run)**
   - `web_search` for "CNN Fear Greed Index today"
   - Extreme Greed (>75): caution — market may be overextended, raise confluence bar by 1 for all tickers
   - Greed (55–75): supportive
   - Neutral (45–55): no adjustment
   - Fear (25–45): caution — prefer higher confluence before signalling
   - Extreme Fear (<25): very cautious — require HIGH confidence (5–6/6) for any BUY

6. **Social media sentiment spike (best-effort)**
   - `web_search` for "{ticker} reddit site:reddit.com/r/wallstreetbets OR site:reddit.com/r/stocks"
   - `web_search` for "{ticker} twitter sentiment today"
   - Sudden spike in positive mentions (e.g. trending on WSB with positive tone) = note in output
   - Flag but do NOT let social media alone drive or kill a signal — treat as weak supporting evidence only

## Contributes to confluence
Feeds the **Sentiment** confluence category. Confirm BUY when output ∈ {`POSITIVE`, `STRONG_POSITIVE`} AND no flagged negative headline keywords present.

CNN Fear & Greed adjustment is applied *after* scoring — it can raise the effective min_confluence required, but it does not change the 6-category vote for individual tickers.
