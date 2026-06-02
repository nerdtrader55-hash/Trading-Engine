# Slack Output Templates

The routine sends ONE Slack message per run. Pick the template that matches the run outcome.

---

## Template A — Normal run with BUY signals

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{TICKER} : BUY
{TICKER} : BUY
{TICKER} : BUY

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

Populate only confirmed BUY signals (max 3). Omit lines for tickers that are WAIT.
If no tickers reach the 4/6 confluence threshold, use Template C instead.

**Substitutions:**
- `{DATE}` — today's date, e.g. `Mon 02 Jun 2026`
- `{TIME}` — time of run in GMT, e.g. `06:02`
- `{TICKER}` — exact ticker symbol from stocks.json (ARM / NVDA / META / GOOG / AMZN / UBER / AAPL / CRWD / GEV / V / SPGI / MCO)

---

## Template B — Kill-switch fired (no signals today)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛑 NO SIGNALS TODAY — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Kill-switch triggered: {REASON}
```

**Reasons:** `VIX above 30` / `FOMC announcement day` / `CPI release day` / `NFP release day`

---

## Template C — No signals (confluence not met)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 PRE-MARKET SIGNALS — {DATE} {TIME} GMT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No signals today — confluence threshold not met across 12 tickers.
```
