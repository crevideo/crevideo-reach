# TikTok Shop Affiliate Outreach & Marketing Skills Pack (Claude Plugin)

> **Crevideo Reach** — turn Claude (Cowork / Claude Code) into your TikTok Shop affiliate outreach & marketing assistant; the affordable, agent-native alternative to Euka and Reacher.

> 🇺🇸 **English (this page)** · 🇨🇳 [中文版 / Chinese version](./README.zh-CN.md)

> 1 base skill + 8 business-scenario skills + Crevideo Reach MCP (79 tools) + 5 scheduled-task templates + 2 Project Instructions
> Works with Claude Code, Claude.ai web, and OpenAI Codex.

> **For**: TikTok Shop **US-Local** sellers driving affiliate creator outreach. China cross-border (POP) sellers — see the [中文版](./README.zh-CN.md) for localized wording.

---

## First: which client are you using?

Install path differs by client. **Anthropic intentionally treats them differently**:

| Client | Entry point | Steps |
|---|---|---|
| **Claude Code** (terminal / VS Code extension / desktop Code tab) | `/plugin` command | **1 step** |
| **Claude.ai web** (browser at claude.ai) | Customize page in the UI | **1 step** (connector auto-registers after plugin install; OAuth on first use) |
| **OpenAI Codex** | UI → "Add plugin marketplace" | **1 step** (source + sparse path `.agents/plugins`) |

> Anthropic's official note: [MCP servers on claude.ai must be added manually — there is no auto-registration](https://support.claude.com/en/articles/11175166-about-custom-integrations-using-remote-mcp). The `mcpServers` field in a plugin **only auto-takes-effect in Claude Code**, not on Claude.ai web.

---

## A. Claude Code users (developers / technical ops) — one-step install

In your terminal or VS Code:

```
/plugin marketplace add crevideo/crevideo-reach
/plugin install crevideo-reach@crevideo
```

Two things happen on install:
1. **9 skills auto-load** — triggered by natural business prompts ("score these creators", "draft an outreach message")
2. **MCP tools auto-connect** — first tool call pops a browser → OAuth to app.crevideo.com → log in to your Reach account → you can use all 79 tools

Run `/plugin list` to see what's installed, `/mcp` to check MCP connection status.

---

## B. Claude.ai web users (operations team) — one-step install

