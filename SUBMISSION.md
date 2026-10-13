# Agentify It: LinkedIn carousel factory

Brief version: 2026-07 (Intern 011, Option D)

> Lines starting with ✍️ are notes for you. Replace them with your own words and delete the notes. The scoring guide compares submissions against a fresh AI answer, so the opinions and voice here must be yours. The facts you need are in `EVIDENCE_LOG.md`. Limit: 4 pages.

## Part 1: Domain

✍️ What area do you want to work in, and why? (direct, 2–3 sentences)

✍️ One specific thing about Single Grain that makes you want to intern there.

✍️ The most impressive thing you have built or shipped, with a link a reviewer can verify. It can be this prototype.

## Part 2: The workflow and the agent

### The manual process today (Option D needs this)
✍️ Describe how a LinkedIn carousel is made by hand: write post → design 8 slides → export PDF → upload to the scheduler. Give the time per carousel from your own timing ([Observed], evidence #14) and how often you do it (label it).

### What the agent does, step by step
✍️ In your words, using the product README's pipeline: queued row → brief task → carousel task → GitHub worker → Cloudinary → Buffer draft.

### Architecture
✍️ Tools and why: Google Sheets as the queue, Drive for JSON, scheduled tasks for the AI step, GitHub Actions + Playwright for deterministic rendering, Cloudinary for immutable assets, the Buffer GraphQL API for drafts. Say what each part is NOT trusted to do.

### Three test cases
✍️ Normal, messy, should-stop: the input, what happened, and the evidence link for each (evidence #11–13).

### One bad output
✍️ Recommended: evidence #2–3, the AI step re-adding a leading space to the job id, which the worker rejected twice. Cover how you found it (Actions log), what you changed (worker trims and validates ids; prompt v2 rule), and how a test now guards it.

## Part 3: The meta question
✍️ 3–5 sentences: the most tedious thing you did last month and how you would agentify it.

---

## Packet

1. **Written answer**: Parts 1–3 above.
2. **Operating artifact**: https://github.com/VedantBhayani/linkedin-carousel-factory (README has run instructions; `prompts/` has the AI step; `docs/evidence/` has logs and traces), plus `evidence/` in this repo.
3. **Evidence log**: `EVIDENCE_LOG.md`.
4. **Number source labels**: every number above carries [Observed], [Estimated], [Benchmarked] or [Assumed].
5. **AI usage disclosure**: ✍️ name every tool and what it did. From the repo history that includes:
   - the scheduled-task model that runs the prompts (confirm which)
   - Claude (web) for drafting the prompts
   - Codex (decision tickets are assigned to `codex`; there is a `codex/` branch)
   - Claude Code for the 2026-10-13 fixes

   Then say what you changed by hand and what you checked yourself.
6. **What breaks it**: ✍️ your judgment. Possible material from the product README "Known limits":
   - manual trigger
   - caption-only Buffer reconciliation
   - size-only asset reuse check
   - one style
   - rows stalled in ChatGPT statuses
   - the 30-word and 2-hour thresholds are assumed, not measured
7. **What stays human**: ✍️ your judgment. Material from the README:
   - approving a row by setting `queued`
   - resolving `needs_human` rows
   - publishing from Buffer (drafts only)
   - choosing between duplicate drafts
