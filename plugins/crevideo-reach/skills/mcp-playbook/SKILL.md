---
name: mcp-playbook
description: >-
  Deep reference / playbook for correctly driving the crevideo-reach MCP — TikTok Shop affiliate (分销) work:
  tool map (74 tools), the create flow, TC automation-wrapper vs direct entity, Open Collaboration (OC),
  read-side tool choice, filter schema, ID-space gotchas, message components, naming, reporting gotchas,
  sample approval, email centre, and debugging. Use when creating/listing/debugging outreach automations
  (TC / DM), running Open Collaborations, pulling affiliate analytics (collaboration / product /
  shoppable-video GMV / orders), managing affiliate creators (list / detail / blacklist / tags / lists /
  segments / journeys), discovering creators, approving sample requests, or handling DM / email conversations.
  Affiliate-only — campaign/marketing out of scope. Triggers on 自动化/目标合作/公开合作/给达人发消息/发邮件/
  分销报表/分销订单/GMV/达人表现/我的达人/找达人/黑名单/分群/申样/私信 and the English equivalents.
---

# Crevideo Reach MCP — Playbook

> **Output language**: respond in the merchant's working language — Chinese for China-POP sellers (分级 / 真实抽成 / 触达阶梯 …), English for US sellers (tier / all-in take-rate / outreach waterfall …). Tool names stay identical in both.


The **deep-reference layer** for the `crevideo-reach` MCP server. The MCP itself ships a baseline tool-map + typical flow via `SERVER_INSTRUCTIONS`; this skill covers what that baseline can't — the create flow, non-obvious constraints, and how to debug when behavior diverges from the brands-app UI.

> **Source of truth**: if anything here disagrees with the **live tool list** in the running session, the **live tools win** (this doc can lag the MCP version). Only call tools that actually exist in the session; never assume historical or future tools.

## When NOT to use
- Pure MCP-protocol or Claude-skill mechanics questions → wrong skill.
- Nothing about automations, creators, affiliate analytics, TikTok Shop, or the reach area → don't invoke.

## Scope: affiliate (分销) only
The MCP exposes **only affiliate/distribution** features. Campaign/marketing surfaces (campaign management, `/report/*` campaign reports, creator **marketplace** discovery, custom products, brand sampling) are **not** tools here — say they're out of scope. `query_*_performance` covers **collaboration / affiliate-product / shoppable-video** (affiliate), not campaign reports.
There is **no** natural-language→filter and **no** copy-polish endpoint — **you (the LLM) translate the user's words into structured filters yourself**, and you write/edit message copy yourself.

## Tool map (74 tools, 15 groups)
Many tools **fold multiple endpoints behind a `view` / `action` / `scope` / `op_type` param** — pass those to drill in; don't expect one tool per endpoint.

