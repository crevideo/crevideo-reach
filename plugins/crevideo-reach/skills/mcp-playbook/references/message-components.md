# Message components, image upload & follow-ups

Every DM message in `create_dm_automation` / `create_tc_dm_automation` is an **array of components** sent in order as one logical message.

| type | purpose | required field | wire `content_type` | wire `content` |
|---|---|---|---|---|
| `text` | Plain text body (templated) | `text: string` | 1 | MCP creates a server template, stores `template_id` as content |
| `image` | Single image (png/jpg/jpeg/webp/gif, ≤10 MB) | `image_path` (absolute local path — local MCP) **OR** `image_base64` (raw/data-URL — hosted/web like claude.ai) | 6 | `"<image_no>.<ext>"` after OSS upload |
| `product` | Product card | `product_id: string` (from `list_products`) | 2 | product_id string |
| `collab` | Target-collab card pointing at THIS automation's TC | `use_this_tc: true` | 3 | literal `"default"` |
| `text_image_card` | Native text + image card | `title`, `content`, exactly one of `image_path` / `image_base64` | 4 | `{title, content, image_no}` |
| `text_products_card` | Native text + products card | `title`, `content`, `product_ids` (1-5 unique ACTIVATE OC products) | 5 | `{title, content, product_ids}` |

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
2. Calls `/affiliate_message/getpolicy` for plain `image`, or `/automation/getpolicy` for `text_image_card`, with `{ file_name, shop_id }` → signed Aliyun OSS credentials + a pre-allocated `image_no`.
3. POSTs multipart FormData to the returned `host` with the file bytes and key `<dir><image_no>.<ext>`.
4. Writes `{ image: { uid, name, status:"done", file_name, image_no } }` into `automation_json` and `<image_no>.<ext>` into the outer `message_content_list[].content`.

Failure surfaces as `ValidationError` / `OssUploadError` — nothing silent.

## Collab card scope
Only `{ type: "collab", use_this_tc: true }` is supported (wire `content = "default"`), **combined automations only**. **DM-only rejects collab** (UI gate `MessageComponentsSection.tsx:34`); MCP throws `ValidationError` pointing to `create_tc_dm_automation`. To attach an **arbitrary existing** TC, you'd need a `list_target_collaborations` tool — not shipped; if asked, call it a known gap, don't fabricate collab IDs.

## Native composite cards

**Mandatory intent routing:** if the user only says "图文" / "图文私信" / "文字配图", ask one short clarification before preview/create: "要单张图文卡，还是文字和图片分开发？" Explicit "图文卡/一个卡片/不要分开" means a single native `text_image_card`; explicit "分开发/两个组件/两条消息" means separate `text` + `image`. "文字商品卡" means a single native `text_products_card` unless separation is explicit.

`send_message` supports two official direct-DM card modes:

- `text_image_card` → `CRM_TEXT_WITH_IMAGE_CARD`; requires `title`, `content`, exactly one image source, and `confirm:true`. The structured body contains the uploaded OSS `image_no`.
- `text_products_card` → `CRM_TEXT_WITH_PRODUCTS_CARD`; requires `title`, `content`, 1-5 unique `product_ids`, and `confirm:true`. MCP verifies every product belongs to the selected shop.

The same names are valid component `type` values inside `create_dm_automation` and `create_tc_dm_automation`, in both `initial_components` and `follow_ups[].components`. Automation cards use `content_type` 4/5, create template types 2/3, store Brand-compatible `selectedTemplateDataList`, and set `is_composite_msg=1`. Do not emulate a requested native card with separate text + image/product components.

For safe acceptance, create with `start_immediately:false`, then open and copy it in Brand. A successful create response alone does not prove `automation_json` is copy/edit compatible.

## Follow-up sequences
Up to 4 follow-ups after the initial (UI caps at 5 total). Each is a full **sequence** with its own `components`:
```ts
follow_ups: [
  { components: [{ type: "text", text: "..." }], timing: "delay", delay_value: 2, delay_unit: "days" }
]
```
- Follow-ups are delay-only; `timing:'immediately'` is rejected.
- `timing: 'delay'` → `follow_up_type: 2`, `delay_time = value × unit_seconds` (days 86400 / hours 3600 / minutes 60).

## Copy-flow template requirement
On DM/combined create the MCP calls `/automation/createmessagetemplate` for each templated component and stores the returned `template_id` in that component's `selectedTemplates`. Plain text uses template type 1; text-image uses 2; text-products uses 3. Composite cards also store `selectedTemplateDataList` plus image/product metadata. Composite template failure aborts creation rather than producing an automation that sends but loses fields in Brand copy/edit.
