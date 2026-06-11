# Message components, image upload & follow-ups

Every DM message in `create_dm_automation` / `create_tc_dm_automation` is an **array of components** sent in order as one logical message.

| type | purpose | required field | wire `content_type` | wire `content` |
|---|---|---|---|---|
| `text` | Plain text body (templated) | `text: string` | 1 | MCP creates a server template, stores `template_id` as content |
| `image` | Single image (png/jpg/jpeg/webp/gif, ≤10 MB) | `image_path: string` (absolute local path) | 6 | `"<image_no>.<ext>"` after OSS upload |
| `product` | Product card | `product_id: string` (from `list_products`) | 2 | product_id string |
| `collab` | Target-collab card pointing at THIS automation's TC | `use_this_tc: true` | 3 | literal `"default"` |

```ts
create_tc_dm_automation({
  ...,
  initial_components: [
    { type: "text", text: "Hey {{nickname}}! Premium invite." },
    { type: "product", product_id: "1732..." },
    { type: "collab", use_this_tc: true },
    { type: "image", image_path: "/Users/seller/launch-banner.png" }
  ],
  follow_ups: [
    { components: [{ type: "text", text: "Following up." }], timing: "delay", delay_value: 2, delay_unit: "days" }
  ]
})
```

## Image upload mechanics
For each image component, MCP:
1. Resolves the shop's numeric `shop_id` via `/member/currentmember`.
2. Calls `/affiliate_message/getpolicy` with `{ file_name, shop_id }` → signed Aliyun OSS credentials + a pre-allocated `image_no`.
3. POSTs multipart FormData to the returned `host` with the file bytes and key `<dir><image_no>.<ext>`.
4. Writes `{ image: { uid, name, status:"done", file_name, image_no } }` into `automation_json` and `<image_no>.<ext>` into the outer `message_content_list[].content`.

Failure surfaces as `ValidationError` / `OssUploadError` — nothing silent.

## Collab card scope
Only `{ type: "collab", use_this_tc: true }` is supported (wire `content = "default"`), **combined automations only**. **DM-only rejects collab** (UI gate `MessageComponentsSection.tsx:34`); MCP throws `ValidationError` pointing to `create_tc_dm_automation`. To attach an **arbitrary existing** TC, you'd need a `list_target_collaborations` tool — not shipped; if asked, call it a known gap, don't fabricate collab IDs.

## Content types we explicitly do NOT support
- `text-image` composite (`content_type: 4`) and `text-product-card` composite (`content_type: 5`) — out of scope. If asked, say "split into separate text + image / product components".

## Follow-up sequences
Up to 4 follow-ups after the initial (UI caps at 5 total). Each is a full **sequence** with its own `components`:
```ts
follow_ups: [
  { components: [{ type: "text", text: "..." }], timing: "immediately" },
  { components: [{ type: "text", text: "..." }], timing: "delay", delay_value: 2, delay_unit: "days" }
]
```
- `timing: 'immediately'` → `follow_up_type: 1`, `delay_time: 0`.
- `timing: 'delay'` → `follow_up_type: 2`, `delay_time = value × unit_seconds` (days 86400 / hours 3600 / minutes 60).

## Copy-flow template requirement
On DM/combined create the MCP calls `/automation/createmessagetemplate` once per message and stores the returned `template_id` in `automation_json.messageSetup.sequences[].components[0].data.selectedTemplates`. This is **required** for the UI's Copy flow (otherwise `validateAutomationSettings` crashes at step 5). If `createMessageTemplate` fails, MCP falls back to raw text in `follow_up_list[].message_content` — the automation still SENDS, but the Copy UI shows an empty editor.
