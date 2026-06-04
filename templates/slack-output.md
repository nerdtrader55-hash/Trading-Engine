# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome. Never modify this file during a run.

---

## Template A — Normal run (with or without BUY signals)

Replace every `{PLACEHOLDER}` with real data before posting. Remove sections that don't apply (e.g. omit the BUY SIGNALS block if there are zero signals; omit the ALSO WATCHING block if all tickers are either signalled or excluded).

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE}  {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🌍 *MACRO SNAPSHOT*
• S&P / Nasdaq futures: {FUTURES_STATUS}
• VIX: {VIX_LEVEL} ({VIX_CHANGE})
• DXY: {DXY_LEVEL} ({DXY_5D_CHANGE})
• 10Y Yield: {YIELD_LEVEL} ({YIELD_5D_CHANGE})

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟢 BUY SIGNALS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY  〔{CONFIDENCE_LABEL} · {SCORE}/6〕
• Entry zone: ${ENTRY_LOW} – ${ENTRY_HIGH}
• Stop: ${STOP}  |  Target: ${TARGET}  |  R:R 1:{RR_RATIO}
• Pattern: {PATTERN_TAG}
• Confirms: {CONFLUENCE_SUMMARY}
• Analog: {HISTORICAL_ANALOG}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
👀 ALSO WATCHING
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
{WATCH_TICKER} — {WATCH_SCORE}/6  ({MISSING_CONFIRMS} more needed)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{EXCLUSIONS_FOOTER}

⚠️ _Signal engine only — not financial advice. Never risk more than 1–2% of account per trade. Past signals do not guarantee future results._
```

### Placeholder reference

| Placeholder | How to fill it |
|---|---|
| `{DATE}` | Today's date, e.g. `Wed 4 Jun 2026` |
| `{TIME}` | Current GMT time at moment of posting, e.g. `05:47` |
| `{FUTURES_STATUS}` | e.g. `ES +0.3% / NQ +0.5% (both green)` or `ES −0.2% / NQ flat (mixed)` |
| `{VIX_LEVEL}` | Current VIX, e.g. `17.4` |
| `{VIX_CHANGE}` | Direction + delta, e.g. `↓ −1.2 from yesterday` |
| `{DXY_LEVEL}` | DXY index, e.g. `104.3` |
| `{DXY_5D_CHANGE}` | 5-day % change, e.g. `+0.4% (5d)` |
| `{YIELD_LEVEL}` | 10Y Treasury yield, e.g. `4.31%` |
| `{YIELD_5D_CHANGE}` | 5-day bps change, e.g. `+8 bps (5d)` |
| `{TICKER}` | Exact ticker symbol, e.g. `NVDA` |
| `{CONFIDENCE_LABEL}` | `HIGH` (5–6/6) or `MEDIUM` (4/6) — smaller size for MEDIUM |
| `{SCORE}` | Integer 4–6, e.g. `5` |
| `{ENTRY_LOW}` | Lower bound of entry zone (nearest hourly support) |
| `{ENTRY_HIGH}` | Upper bound of entry zone (current pre-market price or last close) |
| `{STOP}` | entry − (1.5 × ATR14), rounded to 2 dp |
| `{TARGET}` | entry + (3.0 × ATR14), rounded to 2 dp |
| `{RR_RATIO}` | Numeric ratio to 1 dp, e.g. `2.0` |
| `{PATTERN_TAG}` | e.g. `bull flag`, `50d EMA touch in uptrend`, `ascending triangle breakout` |
| `{CONFLUENCE_SUMMARY}` | Comma-separated list of confirmed categories, e.g. `Trend ✓ Momentum ✓ Volume ✓ Price Action ✓ Macro ✓` |
| `{HISTORICAL_ANALOG}` | e.g. `Bull flag on NVDA: 3/3 hits last 12 mo, avg +2.8% over 5d` — omit if <3 prior occurrences |
| `{WATCH_TICKER}` | Ticker that scored 3/6 (not enough to signal, but close) |
| `{WATCH_SCORE}` | e.g. `3` |
| `{MISSING_CONFIRMS}` | `1` or `2` |
| `{EXCLUSIONS_FOOTER}` | e.g. `Excluded for earnings (next: 2026-06-09): CRWD` — omit line if none |

### Confidence labels

| Score | Label | Position sizing note |
|---|---|---|
| 5 or 6 / 6 | `HIGH` | Full size per risk rules |
| 4 / 6 | `MEDIUM` | Reduce size by ~50% |
| ≤ 3 / 6 | — | No signal; ticker may appear in ALSO WATCHING |

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE}  {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: *{REASON}*

_Next scheduled run: tomorrow pre-market._
```

Kill-switch reasons to use verbatim:
- `VIX above 30 (current: {VIX_LEVEL}) — all signals suspended`
- `FOMC announcement day — no signals on Fed days`
- `CPI release day — extreme volatility expected, standing aside`
- `NFP (non-farm payrolls) release day — extreme volatility expected, standing aside`

---

## Formatting rules

- Use Slack-flavoured markdown: `*bold*`, `_italic_`, `` `code` ``
- No HTML tags
- No extra blank lines inside signal blocks
- Separator line `━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━` must appear exactly as shown
- One Slack message per run — do not split into multiple posts
- Never include fabricated prices or levels; if data is unavailable, omit that field and note in the footer