1. Open [claude.ai](https://claude.ai) → left sidebar → **Customize**
2. Go to the **Plugins** tab → top-right "+" → **Add marketplace**
3. URL / source: `crevideo/crevideo-reach`
4. Click Add → once installed the plugin shows up as "Crevideo Reach"
5. **Skills + connector both auto-load**:
   - Skills tab → 9 skills appear
   - Connectors tab → `crevideo-reach` appears automatically (pointing at `https://mcp.crevideo.com/reach`)

### First time using MCP tools

- Open a new conversation → bottom-left "+" → Connectors → enable Crevideo Reach
- Ask "show me my shops" → triggers OAuth login (browser → app.crevideo.com → back)
- Subsequent `list_shops` calls return your real shop list

> ⚠️ **If `crevideo-reach` does NOT appear in Connectors automatically** → you're on a cached old version. **Uninstall the plugin → reinstall once.** Only v2.0+ auto-registers the MCP into Connectors.

---

## C. OpenAI Codex users — one-step install

Codex expects plugins under a `plugins/codex/` subdirectory (different from Claude Code's "plugin lives at repo root" convention). We maintain both layouts in the same repo.

In the Codex client → **Plugins** → top-right "+" → **Add plugin marketplace**:

| Field | Value |
|---|---|
| **Source** | `crevideo/crevideo-reach` |
| **Git ref** | main branch (leave blank) |
| **Sparse path** | `.agents/plugins` ← directory containing marketplace.json (matches OpenAI's official convention) |

Click **Add marketplace** → marketplace "Crevideo" shows up in the filter → find plugin "Crevideo Reach" → install → 9 skills load.

> **MCP note**: Codex's current documentation on HTTP+OAuth MCP transport is light. If the plugin installs but Codex doesn't auto-connect Crevideo Reach MCP, add the URL `https://mcp.crevideo.com/reach` manually in Codex's MCP settings (same two-step approach as Claude.ai web).

---

## What's inside

### 🟢 Skills (9)

#### Base skill (1)

| Skill | What it does |
|---|---|
| `mcp-playbook` | MCP tool map + invocation rules + canonical create flow + error-code debugging + version notes (v0.6.1 / 42-tool build) |

#### Business-scenario skills (8)

| Skill | What it does |
|---|---|
| `creator-fit-scoring` | Scores a batch of creators on "can they actually drive GMV" + recommendation strength |
| `creator-tier-resolver` | Maps a creator to a collaboration tier (Open / T1 / T2 / T3·VIP) + judges Open→Target graduation |
| `offer-policy-checker` | Commission + compliance check (rate caps, free samples, policy risk) |
| `brand-safety-compliance` | Copy compliance scan (sensitive words, brand red lines, platform risk) |
| `outreach-message-composer` | Drafts outreach copy (DM / email / TC invitation) |
| `reply-triage` | Triages the DM inbox (interested / declined / needs follow-up) |
| `winback-and-pruning` | Wins back silent creators + prunes inactive ones |
| `performance-diagnosis` | Post-automation performance diagnosis + next-step recommendations |

### 🟢 MCP Server: `crevideo-reach` (79 tools)

Connects to `https://mcp.crevideo.com/reach` over OAuth — no manual API key configuration.

Capability surface:
- **Target Collaboration (TC)**: preview / create / start-stop / delete / clone / task results
- **DM automation**: single-message DM / combined TC+DM / edit text of live DM (`update_dm_automation_text`)
- **Creator management**: search / find-similar / detail / blacklist / tags / segments / journeys / creator lists
- **DM**: conversations / history / send
- **Email**: template list + inbox counts (send/reply pending the asset endpoint on backend)
- **Affiliate content**: lists / products
- **Customer Advocates (CA)**: list / tags / notes / contact enrichment
- **Shops / products / orders / analytics**: shop list / product list+detail / shop orders / affiliate analytics (collab / product / shoppable-video)

### 🟡 Extras (not part of the plugin's standard surface — manual setup required)

#### `extras/scheduled-tasks/` — 5 scheduled-task templates

Not auto-installed by the plugin. Claude.ai users create the 5 tasks under Settings → Scheduled Tasks manually, pasting each `.md` body in:

| Template | Frequency | Purpose |
|---|---|---|
| `00-scheduled-task-safety-header-must-go-first.md` | Required reading | Safety preamble for every scheduled task (must be at the top of each task) |
| `01-daily-morning-brief.md` | Each morning | Read yesterday's data / today's to-dos |
| `02-daily-midday-watch.md` | Each midday | Monitor today's automations + urgent replies |
| `03-daily-end-of-day-handoff.md` | Each evening | Roll up the day + leave notes for tomorrow's operator |
| `04-weekly-optimization-review.md` | Weekend | Weekly retrospective + adjust next week's strategy |

#### `extras/project-instructions/` — 2 Project Instructions

For Claude.ai's "Project" feature. Paste into two separate Projects:
- `monitor-project-instructions.md` — for the "Monitor" Project
- `action-project-instructions.md` — for the "Action" Project

#### `extras/brand-knowledge-pack-template.md`

Brand info the customer fills in themselves (brand name, SKU list, voice, Don't-words, etc.); all 9 skills auto-read it.

**Where to put the filled file**:
- **Claude.ai web**: paste the content into your Project's Custom Instructions (Customize → Projects → your Cowork Project → Custom Instructions)
- **Claude Code**: save the content to `~/.claude/CLAUDE.md` or project-level `<project>/.claude/CLAUDE.md`

The plugin still works without it, but output is more brand-aligned with it.

#### `extras/crevideo-reach-cowork-operations-handbook.md` + `extras/quickstart-card.md`

Human-readable docs for the operations team.

---

## Upgrading

### Claude Code

```
/plugin marketplace update crevideo
/plugin update crevideo-reach
```

MCP connection is preserved across upgrades; if something looks off, run `/mcp` to check status.

### Claude.ai web

Customize → Plugins → find Crevideo Reach → click "Check for updates" or uninstall + reinstall. **The connector is independent** — upgrading the plugin does not touch connector config; if the MCP URL changes, disconnect and re-add it in the Connectors tab separately.

---

## Feedback

For bugs or new-scenario requests, open an issue with the Crevideo team.
