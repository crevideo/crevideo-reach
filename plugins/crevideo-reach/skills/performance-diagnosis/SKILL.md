---
name: performance-diagnosis
description: >-
  Reads the 3 affiliate reports (collaboration / product / shoppable-video) and produces
  decision-grade insight itself — key metrics + 5 leading indicators + a decision list.
  This is where "Claude produces the AI insight" lives — there is NO query_ai_insight tool.
  Use when the user wants a performance check, health check, data review, weekly/monthly
  digest, or "insights and recommendations". Affiliate (分销) context.
  Chinese triggers: 看效果 / 体检 / 复盘数据 / 周报月报 / 这周表现怎么样 / 给我洞察和建议.
---

# Performance Diagnosis · 效果诊断(洞察引擎)

**Role**: turn affiliate report data into **decisions**. There is no `query_ai_insight` tool — **the insight is produced by Claude reading the reports**; do not look for a `query_ai_insight` tool. **Read-only, no outbound.** **Scope — vs Winback & Pruning**: this skill does *program-level* affiliate performance diagnosis (which products/creators/videos to scale / hold / stop). For *individual-creator* re-engagement or pruning decisions, use winback-and-pruning.

## Output language
Write every output in the **merchant's working language**, using that market's native seller terminology:
- **US Local sellers → English**: tier / all-in take-rate / outreach waterfall / Target Collaboration / effective creators …
- **China POP sellers → 中文**: 分级 / 真实抽成 / 触达阶梯 / 定向邀约 / 有效达人 …
Tool names (e.g. `query_collaboration_performance`) stay identical in both languages. If unsure which market, ask once before producing output.

## When to use / not use
- Use: health-check running automations, review GMV/conversion, data diagnosis for a weekly/monthly digest, "insight + recommendations".
- Don't use: handling specific replies (→ reply-triage); a single automation's failure post-mortem (→ winback-and-pruning).

## Inputs
1. shop_cipher + time range (days, default 30, or start/end).
2. Focus (optional): overall / a product / a video / graduation / win-back.

## Steps (in order)
1. **Fetch (read-only)**: query_collaboration_performance (scope target/open/both), query_product_performance, query_shoppable_video_performance. Use view=overview/breakdown/detail as needed.
2. **Field traps**: product/video breakdown+detail are TT-official REAL-TIME (match the brand-app page) while overview/trend/collab are legacy aggregates — small overview-vs-breakdown drift is **expected** (two data generations), note it instead of "reconciling". `*_increment` is the **period total** (not a growth rate) but applies to the **legacy** views only — TT-official rows use plain names (gmv/orders/units_sold). TT-official lists are **token-paginated** (you get the first page + a "Showing N of M" line; narrow the date range for the rest — there is no full-set dump). Responses carry "data as of" (latest_available_date, 1-2 day lag) — a missing "today" is not zero performance. Write N/A for any number you can't get and say which query failed — **never fabricate**.
3. **Explicit reasoning**: before giving conclusions, think through "data → meaning → likely cause" internally (insight is produced by your own reasoning, not by some ready-made insight tool); avoid zero-shot guessing.
4. **Compute the 5 leading indicators** (definitions in the Cheat Sheet): ① qualified acceptance rate (meaningful interaction, not raw reply rate) ② reply → first-video time ③ graduatable creator count ④ effective-creator share (active creators producing converting content) ⑤ outreach-pressure / suppression health (share blocked by cooldown / already-replied rules; rising = drifting toward spam). Give each a current value + a trend arrow.
5. **Risk scan**: all-in take-rate over the line (threshold in the registry), suspected duplicate outreach, zero-output automations (0 accepted / 0 videos), violation signs.
6. **Decision-style output**: each point lands on **change → cause → decision needed → recommended action → risk of inaction → evidence**; anything that can't become a decision is "too descriptive" — drop it.
7. Close with "N items need your decision now".

## Sibling skills
- Found someone to graduate → creator-tier-resolver; to win back / zero-output → winback-and-pruning; to launch → outreach-message-composer (all 🔴, need human confirm).

## ⛔ Do NOT
- Never call / pretend `query_ai_insight` exists — it doesn't; produce insight yourself.
- Never treat `*_increment` as a growth rate; never fabricate a missing number.
- Never run any outbound/create in this skill (read-only); for an action, give a recommendation + mark "execute manually in the Action Workspace".
- Never dump numbers with no decision.

## 📌 Strategy dependency (anti-staleness)
The 5 leading-indicator definitions, all-in take-rate threshold, graduation line, and industry baselines all come from the Action Workspace Doctrine → Key-Rules Cheat Sheet; if an example here conflicts with current policy, the Cheat Sheet wins. Only call report tools that actually exist.

## Output format
Render the report in the merchant's working language; the template below shows the structure.
```
📊 Performance diagnosis (shop · time range · # tools called)
[Facts] net GMV, creators producing videos, conversion… + vs prior period (N/A if missing, note failed query)
[5 leading indicators] ①qualified acceptance X%↑ ②reply→video N days→ ③graduatable k↑ ④effective-creator share Y%↓ ⑤suppression health Z%↑ (drifting to spam?)
[Risks] all-in take-rate over line / duplicate outreach / zero-output automations / violation signs (else "none")
[Decision list]
1. Change: T2 acceptance↓ → Cause: undercut → Decision: bump or not → Action: small commission lift for Top5 pending T2 (via offer-policy-checker) → Risk of inaction: miss peak season → Evidence: …
N items need your decision now. (Read-only throughout, nothing sent, no charge.)
```

## Example
Input: last 30 days of shop data. Output:
Facts (net GMV, creators producing videos, conversion; N/A for missing); 5 leading indicators each with value + arrow (e.g. qualified acceptance↑, effective-creator share↓); risks (all-in take-rate over line / duplicate outreach / zero-output automations); decision-list example: "T2 acceptance↓ → cause: undercut → decision: bump or not → action: small commission lift for Top5 pending T2 (via offer-policy-checker) → risk of inaction: miss peak season → evidence: …". Read-only, nothing sent.
