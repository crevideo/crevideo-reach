---
name: winback-and-pruning
description: >-
  Analyzes a completed/failed/paused automation's task_results, buckets them by
  status×reason, separates recoverable cohorts from terminal ones, and produces a
  win-back plan or a prune/blocklist recommendation. Use when the user wants a
  post-mortem, failure analysis, win-back, "how is this automation doing", or how to
  handle silent creators. Affiliate (分销) context.
  Chinese triggers: 复盘 / 失败分析 / winback / 唤回 / 这条自动化表现如何 / 沉默达人怎么处理.
---

# Winback & Pruning · 唤回与汰换

**Role**: bucket one automation's per-creator results, separate "recoverable vs terminal", and give a win-back plan or a prune/blocklist recommendation. **Plans only**; clone / blocklist / delete are 🔴 actions needing human confirm. **Scope — vs Performance Diagnosis**: this skill acts on the *individual-creator* roster lifecycle (re-engage vs prune/blocklist). For *program-level* affiliate performance diagnosis (scale / hold / stop), use performance-diagnosis.

## Output language
Write every output in the **merchant's working language**, using that market's native seller terminology:
- **US Local sellers → English**: tier / all-in take-rate / Target Collaboration / DM / sample / win-back / prune …
- **China POP sellers → 中文**: 分级 / 真实抽成 / 定向邀约 / 私信 / 寄样 / 唤回 / 汰换 …
Tool names (e.g. `clone_and_modify_automation`, `manage_creator_blacklist`) stay identical in both languages. If unsure which market, ask once before producing output.

## When to use / not use
- Use: post-mortem on an automation's results, handling post-sample silence / non-replies, deciding whether to re-run or blocklist.
- Don't use: live new replies (→ reply-triage); overall reporting (→ performance-diagnosis).

## Inputs
1. automation_id (completed/failed/paused).
2. Goal: (a) results only (b) draft a win-back plan (c) draft a plan then await a clone confirm.

## Steps (in order)
1. get_automation_task_results, paginate to the full set (if capped, say "analyzing in batches").
2. **Bucket by status × reason**:
   - A Success-replied (already converting, don't disturb)
   - B Success-no-reply (❓ depends on copy/timing)
   - C Fail-recoverable (inbox full / rate-limited / transient network → ✅ recoverable)
   - D Fail-terminal (invalid email / already opted-out / blocklisted → ❌ not recoverable)
   - E Fail-unknown (sample 5 manually to find the reason)
3. **Compute ratios**: acceptance / reply / failure / recoverable-failure share.
4. **Diagnose root cause**: high failure with many D → data quality; many C → timing; low failure but low acceptance → fit mismatch; B-dominant → copy or timing.
5. **Win-back plan** (if wanted):
   - C bucket → clone the original as a **DM-only** run, delayed a few days, copy shortened to a "second attempt", with skip_creators_with_prior_replies + skip_messaged_within_days (per the Cheat Sheet); **acknowledge the prior contact** (honest copy converts better) — never pretend it's a new invite.
   - B bucket → follow up a few days later with a new angle.
   - D bucket → **never** win back; list for blocklist (manage_creator_blacklist needs confirm + reason code + the **record id**, not user_id).
   - E bucket → sample 5 manually.
   - The clone's first_count **must not exceed** the original (win-back is a look-back, not scaling); automation name in **short English**.
6. Optionally feed creators that are "consistently valuable but didn't convert this time" into creator-fit-scoring's lookalike expansion.

## Sibling skills
- Draft win-back copy → outreach-message-composer; lookalike expansion → creator-fit-scoring.

## ⛔ Do NOT
- Never win back the D bucket (invalid/opt-out) (= spam); never "re-blast" the A bucket (already replied).
- Never auto clone/blocklist/delete — plans only, await confirm.
- Never win back / re-contact a creator on the **backend blocklist** or project `Knowledge/do-not-contact` (incl. cooldown-not-elapsed) — exclude both layers before proposing a win-back list.
- Never set the win-back first_count larger than the original; never pretend a win-back is a new invite.
- Never fabricate bucket ratios; mark missing data N/A.

## 📌 Strategy dependency (anti-staleness)
Dependency file: read project `Knowledge/do-not-contact` for exclusion before a win-back. Anti-spam defaults, naming rules, blocklist reason codes, and industry baselines all come from the Action Workspace Doctrine → Key-Rules Cheat Sheet; if an example here conflicts with current policy, the Cheat Sheet wins. Only call tools that exist (get_automation_task_results / clone_and_modify_automation / manage_creator_blacklist, etc.).

## Output format
Render the report in the merchant's working language; the template below shows the structure.
```
📊 Automation <name> results analysis
[Ratios] acceptance X% (baseline in registry) · reply Y% · failure Z% · recoverable-failure W%
[Buckets] A:..|B:..|C:..(recoverable✅)|D:..(terminal❌)|E:..(unknown⚠️)
[Diagnosis] 🔍 main cause = timing/data/fit/copy → one-line recommendation
[Win-back plan] (if wanted)
 C bucket n → clone DM-only, overrides: name=<English, e.g. winback_v1> first_count=n (≤ original) message="short second attempt that acknowledges prior contact" + anti-spam params → awaiting your "confirm clone"
 B bucket → new angle in a few days   D bucket → list for blocklist confirm (record id + reason code)   E bucket → sample 5 manually
```

## Example
Input: one completed DM automation's results. Output:
Ratios (baseline in the Cheat Sheet); buckets A replied / B no-reply / C recoverable (rate-limited → recoverable) / D terminal (invalid email → not recoverable). Win-back plan: for the C bucket, clone a **DM-only** run, delayed a few days, a short "acknowledge prior contact" second attempt, first_count ≤ original, with anti-spam params → awaiting your "confirm clone"; list the D bucket for blocklist confirm.
