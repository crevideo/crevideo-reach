# Crevideo Reach Plugin · Try-It Checklist for Operators

> 🇺🇸 **English (this page)** · 🇨🇳 [中文版](./skill-smoke-test-checklist.zh-CN.md)

> After installing the plugin, run through this checklist once end-to-end. 5–10 minutes is enough to know whether it can actually get work done.
> No technical knowledge required — just follow the "What you ask" and "What you should see" columns.

---

## I. Three things to confirm before starting

1. **Plugin installed** — In Claude.ai bottom-left → Customize → Plugins, you should see `Crevideo reach` with the toggle on (blue)
2. **MCP connected** — In Connectors, `crevideo-reach` should appear with status Connected (if Not connected, click to log in and authorize once)
3. **Open a new conversation** — Don't test in an old conversation; context bleed will pollute the test

> 💡 **Do I need to set up Knowledge files first?**
> No. Knowledge files (brand voice / SKU catalog / do-not-contact list) are "extra credit" — without them Claude falls back to generic judgment and still answers. Run through this checklist first; once it works, decide whether to attach Knowledge files in the Project to make answers more brand-aligned.

---

## II. 9 day-to-day scenarios · run them one at a time

### 1️⃣ Score a batch of creators

**You ask:**
> I just previewed a Target Collaboration and got 5 matched creators. Help me look at which ones are reliable and which not to send.

**Claude should:**
- Either ask you to paste the 5-creator list, or actively call preview_target_collab to fetch a sample
- Score **each one individually** (not a sweeping one-liner), with a clear "recommend outreach / don't outreach" verdict for each
- Explain why — follower count, GMV, post cadence, that kind of evidence

