# Evidence log

Tiers follow beat-claude `SCORING.md`: 2 demo artifact, 3 code/prompts/inputs/logs a reviewer can trace, 4 measured before/after, 5 independent verification. Every number carries a source label.

Repo shorthand: **LCF** = https://github.com/VedantBhayani/linkedin-carousel-factory

## Already verified (2026-10-13)

| # | Claim | Label | Tier | Where to check |
|---|---|---|---|---|
| 1 | The worker produced a Buffer draft in production at least once: run 37763450842 (2026-10-08 10:26 UTC) logged `"outcome":"draft_created"`. The log did not name the job; the job id is only logged since commit 5d2c6bc. | [Observed] | 3 | LCF Actions run 37763450842 log |
| 2 | **Bad output from the AI step.** The v1 carousel prompt kept a leading space in job id `" live-001"` and re-added it after Drive trimmed the filename. The worker rejected the job twice: run 37735537490 `invalid_filename`, run 37735894619 `jobid_mismatch`. | [Observed] | 3 | LCF `docs/evidence/ai-traces/`; Actions runs 37735537490, 37735894619 |
| 3 | Fix for #2: the worker compares trimmed ids and rejects unsafe ids (commit 8822c4b, tests `tests/worker/job-id-contract.test.js`). The v2 prompts trim and validate the id. | [Observed] | 3 | LCF commit 8822c4b; `prompts/*.v2.md` |
| 4 | Before commit 0199033, every successful Buffer create was reported as "unknown Buffer error". The mutation did not request `__typename`, but the code required it. Runs 37736480784 and 37762672473 logged that message. | [Observed] | 3 | LCF `git show 0199033`; runs 37736480784, 37762672473 |
| 5 | Those two runs created real duplicate drafts in Buffer. | [Unverified] until the Buffer screenshot exists | — | `evidence/bad-output/` (to add) |
| 6 | `external:unknown` on launch-video-001 came from the Cloudinary SDK rejecting with a plain object, which the worker could not read. The 500-character log cut hid the HTTP status. | [Observed] | 3 | LCF `docs/evidence/fix6-external-unknown.md`; run 37660483439 |
| 7 | All 30 workflow runs listed on 2026-10-13 were green, including runs whose job failed, because the worker exited 0. Fixed in 5d2c6bc. | [Observed] | 3 | `gh run list` on LCF; commit 5d2c6bc |
| 8 | Wrong-row risk: without a stored draft id, two rows with the same post text could adopt each other's Buffer draft. A regression test reproduced it before the fix (job B adopted `draft-of-a`). It was found in review and testing, not seen in production. | [Observed] in test | 3 | LCF `tests/worker/duplicate-post-text.test.js`; `docs/adr/0001-buffer-draft-reconciliation.md` |
| 9 | The overflow check missed text spilling out of its element. An 80-character word rendered to x=6753 px on a 1080 px slide and passed. Fixed in 9a77d97. | [Observed] locally | 3 | LCF `tests/render.test.js` ("overflowing copy…") |
| 10 | Test suite: 144 tests passed at baseline, 201 after the fixes (local, Windows, Node 24). | [Observed] | 3 | LCF `docs/evidence/baseline-test-run.txt`, `after-test-run.txt` |

## To fill from the end-to-end run (`RUN_CHECKLIST.md`)

| # | Claim | Label | Tier | Where to check |
|---|---|---|---|---|
| 11 | Normal case reaches `draft_created`, and the draft belongs to its own row | | | |
| 12 | Messy case: trimmed id used and the injected instruction ignored | | | |
| 13 | Should-stop case: `needs_human` with no Drive file | | | |
| 14 | Manual time per carousel (you, timed) | [Observed] | 4 | `evidence/before-after/` |
| 15 | Agent time per carousel (task and Actions timestamps) | [Observed] | 4 | `evidence/before-after/` |
| 16 | Weekly volume or time saved | label as [Estimated]/[Assumed] and show the arithmetic | | |
