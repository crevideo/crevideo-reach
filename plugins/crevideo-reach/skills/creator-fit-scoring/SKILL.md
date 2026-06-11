---
name: creator-fit-scoring
description: >-
  Scores one or more TikTok Shop creators for real sell-through fit using evidence
  (conversion over followers), returning a fit verdict + recommendation strength +
  outreach suggestion, with a lookalike-expansion recruiting mode. Use when the user
  wants to score, rank, evaluate, shortlist, compare, or find lookalike creators, or
  to process the sample creators returned by preview_target_collab. Affiliate (分销) context.
  Chinese triggers: 打分 / 评估 / 筛选 / 找相似达人 / 这批达人合不合适.
---

# Creator Fit Scoring · 达人契合打分

**Role**: turn a creator's profile/history/performance into an evidence-based judgment of "worth collaborating for this product/brand?". **Only judges fit + recommendation strength** — does not set tier (creator-tier-resolver), validate commission (offer-policy-checker), or write copy (outreach-message-composer).

## Output language
Write every output in the **merchant's working language**, using that market's native seller terminology:
- **US Local sellers → English**: tier / all-in take-rate / outreach waterfall / Target Collaboration / DM / sample …
- **China POP sellers → 中文**: 分级 / 真实抽成 / 触达阶梯 / 定向邀约 / 私信 / 寄样 …
Tool names (e.g. `preview_target_collab`, `find_similar_creators`) stay identical in both languages. If unsure which market, ask once before producing output.

## When to use / not use
- Use: score/rank a creator or a sample pool, decide whether to contact, run lookalike-expansion recruiting.
- Don't use: the user only wants report data (→ performance-diagnosis); only wants copy (→ outreach-message-composer).

## Inputs (ask if missing, max 3 questions)
1. Creator data source: preview_target_collab sample, list_affiliate_creators results, or a profile the user pastes.
2. Target product: name/category/price (for fit; product must be ACTIVATE).
3. Optional: the user's preferred tier or headcount.

## Steps (in order)
1. **Exclude first (before ranking/scoring)**: cross-check the **backend blocklist + project `Knowledge/do-not-contact`** (incl. cooldown-not-elapsed, whole-category avoidance); matches go to "Excluded" and are not scored (check both seeds and results).
2. **Extract evidence per creator** (only real fields you actually have; write N/A for missing, never fill with "industry average"): conversion signal (GMV per view / orders), audience fit (category + audience vs product), trust signal (fulfillment rate / past collaborations), risk signal (recent complaints / fulfillment drop).
3. **Apply "conversion > followers" as the first principle**: follower count is reference only, never the primary basis.
4. **Per creator, output**: fit (high/mid/low) + **3 concrete fit reasons + 1 uncertainty** + suggested product + suggested outreach (as input for creator-tier-resolver / outreach). **If you cannot give 3 concrete reasons → output HOLD (do not contact yet); do not pad.**
5. **Pool-level assessment**: headcount, fit distribution, whether to loosen/tighten the filter.
6. **Lookalike-expansion mode** (when the user wants "find similar / scale up"): seed find_similar_creators only with **sustained-sales** creators (look at repeat/continuous conversion, not one GMV spike); re-exclude the blocklist on both seeds and results; output a "suggested new-invite candidates" table (same 3 reasons + 1 uncertainty).

## Sibling skills
- Need a tier decision → creator-tier-resolver.
- Need to validate a proposed commission/offer → offer-policy-checker.
- Need outreach copy → outreach-message-composer (auto-runs brand-safety-compliance).

## ⛔ Do NOT
- Never auto-trigger create_*/start_/send_: output judgment only, wait for a human decision.
- Never call high fit on "lots of followers / content looks good" alone — require conversion or audience evidence.
- Never fabricate data; mark missing dimensions N/A.
- Never use a blocklisted creator as a lookalike seed or a recommendation.
- Never dump an oversized scoring table at once (paginate and ask which batch to see first).

## 📌 Strategy dependency (anti-staleness)
This skill embeds **no** thresholds, commission numbers, tier definitions, tool lists, or naming rules. All numbers/tiers/tools come from the Action Workspace Doctrine → Key-Rules Cheat Sheet; if you are reciting from memory, or an example here conflicts with current policy, the Cheat Sheet wins. Only call tools that exist in this session (e.g. `query_ai_insight` does NOT exist — insight is produced by reading reports yourself).

## Brand knowledge needed (project `Knowledge/`, read if present)
Depends on: `brand-voice` (audience/category fit), `sku-catalog-snapshot` (target product + persona), and **`do-not-contact` (must read before generating candidates, for exclusion)**.

## Output format
Render the report in the merchant's working language; the template below shows the structure.
```
[Pool] N creators · fit high:a/mid:b/low:c/excluded:d · one-line quality note · suggest keep/loosen/tighten filter
[Ranking]
| # | Creator | Key conversion signal | Audience fit | Fit | 3 reasons (brief) | Uncertainty | Suggested product | Suggested outreach |
| 1 | @xxx | high GMV/view, repeat buyers | category+audience overlap | high | …;…;… | … | <SKU> | warm → Target Collaboration |
[HOLD — insufficient evidence, do not contact yet] @yyy: followers only, no conversion/audience evidence
[Excluded] @zzz: on blocklist / risk signal
(lookalike mode adds) [Suggested new-invite candidates Top N] seeds = sustained-sales creators; blocklist already excluded
```

## Example
Input: 3 candidate creators + one ACTIVATE face cream. Output:
- @a fit **high**: strong GMV/view, audience overlaps skincare, sold same category in last 30 days; uncertainty: fulfillment rate unknown → suggest T2, warm → Target Collaboration.
- @b **HOLD**: followers only, no conversion evidence, can't give 3 reasons → do not contact yet.
- @c **Excluded**: on the blocklist.
(Numbers/tier caps per the Key-Rules Cheat Sheet; output judgment only, no auto-trigger.)
