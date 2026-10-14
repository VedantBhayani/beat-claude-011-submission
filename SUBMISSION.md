# Agentify It (Intern 011, Option D: own workflow): LinkedIn carousel factory

Brief version: 2026-07

> Lines marked ✍️ are for you. Replace each one with your own words, then delete the marker. Single Grain compares submissions against a fresh AI-generated answer, so the opinions and voice must be yours. Everything not marked ✍️ is a fact with a source in `EVIDENCE_LOG.md`; keep, trim or reword it. Limit: 4 pages for this file.

## Written answer

### Part 1: Domain
✍️ What area you want to work in, and why (2–3 direct sentences).

✍️ One specific thing about Single Grain that makes you want to intern there.

✍️ The most impressive thing you have built or shipped, with a link a reviewer can verify. It can be this prototype: https://github.com/VedantBhayani/linkedin-carousel-factory

### Part 2: The workflow

**The manual process.** ✍️ How you make a LinkedIn carousel by hand: write the post, design 8 slides, export the PDF, upload it to the scheduler. Give your timed number ([Observed], `evidence/e2e-run/before-after/`) and how often you do it (label it [Estimated] or [Assumed]).

**What the agent does.** A Google Sheet row is the job. Each step moves the row to a new status:

1. A person adds a post and sets `status = queued`.
2. Scheduled task 1 (https://github.com/VedantBhayani/linkedin-carousel-factory/blob/master/prompts/brief-builder.v2.md) writes an editorial brief to Drive, or stops the row as `needs_human`.
3. Scheduled task 2 (https://github.com/VedantBhayani/linkedin-carousel-factory/blob/master/prompts/carousel-json-builder.v2.md) writes the eight-slide JSON to Drive (`carousel_created`).
4. A GitHub Actions worker validates the JSON and renders it with Playwright/Chromium: a 1080×1350 PDF, a cover and 8 PNGs ([Observed], see below). It stores them in Cloudinary under content-hash IDs and creates an unpublished Buffer draft (`draft_created`).
5. A person reviews and publishes the draft in Buffer.

The output format is [Observed] in the test suite: 8 slides at 1080×1350, an 8-page PDF and a cover. See the "renders eight correctly sized slides" test in `evidence/tests/test-run-50088e9.log`.

✍️ One or two sentences on why the work is split this way: the AI writes the content, and code that can be checked does the rendering and API calls.

**Architecture.**

| Part | Does | Is not trusted to |
|---|---|---|
| Scheduled tasks | brief, slide copy, should-stop judgment | render, call Cloudinary or Buffer, or touch rows past `carousel_created` |
| Google Sheet | queue and status, the error a person reads | hold payloads |
| Drive | brief and carousel JSON | — |
| GitHub worker | validation, render, upload, draft, retries | editorial decisions |
| Cloudinary | immutable media at content-hash IDs | overwrite anything (`overwrite: false`) |
| Buffer | holds the draft | publish (the worker only saves drafts) |

✍️ Optional: one line on what you would change at 10× volume.

**Three test cases.** ✍️ Fill these from the end-to-end run (`RUN_CHECKLIST.md`): input, what happened, evidence path.
- Normal (`post-004`, a new post about onboarding emails): the scheduled tasks briefed it and wrote the slides. Preflight confirmed the Drive file, and the worker created the Buffer draft in a 76-second run [Observed]. Only that row was written. See evidence rows 12–16, `evidence/e2e-run/normal/`. ✍️ Add one sentence on what you checked by eye in the draft.
- Messy (` messy-001`: leading space in the id, plus an instruction hidden in a 53-word post): the tasks used the trimmed id in the JSON, kept the instruction as post text and kept it off the slides. The worker created the Buffer draft. The leading space is still in the cell, and no other row changed [Observed]. My first attempt held only the instruction (8 words), so it was stopped as too short, which was correct but didn't test the id. See evidence rows 18–22, `evidence/e2e-run/messy/`. ✍️ Add one sentence on what you checked by eye.
- Should-stop (`stop-001`: an exact copy of post-004): the brief task set `needs_human` with an error naming post-004, wrote no brief, and changed no other row [Observed]. See evidence row 17, `evidence/e2e-run/should-stop/`.

**Bad output, owned: the AI step broke its own job id.**
- **What happened.** [Observed] The `live-001` row's id was entered with a leading space. The v1 carousel prompt kept `" live-001"` in the filename and the JSON, and when Drive trimmed the filename, the task restored the space and reported it as fixed (https://github.com/VedantBhayani/linkedin-carousel-factory/tree/master/docs/evidence/ai-traces).
- **What the worker did.** It trims ids, so it rejected the job twice: run 37735537490 (`invalid_filename`) and run 37735894619 (`jobid_mismatch`), saved in `evidence/actions-logs/`.
- **What changed.** The worker now compares trimmed ids and rejects ids that aren't safe for storage paths (a test now covers this). The v2 prompts trim and validate the id.
- ✍️ How you noticed it, and what you would watch for next.

✍️ Optional second bad output: [Observed] before commit 0199033, every successful Buffer create was reported as a failure, so retries likely made duplicate drafts. Only include this once your Buffer screenshot confirms the duplicates (evidence #5).

### Part 3: The meta question
✍️ 3–5 sentences: the most tedious, repetitive thing you did last month and how you would agentify it.

## Operating artifact
- **Prototype repo (public):** https://github.com/VedantBhayani/linkedin-carousel-factory. Its README covers the pipeline and commands, `prompts/` holds the AI step, and `docs/adr/` and `docs/evidence/` hold decisions and incident notes.
- **This packet's `evidence/`:**
  - Actions log excerpts
  - redacted Sheet export
  - test runs
  - the end-to-end run folders

  The prompts (v1 and v2) and the v1 run reports are committed in the prototype repo, under `prompts/` and `docs/evidence/ai-traces/`.

## Evidence log
Full table with tiers and a checkable source per claim: `EVIDENCE_LOG.md` (https://github.com/VedantBhayani/beat-claude-011-submission/blob/main/EVIDENCE_LOG.md).

| Claim | Tier | Proof (all [Observed] unless marked ✍️) |
|---|---|---|
| The AI step broke its own job id; the worker rejected it twice; now fixed | Tier 3 | `evidence/actions-logs/run-37735537490.log`, `run-37735894619.log`, LCF commit 8822c4b |
| Successful Buffer creates were misread as failures | Tier 3 | `evidence/actions-logs/run-37736480784.log`, LCF commit 0199033 |
| Failed jobs were invisible: green runs whose worker had failed | Tier 3 | `evidence/actions-logs/worker-outcomes-in-green-runs.log` |
| Wrong-row draft risk, reproduced by a regression test | Tier 3 | LCF `tests/worker/duplicate-post-text.test.js` |
| Three test cases run end to end: normal and messy reached a Buffer draft, should-stop stopped at the brief | Tier 3 | `evidence/e2e-run/`, `evidence/sheet/sheet-after-*.csv` |
| Manual vs agent time per carousel | ✍️ Tier 4 after the run | `evidence/e2e-run/before-after/timings.csv` |

## Number source labels
Every number in this packet is labeled [Observed], [Estimated], [Benchmarked] or [Assumed]. Numbers measured from logs, test output or the Sheet are [Observed] and point to a file in `evidence/`. Examples:
- [Observed] 19 runs that GitHub marked green had a worker that logged `outcome=failed` (`evidence/actions-logs/worker-outcomes-in-green-runs.log`).
- [Assumed] the 30-word minimum and 2-hour stall limit in the v2 prompts are starting values, not measured.

## AI usage disclosure
✍️ Name each tool, what it did, what you changed, and what you checked yourself. [Observed] from the repo history (https://github.com/VedantBhayani/linkedin-carousel-factory/commits/master):
- the model that runs the scheduled tasks (confirm which product and model)
- Claude (web) for drafting the prompts
- Codex (the decision tickets are assigned to `codex`; there is a `codex/` branch)
- Claude Code for the 2026-10-13 audit and fixes (commit trailers)

✍️ Known weak spots you want to own.

## Failure handling

### What breaks it
Known limits, from the product README:
- The worker runs only when someone dispatches it.
- Buffer has no idempotency key. An unrecorded draft is recognised only by its exact caption, so rows with duplicate text are stopped.
- When an upload finds an existing Cloudinary asset, only byte size is compared.
- There is one visual style.
- `buffer_draft_url` stays empty because Buffer returns no link for drafts. [Observed] The live Sheet shows it empty for `live-001` (`evidence/sheet/sheet-before-2026-10-13.csv`).

✍️ Add the failure you think matters most, and why.

### How failures are detected
- A failed job makes the Actions run red (exit 1).
- The log line names the job, stage and error code.
- The same code is written to the row's `error` cell.
- `mode: preflight` checks the Sheet read-only before a run.
- Content that overflows a slide fails validation and names the slide.

### What stays human
✍️ Your judgment. Material from the build:
- Approving a post by setting `queued`.
- Resolving `needs_human` rows: short, duplicate or unsourced posts, bad ids, duplicate Drive files, stalled rows.
- Choosing between duplicate Buffer drafts.
- Publishing from Buffer.

## Artifact access
- **No login needed, [Estimated] about 5 minutes:** clone the prototype repo, then run:
  ```
  npm ci
  npx playwright install chromium
  npm test
  CAROUSEL_INPUT=input/launch-video-001-carousel-v1.drive.json CAROUSEL_DIST=out npm run render
  ```
  The last command writes the HTML, the 8-page PDF, the cover and 8 PNGs to `out/`. Edit the JSON to try your own copy. `examples/launch-video-001/` has a committed render.
- **Sample data:**
  - `input/*.json` in the prototype repo
  - the redacted Sheet export in `evidence/sheet/`
  - the end-to-end run inputs in `evidence/e2e-run/`
- **The live pipeline** needs the owner's Google, Cloudinary, Buffer and scheduled-task accounts. Credentials are not shared; the logs in `evidence/` stand in for access. ✍️ Offer a live run on a new post during a walkthrough.
