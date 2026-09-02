---
name: outreach-message-composer
description: >-
  Drafts TikTok Shop creator outreach copy across three channels — DM / Target
  Collaboration invite / email — tuning tone by tier, localizing to brand voice,
  forcing a compliance scan, and attaching anti-spam params. Use when the user wants
  to draft a DM, a Target Collaboration invite, an email, or a follow-up. Affiliate (分销) context.
  Chinese triggers: 起草 DM / 写触达文案 / TC 邀请语 / 写邮件 / follow-up / 邀约文案.
---

# Outreach Message Composer · 多渠道触达文案

**Role**: produce ready-to-use outreach copy (DM / Target Collaboration / email). **Only writes copy + channel/param suggestions**; fit, tier, and commission compliance go to creator-fit-scoring / creator-tier-resolver / offer-policy-checker.

## Output language
Write every output in the **merchant's working language**, using that market's native seller terminology:
- **US Local sellers → English**: tier / all-in take-rate / outreach waterfall / Target Collaboration / DM / sample …
- **China POP sellers → 中文**: 分级 / 真实抽成 / 触达阶梯 / 定向邀约 / 私信 / 寄样 …
Tool names (e.g. `send_email`, `create_dm_automation`) stay identical in both languages. If unsure which market, ask once before producing output.

## When to use / not use
- Use: write a cold DM, a Target Collaboration invite, an email, a follow-up.
- Don't use: only scoring / tier judgment / data lookup.

## Inputs (ask if missing)
1. Target creator tier + channel (DM / TC / email / combo).
2. Product (name/category/price; must be ACTIVATE).
3. Scenario: cold / follow-up / negotiation reply.
4. Optional: proposed commission (if the copy must mention it).

## Steps (in order)
1. Read brand voice (project `Knowledge/brand-voice`; default "warm but professional, no emoji" if absent) and the current hero product + angle (`sku-catalog-snapshot`, if present); **before contacting, compare against `Knowledge/do-not-contact` and drop matches (before ranking)**.
2. **Pick the channel** (per the Cheat Sheet's outreach waterfall): warm / existing / customer-type → prefer Target Collaboration; cold T2/T3 → DM first, asking permission for "a quick chat"; cold VIP/T1, or DM with no reply → email (consider enrichment only for high value).
3. **Set tone & length by tier**: higher tier = more personalized, more creative latitude; lower tier = concise, one clear value point. **Do not drop a commission number early in a cold DM** (hold it until they reply — acceptance is higher).
4. **Draft 3 versions** (1 primary + 2 alternates, differentiated tone); email must carry the value prop + compliance requirements (#ad / branded-content).
5. **Force brand-safety-compliance**: any version that hits a block is rewritten, not output, with the violated rule noted.
6. **If commission is mentioned** → run offer-policy-checker; if over the cap, flag a red warning at the top of the copy.
7. **Attach anti-spam params** (per the Cheat Sheet): when building a DM/combo automation, include stop_on_reply + skip_creators_with_prior_replies + skip_messaged_within_days; don't re-contact within the cross-channel cooldown.
8. **Naming suggestion**: if the copy lands in an automation name, give a **short English/alphanumeric** name (per current naming rules); if non-ASCII is present, give a deterministic fallback (pinyin / short slug, then truncate) — never let Chinese end up in a name.

## Sibling skills
- brand-safety-compliance (mandatory); offer-policy-checker (when commission is involved).

## ⛔ Do NOT
- Never use emoji (unless the brand voice explicitly asks); never promise exclusivity / guaranteed income / guaranteed payment (unless already contracted).
- Never pretend "we talked" in a follow-up (no real interaction); never impersonate the brand founder.
- Never drop a commission number early in a cold DM; never write over-long copy (long DMs tank open rate).
- Never auto-send — produce copy only; sending is a 🔴 action needing human confirm.

## 📌 Strategy dependency (anti-staleness)
The outreach waterfall, anti-spam defaults, naming rules, and compliance red lines all come from the Action Workspace Doctrine → Key-Rules Cheat Sheet; brand voice comes from `Knowledge/brand-voice`. If an example here conflicts with current policy, the Cheat Sheet wins. Use the email tools that actually exist (send_email / reply_email, etc.); only call tools that exist.

## Brand knowledge needed
The essentials + outreach + campaign sections (voice, banned words, CTA, hero product).

## Output format
Render the copy in the merchant's working language; the template below shows the structure.
```
⚠️[only if a violation] BRAND-SAFETY: version A matched "guaranteed", rewritten
⚠️[only if over cap] OFFER: commission X% over the T2 cap — see offer-policy-checker
[Primary · A] (tone tag / channel)
<copy>
— words N | variable {{nickname}} | compliance ✅ | channel DM/TC/email
[Alternate · B] (more direct) …   [Alternate · C] (softer) …
[Launch suggestion] channel=… · anti-spam: stop_on_reply + skip_messaged_within_days=<see Cheat Sheet> · automation name (English): <e.g. dm_beauty_v1>
[Usage] A/B test, lead with A; if the audience skews young, push C
```

## Example
Input: cold, T2 creator, a skincare product, via DM. Output:
Primary A (warm-professional, no emoji, **no commission number**, with #ad / branded-content) + alternates B/C. Launch suggestion: channel DM, with stop_on_reply + skip_messaged_within_days (value in the Cheat Sheet), automation name `dm_skin_v1`. Passed the compliance scan ✅; sending is a 🔴 action needing your confirm.
