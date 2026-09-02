---
name: creator-tier-resolver
description: >-
  Maps a creator's evidence to a collaboration tier (Open / T1 / T2 / T3·VIP) and
  judges whether they hit the Open→Target graduation line. Use when the user needs
  to decide which tier a creator belongs to, whether to graduate them, or what
  priority to give them; also called by creator-fit-scoring and offer-policy-checker.
  Affiliate (分销) context.
  Chinese triggers: 判定分级 / 该不该毕业 / 用什么优先级对待.
---

# Creator Tier Resolver · 达人分级判定

**Role**: answer only "which tier this creator should be in, and whether they hit the graduation line". **No scoring** (that's creator-fit-scoring), **no commission validation** (that's offer-policy-checker), **no copy**.

## Output language
Write every output in the **merchant's working language**, using that market's native seller terminology:
- **US Local sellers → English**: tier / all-in take-rate / outreach waterfall / Target Collaboration / DM / sample / graduation …
- **China POP sellers → 中文**: 分级 / 真实抽成 / 触达阶梯 / 定向邀约 / 私信 / 寄样 / 毕业 …
Tool names stay identical in both languages. If unsure which market, ask once before producing output.

## When to use / not use
- Use: set a creator's priority tier, judge Open→Target graduation, fix the tier for downstream offer/outreach.
- Don't use: fit not yet judged (run creator-fit-scoring first).

## Inputs
1. Creator evidence: conversion / GMV-per-view, orders, fulfillment, audience persona; follower count (reference only).
2. Optional: target product category (category affects tier economics — see the Key-Rules Cheat Sheet).

## Steps (in order)
1. **Hard-exclude check**: blocklist / explicit opt-out / risk-signal hit → "Excluded / do not invite".
2. **Conversion evidence is primary**: judge the tier against the Cheat Sheet's tier definitions. **Follower count is not the primary basis**; category-sensitive (the same numbers may land in a different tier per category, per the registry).
3. **Insufficient data or on a tier boundary**: take the **lower** tier (conservative) and flag "insufficient/borderline evidence, recommend human confirm or more data".
4. **Graduation check**: per the Cheat Sheet's graduation line (last-N-day GMV or count of converting videos), judge whether an Open creator should rise to Target; if they qualify → mark "recommend graduation + go via Target Collaboration invite".
5. Output tier + basis + next step (hand to offer-policy-checker for the offer, outreach-message-composer for the draft).

## Sibling skills
- Set/validate the offer for that tier → offer-policy-checker.
- Draft the matching outreach → outreach-message-composer.

## ⛔ Do NOT
- Never tier up or graduate on follower count alone.
- Never tier up because a creator "has potential / good content" — tier is evidence-driven, not a feeling.
- Never tier up when data is insufficient — take the lower tier and flag for more data.
- Never alter the tier definitions or graduation line yourself (they live in the Cheat Sheet).

## 📌 Strategy dependency (anti-staleness)
This skill embeds **no** tier thresholds, graduation line, commission numbers, or tool list. Tier definitions, the graduation line, and category-sensitivity rules all come from the Action Workspace Doctrine → Key-Rules Cheat Sheet; if an example here conflicts with current policy, the Cheat Sheet wins. Only call tools that actually exist.

## Output format
Render the report in the merchant's working language; the template below shows the structure.
```
🎚️ Tier: T2 (or Open/T1/T3·VIP/Excluded/insufficient-evidence)
[Basis] key conversion evidence… · audience fit… · checked against Cheat Sheet tier defs · no hard-exclude signal
[Borderline/insufficient] (if any) just short of the next tier / missing X data → recommend human confirm or more data
[Graduation] (Open creators) hit the registry graduation line? → recommend graduation via Target Collaboration / not yet
[Next] → offer-policy-checker for the offer; → outreach to draft (warm creators prefer Target Collaboration)
```

## Example
Input: a creator with steady repeat purchase, audience fit, mid followers. Output:
🎚️ Tier = **T2** (on conversion evidence, checked against the Cheat Sheet tier defs; not on followers). Did not reach a higher tier; if just short, flag "recommend more data / human confirm". Next → offer-policy-checker for the offer.
