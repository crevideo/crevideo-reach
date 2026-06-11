# Debugging, error patterns & version history

## When wire diverges from UI — walk this order
If a created automation works in the list but breaks in the Copy flow, UI preview, or detail view:

1. **Layer 3 — Post-submit readback**:
   ```bash
   curl -sS -X POST '<base>/openapi/v1/brands/automation/queryautomationdetail' \
     -H 'X-API-Key: <key>' -H 'Content-Type: application/json' \
     -d '{"automation_id":"<id>"}'
   ```
   Inspect `data.automation_json` (parse the string); compare to what the UI expects at the broken step.
   **Limitation**: only `automation_json` round-trips. Outer wire fields (`schedule_type`, `workflow_sequencing`, `within_messaged_timestamp`) are NOT echoed.

2. **Layer 2 — Wire capture**: ask the user for a real UI curl (F12 → Network → the `createbulk*` request → Copy as cURL). Diff EVERY field vs MCP's builder output. **Source inference is NOT enough** — wizard state mutates between `initialState` and submit; the captured curl is authoritative, not the React source.

3. **Layer 1 — Source read** (brands-app `src/pages/Reach/Program/Automations/`):
   - `components/NewAutomation/index.tsx` — `transformToBackendFormat` (canonical payload builder)
   - `components/NewAutomation/components/AutomationDrawer.tsx` — wizard state machine, `getautomationname`, validation
   - `components/CopyAutomation/index.tsx` — how Copy reads back and rehydrates

## Common error patterns
| User sees | Likely cause |
|---|---|
| "1 product(s) are not in ACTIVATE status" | Deactivated product OR a transient `/querytiktokshopproductlist` 5xx (no longer swallowed — real error shows through) |
| Copy step 5 "Cannot convert undefined or null to object" | Pre-0.3.9 automation (component.data was null). New creates shouldn't hit this |
| "Copy Step 2: No products found" | ALL products on the automation are non-ACTIVATE — check product status |
| "Preview token expired" | >10 min since `preview_target_collab`, or token already consumed — re-run preview |
| Name shows `TC_Auto_20260423_4832` (YYYYMMDD + random suffix) | Pre-0.3.9 automation; new ones show MMDDYYYY + monotonic `_N` |

## Preview token semantics (detail)
Lifetime 10 min; one-shot (consumed on first use even if the create later throws); scope-bound to shop+filter+first_count. Re-run preview before any retry.

## Sample-approval radios
When `has_free_sample=true`, the UI shows: "Auto approve" → `is_sample_approval_exempt:1`; "Manually review" → `:0`. When `false` → `:-1` (off). MCP's `is_sample_approval_exempt` (bool, optional) is only meaningful when `has_free_sample=true`, and must land consistently in **both** the outer wire AND `automation_json.targetCollaboration` (a 0.3.9 bug hardcoded `-1` only in automation_json → Copy UI showed neither radio).

## Server naming & date format (detail)
Names allocated via `/automation/getautomationname`: TC-only `TC_Auto_<MMDDYYYY>_<N>`; DM-only `DM_Auto_<MMDDYYYY>_<N>`; Combined needs **three** calls (`TCDM_Auto_…` outer + `TC_Auto_…` + `DM_Auto_…`). `<MMDDYYYY>` is local time; `<N>` is a server-side monotonic per-prefix counter. User-facing names always get `mcp_YYMMDDHHmm_`. TC "定向计划名称" capped 12 chars, sanitized `[A-Za-z0-9_]`, pure-Chinese → literal `mcp`.

## Version history
| Version | Key change |
|---|---|
| 0.3.8 | automation_json schedule/workflowSequencing set to 'immediately' (was null) |
| 0.3.9 | getautomationname + MMDDYYYY naming + createMessageTemplate + ACTIVATE product gate |
| 0.3.10 | is_sample_approval_exempt consistency fix; TCDM_Auto outer name; local-time chat_name; reply_handling / skip / follow_ups exposed; stopped swallowing fetchProductMeta errors |
| 0.4.0 (breaking) | Mixed-component messages (`text`/`image`/`product`/`collab`); old `message_text`/`dm_message_text` removed (use `initial_components`); image upload via getpolicy + OSS; collab rejected on DM-only |
| 0.5.0 | HTTP transport + OAuth (per-user JWT) for hosted multi-tenant; stdio unchanged |
| 0.6.0 | +affiliate analytics & creator management (reporting / my-creators / recruit / content / conversations); affiliate-only scope |
| **current** | **`query_ai_insight` removed** — produce insight by reading the 3 `query_*` reports. **Email send/reply/inbox now LIVE** (`list_email_templates` / `list_email_conversations` / `get_email_detail` / `send_email` / `reply_email`); `send_email`/`reply_email` are real outbound — confirm first |
