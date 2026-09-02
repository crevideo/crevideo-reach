---
name: brand-safety-compliance
description: >-
  Scans outbound copy or creator-submitted content for 6 compliance red lines —
  medical/efficacy, exaggeration/absolute, beauty-permanence, financial/income,
  fake-scarcity, fake-rapport — and returns a block/warn verdict with fix suggestions.
  Use when the user asks to check, scan, review, or vet copy before sending, or to
  re-scan already-published content; also called by outreach-message-composer and
  reply-triage. Affiliate (分销) context.
  Chinese triggers: 审文案 / 合规扫描 / 这段话能不能发 / 复盘已发内容是否违规.
---

# Brand Safety & Compliance · 品牌安全合规

**Role**: run a compliance red-line scan on text and return block/warn + fix suggestions. This is a **mandatory pre-flight** for any outbound copy, and is also used to re-scan published content.

## Output language
Write every output in the **merchant's working language**, using that market's native seller terminology:
- **US Local sellers → English**: tier / all-in take-rate / outreach waterfall / Target Collaboration / DM / sample …
- **China POP sellers → 中文**: 分级 / 真实抽成 / 触达阶梯 / 定向邀约 / 私信 / 寄样 …
Tool names stay identical in both languages. If unsure which market, ask once before producing output.

## When to use / not use
- Use: scan copy before sending, re-scan today's confirmed content, vet a creator-submitted script/caption.
- Don't use: pure data analysis with no text.

## Inputs
1. The text to scan.
2. Scenario: `outreach` (our outbound, strictest) / `creator_submitted` (creator content, still strict if regulated) / `internal_draft` (internal draft, advisory only).

## Steps (in order)
1. Read brand/category-specific banned words (project `Knowledge/brand-compliance-additions`, if present); merge with the MUST-NOT red lines from the Key-Rules Cheat Sheet.
2. **Scan all 6 classes**, reporting each independently:
   - C1 Medical/efficacy (cure / heal / treat / prevent…, medical verbs)
   - C2 Exaggeration/absolute (100% / guaranteed / instant / permanent / miracle…)
   - C3 Beauty-permanence (permanently whitens / removes wrinkles forever…, unless backed by real clinical evidence)
   - C4 Financial/income promise (guaranteed income / earn $X…)
   - C5 Fake scarcity (only N left / a "limited" drop with no real inventory)
   - C6 Fake rapport (as we discussed / per our agreement, with no real prior interaction)
3. For each hit: class + matched phrase + context + severity (block/warn) + fix suggestion.
4. **Overall verdict**: any block → ❌ must rewrite; warn only → ⚠️ may proceed but advise a fix; none → ✅.
5. Reasonable carve-out: if the text is meta-discussion teaching creators to **avoid** these phrases, mark it false-positive and pass.

## Sibling skills
- Called by outreach-message-composer before a draft ships (a version that hits a block is rewritten, not output).
- Called by reply-triage before drafting a negotiation/reply.

## ⛔ Do NOT
- Never pass on semantic similarity alone — require a concrete phrase/pattern hit.
- Never soften a block into a warn.
- Never accept a "let it through just this once" — this skill is ground truth.
- Never pass because "the context looks fine" (context is lost once the copy spreads).

## 📌 Strategy dependency (anti-staleness)
The 6 classes are general compliance rules; **brand/category-specific banned words** live in `Knowledge/brand-compliance-additions`, and the **authoritative MUST/MUST-NOT list** lives in the Action Workspace Doctrine → Key-Rules Cheat Sheet. If an example here conflicts with current policy, the Cheat Sheet wins. Only call tools that actually exist.

## Output format
Render the report in the merchant's working language; the template below shows the structure.
```
✅ BRAND-SAFETY pass | scanned 6 classes (general + brand additions)
—— or ——
❌ Reject (block) | N hits
[1] C1 Medical·BLOCK matched "heals" context "…heals scars…" → suggest "improves the appearance of"
[2] C2 Exaggeration·BLOCK matched "100%" → suggest deleting
📋 Summary: must rewrite; issues cluster around efficacy claims.
—— or ——
⚠️ Warn | matched "limited time" → keep if there is a real deadline, else change to "currently inviting"
```

## Example
Input (draft DM): "This serum fades wrinkles in 7 days, 100% effective." Output:
❌ **Reject (block)**: hits C3 beauty-permanence ("fades wrinkles") + C2 absolute ("100%") → suggest "helps improve the look of fine lines", delete "100%". Rewrite before sending.