- **Automations (💳 on create):** `list_automations`, `get_automation_detail`, `get_automation_task_results`, `preview_target_collab` (free, 10-min token), `create_target_collab`, `create_dm_automation`, `create_tc_dm_automation`, `clone_and_modify_automation`, `start_automation` / `pause_automation` / `delete_automation`, `update_dm_automation_text`, `create_bulk_tc_operation`, `manage_message_templates`, `get_dm_task_message_template`.
- **TC direct entity (no automation wrapper):** `list_target_collaborations`, `get_target_collaboration_detail`, `preview_target_collab_direct`, `create_target_collab_direct`, `update_target_collab_direct`, `cancel_target_collaboration`, `add_creator_to_target_collaboration`. See "TC: two creation paths" below.
- **Open Collaboration (OC):** `list_open_collaborations`, `get_open_collaboration_detail`, `create_open_collaboration`, `modify_open_collaboration`, `remove_open_collaboration`, `manage_open_collaboration_settings` (get/edit), `manage_open_collaboration_sample_rule` (get/edit), `remove_creator_from_open_collaboration`, `list_not_added_open_collaboration_products`, `get_open_collaboration_product_count`, `get_open_collaboration_status_counts`.
- **Reporting (read-only, 3 tools):** `query_collaboration_performance` (`scope`: target/open/both), `query_product_performance`, `query_shoppable_video_performance`. breakdown/detail views run on **TT-official REAL-TIME data** (matches the brand-app page; video adds GPM + daily avg customers, product AOV is derived GMV÷orders); overview/trend stay on legacy aggregates — small drift between the two is expected, explain it rather than "fix" it. **There is NO `query_ai_insight` tool — produce insight yourself by reading these three reports.**
- **Affiliate orders:** `list_affiliate_orders` (TC dimension — the canonical "我分销卖了多少" answer; `target_collaboration_id` REQUIRED), `list_customer_creator_orders` (🚨 naming trap — CA dimension, NOT distribution GMV).
- **Creators:** `list_affiliate_creators`, `get_creator_detail`, `manage_creator_blacklist` (list/add/remove/modify), `update_creator_metadata`, `manage_segment`, `manage_journey`, `manage_creator_lists`, `get_creator_list_overview`, `search_affiliate_creators` (filters **or `creator_keyword` to resolve a @handle → user_id**), `find_similar_creators`, `list_customer_advocates`, `manage_customer_advocates` (7 actions), `manage_contact_enrichment`.
- **Content:** `list_affiliate_content` (TT-official REAL-TIME published videos — needs `shop_cipher`, optional date range/`account_type`), `get_affiliate_content_products`, `manage_affiliate_content` (list/add_notes — legacy row ids; use its ids for notes, not the real-time list's).
- **DM conversations:** `list_conversations`, `get_conversation_messages`, `send_message` (real outbound — confirm), `create_conversation` (open a NEW conversation, idempotent), `list_conversation_groups` (left-rail groups + counts), `list_group_conversations` (conversations in one group — use for "show unread DMs").
- **Email (8 tools, LIVE):** `list_email_conversations`, `get_email_detail`, `send_email`, `reply_email`, `manage_email_templates` (CRUD), `manage_email_drafts` (CRUD), `mark_email_as_read`, `move_emails_to_trash`. `send_email` / `reply_email` are **real outbound** — confirm intent first.
- **Samples (申样):** `manage_sample_auto_approval` (get/update rules), `manage_sample_applications` (list/approve/reject the manual queue).
- **Shops/products:** `list_shops`, `list_products`, `get_product_detail`, `list_creator_categories`.

> **Renamed tools — never call the old names**: `list_email_templates` → `manage_email_templates` with `action:'list'`; `list_shop_orders` → `list_affiliate_orders`; `list_segments` / `list_journeys` → `manage_segment` / `manage_journey` with `action:'list'` (list returns both official + custom entries).

## The canonical create flow
Every automation create chains these 6 phases — don't jump to the final call:
```
1. Shop         → list_shops if user hasn't specified one
2. Product      → list_products, validate ACTIVATE status, pick commission rate
3. Category IDs → list_creator_categories if filter mentions categories by name
4. Preview      → preview_target_collab with filter + first_count → preview_token
5. Confirm      → show match count + 5 samples + warnings, ASK USER to confirm
6. Create       → create_* with preview_token + final params
```
**Only skip a phase** when the user explicitly supplied that data. Don't invent defaults for shop or products.

**Creator source alternates** (skip preview; need `shop_cipher`): `source='manual'` takes `appoint_creator_list` = **TikTok handles** (unique_id, `@` ok) — the MCP resolves them to the right wire ID space automatically; unknown handles fail fast. `'list'` / `'segment'` / `'journey'` take IDs from the matching `manage_*` list action; `'target_collab'` (DM-only) re-targets a TC automation's creators. `clone_and_modify_automation` is filter-source-only — manual-source automations can't be cloned, create anew.

**Timing parameters** (2026-06-11): `tc_schedule`/`tc_delay_time`/`tc_time_unit` (TC/combined — delay the TC entity creation), `send_schedule`/... (DM-only — delay the whole send), `dm_schedule`/... (combined — delay the DM part). All default immediate; `'delay'` needs raw amount (NOT seconds) + unit. **Follow-ups are delay-only** (immediate removed from the product 2026-06-10; default 10 minutes after the previous message) — the initial message still sends immediately. Backend doesn't echo these inputs; verify via `tasks[].pending_excute_time` on the list row.

## TC: two creation paths (NOT interchangeable)
| Path | Entry | Produces | When |
|---|---|---|---|
| **Automation-wrapper** (default) | `preview_target_collab` → `create_target_collab` / `create_tc_dm_automation` | An *automation* that owns a TC — templates, follow-ups, schedule | User says "建一个推广 / 自动化 / outreach" |
| **Direct entity** | `preview_target_collab_direct` → `create_target_collab_direct` | A bare TC, no wrapper / DM / follow-ups; manage via `update_target_collab_direct`, `cancel_target_collaboration`, `add_creator_to_target_collaboration` | User has exact **`creator_open_ids`** (NOT user_ids, NOT handles) and wants the offer pushed now, or batch work via `create_bulk_tc_operation` |

Direct-entity TCs **don't appear in `list_automations`** — list via `list_target_collaborations`. Wrapper TCs appear in both. Never control a wrapper TC with direct-entity tools — use `clone_and_modify_automation` / `create_bulk_tc_operation`.

## OC vs TC (scope mistake to avoid)
"公开合作 / open collab / 让达人主动申请" = **OC** (public offer, creators apply, you approve — no preview, no creator filter), NOT TC (you invite specific creators). For OC reporting use `query_collaboration_performance` with `scope:'open'`. OC sample policy lives in `manage_open_collaboration_sample_rule`; shop-wide OC defaults in `manage_open_collaboration_settings`.

## Read-side: pick the right tool
| Tool | Answers | When |
|---|---|---|
| `list_automations` | "What does the list look like?" | Browsing / finding an ID / filtering by status+type |
| `get_automation_detail` | "What is this configured to do + overall stats?" | Name/ID → config / shop / product / filter / overview |
| `get_automation_task_results` | "Who got reached, and what happened to each?" | Post-send per-creator status + failure reasons |

- `list_automations` — **always pass arrays** for `statuses` / `automation_types` (scalar is silently ignored). Statuses: `pending`(=`draft`)/`active`/`paused`/`completed`/`failed`. Types: `tc`/`dm`/`combined`. Page ≤ 50, default 10. `search_by:'no'` matches `automation_no`; default matches name LIKE.
- `get_automation_task_results` **auto-detects** `kind`+`task_id` from `automation_type` — usually just pass `automation_id`. Draft automations return empty (start first).

## Editing without recreate
- `update_dm_automation_text` — replace one text body in a running DM (text components only; image/product/collab → clone instead).
- `create_bulk_tc_operation` — 6 `action`s (`add_creators` / `remove_creators` / `add_products` / `remove_products` / `edit_settings` / `cancel`) batch-mutating the TC behind an automation. TC only — for DM-only automations, clone.
- `get_dm_task_message_template` — read the template a DM task actually used (diagnose "why did the wire body differ from my draft").
- Anything else (filter / schedule / follow-up structure / non-text component) → `clone_and_modify_automation` + `delete_automation` the old one.

## High-leverage gotchas (memorize)
- **FOUR creator ID spaces** ⚠️ (the #1 cause of 101 "bad request"): `user_id` (numeric — detail / notes / find_similar seed / create_conversation / send_email) · record `id` (row UUID — tags / blacklist) · `creator_open_id` (long base64-ish — direct-TC flow only) · `unique_id` (TikTok handle — what humans type; `appoint_creator_list` + bulk `creator_unique_ids` take handles, MCP resolves internally). Rule of thumb: human inputs = handles, analytics = user_id, blacklist/tags = record id, direct-TC = open_id.
- **Preview token**: 10-min lifetime, **one-shot** (consumed even if the create later throws), **scope-bound** to shop+filter+first_count. If a create fails, **re-run `preview_target_collab`** before retrying — don't reuse the token.
- **Product status = ACTIVATE only**: creates hard-fail if any product isn't `ACTIVATE` (also the Copy-flow silently drops non-ACTIVATE products). Tell the user to reactivate; no workaround.
- **`*_increment` fields are period TOTALS, not deltas** — don't read them as growth % (legacy views only; TT-official rows use plain names like gmv/orders).
- **TT-official lists use token pagination** — tools return the first page (`page_size` ≤ 50) + a "Showing N of M" line; narrow the date range or raise page_size, there is no `page` param. Responses carry `latest_available_date` ("data as of") — data lags 1-2 days, a missing "today" is not zero performance.
- **`detail` view rates are derived, not raw fields** (mirrors the brand-app detail drawers): product detail = Sales (AOV=GMV÷orders) + Sales-by-channel (share%) + Traffic funnel (CTR=clicks÷impressions, conversion=orders÷clicks) + Ratings + Top contents/creators; video detail = Sales (GMV/GPM/items/customers) + Conversion funnel (exposure=impressions÷views, CTR-to-views=clicks÷views, conversion=units÷clicks) + ER=(likes+comments+shares)÷views + Audience. Don't look for a "conversion_rate" field — produce/cite the derived value.
- **`list_affiliate_orders` needs `target_collaboration_id`** (backend 504s without it). Cross-TC view: `list_target_collaborations` first → loop per TC. OC dimension not supported.
- **Naming**: names are server-allocated; user-facing names get an `mcp_YYMMDDHHmm_` prefix. The TC "定向计划名称" is capped at **12 chars** and sanitized to `[A-Za-z0-9_]`; **pure-Chinese input falls back to the literal `mcp`** (so multiple Chinese-named automations collide) — keep the part that lands in the automation name **short ASCII**.
- **Paid-tier features**: customer-advocates + contact-enrichment are yearly add-ons — a 403 / empty response usually means the account lacks the add-on, not a bug.

## Anti-spam / advanced settings (DM & combined)
| Param | Effect | Default |
|---|---|---|
| `reply_handling: 'continue' \| 'stop_on_reply'` | stop pinging creators who replied | `continue` |
| `skip_creators_with_prior_replies: bool` | skip creators with prior replies | `false` |
| `skip_messaged_within_days: number` (0=off) | skip anyone messaged within N days | off |

## Message components (summary)
Each DM message is an **array of components** sent in order. 4 types: `text` (templated body), `image` (local path, ≤10 MB), `product` (`product_id`), `collab` (`use_this_tc:true`, **combined-only — DM-only rejects it**). Full wire formats, image OSS-upload mechanics, follow-up sequencing, and unsupported composite types → **`references/message-components.md`**.

## Conversations + email
- DM: `list_conversations` / `get_conversation_messages` / `send_message` (real outbound — confirm). New thread → `create_conversation` first (idempotent). Triage by group: `list_conversation_groups` → `list_group_conversations` (don't client-side filter the flat list for "unread").
- **DM a creator who is NOT yet in your pool (you only have their @handle):** `search_affiliate_creators(creator_keyword='@handle')` → `user_id` → `create_conversation([user_id])` → `send_message`. This path is **FREE** (no MCP credits). Do **NOT** fall back to a manual-source `create_dm_automation` (spends 3 credits + creates a standing automation) just because no conversation exists yet — that's only the right tool for *bulk* outreach, not a single cold DM.
- Email: `thread_id` = root_email_id (a per-message id returns code=-1); read `get_email_detail` before `reply_email`. `send_email` requires `shop_id` (auto-resolves single-shop). Templates are starting drafts, not verbatim sends. Both send tools need `confirm:true`.

## Sample approval (申样)
`manage_sample_auto_approval` `get`/`update` sets the rule thresholds; `manage_sample_applications` `list` (needs `shop_cipher`) → `approve` / `reject` by `apply_id` clears what the rule didn't cover.

## Safety defaults (do NOT change without explicit user intent)
- `start_immediately` defaults to **false** — only `true` on literal "立即启动/send now/start now/发吧". Treat `true` as **destructive** (messages go to real creators); confirm first.
- `per_target_collaboration_creator_count` defaults to 50; override only on explicit ask (bound-checked vs shop limit).
- `has_free_sample` defaults to false.
- Blacklist / segment / journey / creator-list mutations, sample approve/reject, OC removals, email/DM sends — all need `confirm:true`. Surface the plan, then wait.

## Deep references (load on demand)
- **`references/filters.md`** — full `preview_target_collab` filter schema: every field shape, enum buckets, the ×100 / no-×100 rules, and a natural-language→filter example.
- **`references/message-components.md`** — the 4 component types' wire formats, image OSS upload, collab-card scope, unsupported composites, and follow-up sequence wire detail.
- **`references/debugging.md`** — the 3-layer (curl readback → wire capture → source read) debugging method, common error→cause table, Copy-flow template requirement, sample-approval radios, and the version-change history.