**Pass ✅:** Verdict on all 5 + concrete reason per creator (not "looks decent")
**Fail ❌:** Single vague conclusion; or fabricates 5 creator profiles out of thin air (meaning it didn't actually call any data)

---

### 2️⃣ Decide which tier a creator belongs in

**You ask:**
> This creator did $80K GMV last month, posted 12 videos in 30 days. By our rules, is this T1 or T2? Should they graduate from Open?

**Claude should:**
- Give a specific tier (one of Open / T1 / T2 / T3/VIP)
- Explain why they meet this tier and why not the next one
- Give a clear yes/no on graduation

**Pass ✅:** Specific tier + listed evidence
**Fail ❌:** Vague "it depends" / "could be T1 or T2"

---

### 3️⃣ Check whether a commission offer is acceptable

**You ask:**
> A creator wants 20% commission + free samples + $50 cash. Can I accept?

**Claude should:**
- Ask which product / which tier the creator is in (if you didn't say)
- Give a clear verdict: "accept / don't accept / counter at X%"
- List each check: is the commission within cap, what's the real take-rate, is the product in the right status

**Pass ✅:** Gets to "accept / don't / counter at X%" — actionable
**Fail ❌:** Just "be cautious" / "depends" filler

---

### 4️⃣ Draft a DM

**You ask:**
> Draft a DM to beauty creators inviting them to a T2 collaboration — polite but with conviction.

**Claude should:**
- Output one complete DM (at least 80+ words, directly usable)
- Mention "compliance scan passed" or actually scan for risk
- Add anti-spam parameter reminder (how to avoid the platform flagging it as spam)

**Pass ✅:** A draft you can paste and send + compliance and anti-spam noted
**Fail ❌:** Outputs "Dear creator, we hope you..." template (means brand voice was never read)

---

### 5️⃣ Test whether a line is sendable

**You ask:**
> I'm thinking of sending this line: "After 7 days with our product I lost 10 lbs, never bouncing back" — can I send it?

**Claude should:**
- Clearly say **don't send** (this is medical + exaggeration, double violation)
- Name the rules it breaks (medical / exaggeration / false promise)
- Offer a sendable **replacement**

**Pass ✅:** Clear rejection + listed reasons + replacement copy
**Fail ❌:** Answers "consider rephrasing carefully" (vague pass)

---

### 6️⃣ Triage today's replies

**You ask:**
> Today's inbox has new replies — sort which ones need immediate attention and which can wait.

**Claude should:**
- **Actually pull the inbox** (not fabricate replies)
- Bucket by priority: strong intent / negotiating terms / general inquiry / declined / noise
- Give "next step" recommendation per item

**Pass ✅:** Specific creator names + actual reply text (proves it called real data) + buckets
**Fail ❌:** Makes up "@user1 wants to collaborate, @user2 not interested" (fabricated)

> ⚠️ **Empty-account case:** If your account has no new replies today, Claude will tell you "inbox is empty" — that's normal, not a failure.

---

### 7️⃣ Review a completed automation

**You ask:**
> Last week's TC automation finished with 40 failures — help me figure out which can be recovered and which to blacklist.

**Claude should:**
- Ask which automation (or list them itself for you to pick)
- Pull actual failure reasons → group by reason
- Per group: recoverable → how to revise copy and try again; non-recoverable → add to blacklist

**Pass ✅:** Real failure-reason grouping + recoverable / non-recoverable each with a plan
**Fail ❌:** Vague "optimize the copy"

---

### 8️⃣ Monthly review

**You ask:**
> How was affiliate performance this month — give me a diagnosis + how to adjust next month.

**Claude should:**
- Pull all three reports (collaboration / product / shoppable-video)
- List key metrics + where the numbers turn
- Give concrete action recommendations — "double down on category X / cut product Y collaborations" — executable conclusions

**Pass ✅:** Specific numbers + specific next actions
**Fail ❌:** "Post more videos, recruit more creators" — correct but useless

---

### 9️⃣ Tool map (in case you forget what it can do)

**You ask:**
> I just installed this plugin — what can it do?

**Claude should:**
- Give a capability list grouped by business scenario: finding creators / outreach / DM / email / reports
- Note "the standard flow for creating a TC is..."

**Pass ✅:** Reasonable scenario groupings, not just tool-name dump
**Fail ❌:** Replies "I can help you with TikTok Shop tasks" filler

---

## III. End-to-end scenarios · real business flows (best at showing landed value)

### Scenario: from finding creators to outreach

**You ask:**
> Preview a batch of beauty-category creators, pick the reliable ones, and draft a DM inviting each to T2 collaboration.

**Process you should see:**
1. Claude previews matched creators (calls preview_target_collab)
2. Scores each, picks the reliable ones
3. Decides each one's tier
4. Drafts personalized DM per creator
5. Runs each draft through compliance scan

**Pass ✅:** All 5 steps complete, ends with a "creator + score + copy" table
**Fail ❌:** Breaks midway (e.g., skips scoring and jumps to drafts) or all drafts share one template

---

### Scenario: negotiating + replying

**You ask:**
> This creator replied wanting 25% commission. Can I accept? If yes, draft the reply.

**Process you should see:**
1. First, check whether 25% is compliant / acceptable
2. Decide "accept / counter / don't"
3. Based on that, draft the reply (accept → thank-you confirmation; counter → explain reason)
4. Run draft through compliance

**Pass ✅:** Clear decision + a draft you can paste and send
**Fail ❌:** Only answers "can be discussed" without producing the reply text

---

## IV. Score yourself after running

After 9 + 2 = 11 scenarios, you'll know whether this plugin is reliable.

- **9 scenarios + 2 chains all pass** → roll out to the team
- **8–10 pass** → use in a small group, log the failures and report
- **5–7 pass** → don't roll out; report the failed scenarios to engineering
- **Fewer than 5 pass** → install is wrong or environment broken — get engineering to debug

---

## V. When things misbehave

| Symptom | Who / what to do |
|---|---|
| Claude never calls MCP (fabricates every answer) | Check whether `crevideo-reach` in Connectors is Connected; if not, re-login |
| Calls but errors "Not logged in / 401" | OAuth expired — disconnect and reconnect in Connectors |
| Answers correctly but lacks "business terms" (tier, bucket, violation type) | Skill didn't trigger; possibly Claude.ai cache hasn't refreshed — click "Check for updates" on the marketplace chip |
| Installed but nothing appears | Confirm plugin toggle is on (blue), restart the Claude.ai tab |
| Data is obviously wrong (wrong numbers, non-existent creators) | Take a screenshot + the original prompt, send to engineering (don't rephrase the question to work around it) |

---

## VI. Log your test results (screenshot it and send to the team)

```
Tester: __________
Date: __________
Plugin version: __________ (see the Plugins page)

9 single-skill scenarios: ___ / 9 passed
2 chain scenarios: ___ / 2 passed

Failures:
  □ #X — Symptom: __________
  □ #X — Symptom: __________

Score (out of 10): __________
Roll out to team: ☐ Roll out now  ☐ Small group first  ☐ Hold
```

---

## About Knowledge files (advanced — does not affect the first run)

Once this checklist runs cleanly and you feel the plugin is useful, you can attach 4 Knowledge files to the Cowork Project to make Claude's answers more brand-aligned:

| File | Content |
|---|---|
| `brand-voice` | Brand voice, target audience, audiences to avoid |
| `sku-catalog-snapshot` | This-season featured products + target creator profile |
| `do-not-contact` | Blacklist / creators still in cooldown / category-wide exclusions |
| `brand-compliance-additions` | Category-specific forbidden words (different for supplements / cosmetics / etc.) |

After attaching, run the checklist again — you should feel that "Claude's recommendations sound more like our own people wrote them".
