# Setup Guide

Step-by-step. ~15 minutes if all your accounts are already linked.

## Prerequisites

- A Claude plan with **Claude Code on the web** enabled (Pro, Max, Team, or Enterprise — the free tier does not have routines)
- A GitHub account (Claude Code clones from GitHub)
- A Slack workspace where you can install apps and you have permission to post to a channel
- An Alpha Vantage API key (free tier works for testing; paid tier recommended for production — free tier is 25 calls/day, you'll need ~200/day for 12 tickers)

---

## Step 1 — Get this repo onto GitHub

```bash
# Option A: fork via gh CLI
gh repo create your-username/claude-trading-signals --public --clone
cd claude-trading-signals
# copy the files from this bundle into the directory
git add .
git commit -m "Initial signal engine"
git push origin main

# Option B: create empty repo on github.com, then push files manually
```

The repo can be private — Claude Code routines just need read access.

---

## Step 2 — Connect Alpha Vantage MCP to Claude

1. Go to **claude.ai → Settings → Connectors**
2. Search for "Alpha Vantage" → click **Connect**
3. The MCP URL is `https://mcp.alphavantage.co/mcp`
4. When prompted, paste your Alpha Vantage API key
5. Verify it's working: in a normal Claude chat, ask "use Alpha Vantage to get the latest quote for NVDA" — you should see a tool call.

---

## Step 3 — Connect Slack

1. **claude.ai → Settings → Connectors → Slack → Connect**
2. Authorize Claude in your Slack workspace
3. Decide which channel will receive signals. Recommended: create a private channel `#trading-signals` (just you, or you + trusted collaborators)
4. Invite the Claude bot to that channel: in Slack, `/invite @Claude`
5. Get the channel ID:
   - Click the channel name → **View channel details** → scroll to bottom → copy the **Channel ID** (looks like `C0A1B2C3D4`)
6. Open `config/runtime.json` in your fork, set:
   ```json
   "slack_channel_id": "C0A1B2C3D4"
   ```
   Commit + push.

---

## Step 4 — Connect GitHub to Claude Code

1. **claude.ai → Settings → GitHub** (or run `/web-setup` in the CLI) and authorize
2. Grant access to the trading-signals repo

---

## Step 5 — Create the routine

### Via web

1. Go to **claude.ai/code/routines**
2. Click **New routine** → choose **Remote**
3. Fill in:
   - **Name:** `trading signals`
   - **Repository:** `your-username/claude-trading-signals`
   - **Connectors:** check **Alpha Vantage** and **Slack**
   - **Trigger:** **Schedule** → **Weekdays** → **02:00** in your timezone
   - **Prompt:** paste the fenced block from [`ROUTINE_PROMPT.md`](./ROUTINE_PROMPT.md)
4. Click **Create**

### Via CLI

```
/schedule
```
…and follow the prompts. Paste the same prompt and pick the same trigger.

---

## Step 6 — Test it

Don't wait for tomorrow morning. Run it once manually:

1. On the routine's detail page, click **Run now**
2. Watch the session — it should clone the repo, read `SKILL.md`, call Alpha Vantage tools, then post to Slack
3. If something fails, the session log will show exactly where. Common issues:
   - **Alpha Vantage rate-limited:** free tier is 25/day. Upgrade for production, or run only a subset of tickers per day during testing.
   - **Slack channel not found:** Claude bot wasn't invited to the channel, or `slack_channel_id` is wrong.
   - **EARNINGS_CALENDAR returns CSV not JSON:** that's how Alpha Vantage returns this endpoint — the routine handles it, but if you've forked and changed the SKILL, you may need to re-parse.

---

## Step 7 — Iterate

The framework files in `framework/` are designed to be edited. If you find a module is too noisy or too strict, tune it and push. The next routine run will pick up the change — no need to recreate the routine.

Useful knobs in `config/runtime.json`:

- `confluence.min_categories_for_buy` — raise to 5 to be stricter
- `risk.min_rr` — raise to 2.5 or 3.0 to filter for fatter setups
- `macro_kill_switches.vix_max` — lower to 25 for more risk-off
- `confluence.max_signals_per_run` — lower to 1 or 2 for higher-conviction-only days

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| Routine runs but no Slack message | Bot not in channel, or `slack_channel_id` wrong | Re-invite `@Claude` to the channel; check channel ID |
| "ALPHA_VANTAGE_RATE_LIMIT" in session log | Free tier 25/day exceeded | Upgrade plan or reduce ticker count |
| Routine fires at wrong time | Schedule TZ vs `runtime.json` TZ mismatch | The schedule TZ is the one configured in claude.ai/code/routines; `runtime.json` TZ only controls date-stamps in the message |
| All signals always WAIT | Confluence threshold too strict, or VIX kill-switch firing | Lower `min_categories_for_buy` to 4, check VIX |
| Signals fire on FOMC day anyway | `web_search` for FOMC calendar didn't match | Add a manual override in `config/runtime.json` (future feature — for now, pause the routine on known FOMC days) |
| Costs spiraling | Each routine run is a full Claude Code session — they count against your daily run cap and usage limits | Reduce frequency (maybe weekdays only is enough), or trim the framework to fewer Alpha Vantage calls per ticker |

---

## What happens if you hit Claude Code daily run limits

Pro: 5 routine runs/day. Max: 15. Team/Enterprise: 25. If you run this routine 5 days/week, you'll use 5 runs/week — well within any tier. If you also have other routines (e.g. an end-of-day review), budget accordingly.

Source on limits: [Anthropic's routines announcement](https://claude.com/blog/introducing-routines-in-claude-code).
