# Filter cheat sheet — `preview_target_collab`

Filter fields are **spread flat** into preview's input (no nested `filter` object). Schema is `.strict()` — unknown keys are rejected. Everything optional; omit what you don't need.

| Field | Shape | Notes |
|---|---|---|
| `count_range` | `{ min?, max? }` integers, follower counts | MCP clamps `max` to shop's `creatorFollowerCntMax` if set (new shops have outreach caps) |
| `age_ranges` | `['AGE_RANGE_18_24', ...]` enum array | 5 buckets: `_18_24` / `_25_34` / `_35_44` / `_45_54` / `_55_AND_ABOVE` |
| `gender_distribution` | `{ gender: 'MALE' \| 'FEMALE', percentage_ge: 0–100 }` | MCP ×100 on the wire — pass **raw percent**. `percentage_ge: 60` = "≥ 60%" |
| `gmv_ranges` | enum array | 5 buckets: `GMV_RANGE_0_5000` / `_5000_25000` / `_25000_60000` / `_60000_150000` / `_150000_AND_ABOVE` |
| `units_sold_ranges` | enum array | 4 buckets: `UNITS_SOLD_RANGE_0_10` / `_10_100` / `_100_1000` / `_1000_AND_ABOVE` |
| `creator_categorys` | `string[]` or `number[]` of category IDs | Get IDs from `list_creator_categories`. MCP coerces to string for the wire |
| `avg_video_views_range` | `{ min?, max? }` integers | Video views, **not** followers |
| `fulfillment_rate_range` | `{ min?, max? }` raw percent 0–100 | **No** ×100 (differs from gender). `{ min: 1 }` = "≥ 1%" |
| `keyword` | `string` | Creator nickname LIKE match |

**Natural-language → filter example.** User: "美妆类目、粉丝 10k–50k、女性占比 ≥ 70%、履约率 ≥ 90%":

```ts
preview_target_collab({
  shop_cipher: '...', first_count: 50,
  // ...product fields...
  creator_categorys: [/* category IDs from list_creator_categories */],
  count_range:        { min: 10_000, max: 50_000 },
  gender_distribution: { gender: 'FEMALE', percentage_ge: 70 },
  fulfillment_rate_range: { min: 90 }
})
```

If the user asks for something not in the table (e.g., "GMV 趋势" / "活跃度"), say the filter doesn't support it. **Don't fabricate a wire field** — the canonical UI curl is the truth source; the strict schema will reject unknown keys anyway.
