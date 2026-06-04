# Claude Code Trading Signals Engine

BUY signal engine for 12 US large-caps, built on **Claude Code Routines** + **Alpha Vantage MCP** + **Slack**.

Inspired by the Simons / Renaissance approach: public data, multi-factor confluence scoring, statistical pattern recognition.

> ⚠️ **Research / educational use.** Nothing here is financial advice. Always size positions inside your own risk envelope and never risk money you can't afford to lose.

---

## How it works

```
                    ┌────────────────────────────────┐
                    │ Claude Code Routine (cloud)    │
                    │  • cron: 06:00 AM GMT daily    │
                    │  • clones this repo            │
                    │  • reads SKILL.md + framework/ │
                    └──────────────┬─────────────────┘
                                   │
                ┌──────────────────┼──────────────────┐
                ▼                  ▼                  ▼
        ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
        │ Alpha Vantage│   │ web_search   │   │ Slack MCP    │
        │ MCP (data)   │   │ (news/macro) │   │ (delivery)   │
        └──────────────┘   └──────────────┘   └──────────────┘
```

Every weekday at 06:00 AM GMT (signal window: 02:30–08:30 AM GMT), the routine:

1. Loads the 12-ticker universe from `config/stocks.json`
2. Skips any ticker within 3 trading days of earnings (checked live via Alpha Vantage `EARNINGS_CALENDAR`)
3. Pulls daily OHLCV, EMAs, RSI, MACD, BBANDS, ATR, VWAP, news sentiment for each remaining ticker
4. Scores each across the 6 confluence categories from `framework/`
5. Issues `BUY` for any ticker scoring ≥4/6 (subject to risk caps)
6. Posts the signal block to your configured Slack channel

---

## Quick start

See [`SETUP.md`](./SETUP.md) for the full walkthrough. TL;DR:

```bash
# 1. Fork this repo and clone locally
gh repo fork <your-org>/claude-trading-signals --clone

# 2. Edit config/stocks.json and config/runtime.json to your liking

# 3. In Claude (web or desktop):
#    Settings → Connectors → enable Alpha Vantage MCP and Slack
#
# 4. Go to claude.ai/code/routines → New routine
#    - Repository: this repo
#    - Connectors: Alpha Vantage, Slack
#    - Trigger: schedule, weekdays 06:00 AM GMT (Europe/London)
#    - Prompt: paste ROUTINE_PROMPT.md verbatim
```

---

## Repo layout

| Path | What it does |
|---|---|
| `ROUTINE_PROMPT.md` | Paste this into the routine's prompt field. Short on purpose — it delegates to `SKILL.md`. |
| `SKILL.md` | The operational playbook Claude follows. Loaded automatically by Claude Code. |
| `config/stocks.json` | 12-ticker universe. Edit to add/remove. |
| `config/runtime.json` | Risk caps, confluence thresholds, VIX kill-switch, ATR multipliers, signal window, earnings blackout rules. |
| `framework/` | The 40-50 point analysis framework broken into 10 modules. |
| `templates/slack-output.md` | The exact Slack message format. |
| `SETUP.md` | Step-by-step first-run guide. |

---

## Known issues vs. the original spec

Three things in the source brief needed updating — they're now handled in code, but worth knowing:

1. **Stock count:** Brief said "12 stocks" but listed 12. The repo uses all **12 tickers**.
2. **Earnings dates:** Hardcoded dates (AAPL Apr 30, NVDA May 20, CRWD Jun 9) were stale. Replaced with a live Alpha Vantage `EARNINGS_CALENDAR` lookup at runtime.
3. **Timezone:** Brief is GMT-centric. Cron schedule lives in `config/runtime.json` — set it to your wall-clock 02:00.

---

## License

MIT. Use at your own risk.
