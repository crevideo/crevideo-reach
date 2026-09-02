---
name: offer-policy-checker
description: >-
  Validates a proposed commission/offer/outbound action against policy: within the
  tier's commission cap, all-in take-rate under the gate, product ACTIVATE, anti-spam
  params present — returning pass / warn / block + fixes. Use when setting, changing,
  or negotiating commission, deciding whether to accept a creator's counter-offer, or
  running a pre-launch compliance check; also called by outreach-message-composer and
  reply-triage. Affiliate (分销) context.
  Chinese triggers: 设置佣金 / 接受反价 / 发起前合规检查.
---

# Offer Policy Checker · 报价与合规校验

**Role**: a pass/warn/block **checker**, not a policy database. It judges whether an offer/outbound crosses a line and gives fixes. **Does not set tier** (creator-tier-resolver), **does not write copy**.

## Output language
Write every output in the **merchant's working language**, using that market's native seller terminology:
- **US Local sellers → English**: tier / all-in take-rate / outreach waterfall / Target Collaboration / DM / sample …
- **China POP sellers → 中文**: 分级 / 真实抽成 / 触达阶梯 / 定向邀约 / 私信 / 寄样 …
Tool names stay identical in both languages. If unsure which market, ask once before producing output.

## When to use / not use
- Use: validate a proposed commission, a creator counter-offer, a batch flat commission; the pre-send "does this fit policy?" check.
- Don't use: tier not yet decided (run creator-tier-resolver first).

## Inputs
1. Proposed commission/offer + the creator's tier (Open/T1/T2/T3).
2. Scenario: first offer / counter-offer / batch.
3. Optional: product (to check ACTIVATE), intended channel + anti-spam params.

## Steps (in order)
1. **Tier commission-cap check** (numbers from the Cheat Sheet): within range → pass; over default but under cap → warn (needs explicit human confirm); over cap → reject (must lower the price or re-judge the tier).
2. **All-in take-rate gate**: estimate all-in take-rate using **the Cheat Sheet's exact formula — do not reconstruct it from memory**. (Shape per the Doctrine: platform commission% + effective creator commission [listed ÷ (1 − return rate)] + per-order fulfillment ÷ AOV; landed cost/duty, samples, sales tax, and return-handling costs are **NOT** in it.) Write N/A for any missing item, do not fabricate. Over the Cheat Sheet's all-in take-rate ceiling → mark "needs intervention" (suggest lower commission / raise AOV·bundle / different product / lower-cost channel); **never pass on "high GMV".**
3. **Eligibility check**: product must be ACTIVATE; in batch, **every** creator in the pool must pass (else split the pool).
4. **Anti-spam param check** (outbound): confirm stop_on_reply / skip_creators_with_prior_replies / skip_messaged_within_days per the Cheat Sheet, plus the cross-channel cooldown. Flag any missing.
5. Output the verdict + fixes (do not decide acceptance of a counter-offer for the user).

## Sibling skills
- Tier undecided → creator-tier-resolver.
- After it passes, draft copy → outreach-message-composer.

## ⛔ Do NOT
- Never pass an offer over the tier cap or over the all-in take-rate ceiling (even "a competitor pays more / special case") — raise the tier or change the Cheat Sheet first.
- Never auto-accept a creator counter-offer — give the verdict only, wait for a human.
- Never apply a high tier's cap to a data-insufficient creator — treat conservatively.
- Never judge a batch on an "average tier".

## 📌 Strategy dependency (anti-staleness)
This skill embeds **no** commission caps, all-in take-rate **formula or** thresholds, anti-spam defaults, or tool list — all come from the Action Workspace Doctrine → Key-Rules Cheat Sheet; if an example here conflicts with current policy, the Cheat Sheet wins. Only call tools that actually exist.

## Output format
Render the report in the merchant's working language; the template below shows the structure.
```
✅ Pass | tier T2 | proposal in range | all-in take-rate est ≤ ceiling | product ACTIVATE | anti-spam params present
⚠️ Warn | over default, under cap +X% | needs your explicit "over-default" confirm | all-in take-rate est = …
❌ Reject | over tier cap +X% or all-in take-rate est > ceiling
  Options: (1) lower into range [recommended] (2) if evidence supports a higher tier → re-judge tier (3) change the Cheat Sheet (strategic, needs the lead)
(missing) an all-in take-rate item = N/A → recommend more data; anti-spam params missing → recommend adding skip_messaged_within_days etc.
```

## Example
Input: a T2 creator, proposed commission above that tier's default. Output:
⚠️ **Warn** — over default, under cap (exact cap in the Cheat Sheet), needs your explicit "over-default" confirm; all-in take-rate est passes the gate, product ACTIVATE, anti-spam params present. Options: (1) lower to within default [recommended] (2) if evidence supports a higher tier → re-judge tier. Won't decide for you.
