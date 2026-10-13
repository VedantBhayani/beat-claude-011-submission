# Evidence log

Tiers follow beat-claude `SCORING.md`: Tier 2 demo artifact, Tier 3 code, prompts, inputs and logs a reviewer can trace, Tier 4 measured before/after, Tier 5 independent verification. Every number carries a source label.

**LCF** = https://github.com/VedantBhayani/linkedin-carousel-factory. Run links have the form `https://github.com/VedantBhayani/linkedin-carousel-factory/actions/runs/<id>`.

## Verified on 2026-10-13

| # | Claim | Label | Tier | Where to check |
|---|---|---|---|---|
| 1 | The worker created a Buffer draft in production at least once: run 37763450842 (2026-10-08 10:26 UTC) logged `"outcome":"draft_created"`. That log does not name the job; the job id has been logged since LCF commit 5d2c6bc. | [Observed] | Tier 3 | `evidence/actions-logs/run-37763450842.log` |
| 2 | **Bad output from the AI step.** The v1 carousel prompt kept a leading space in `" live-001"` and restored it after Drive trimmed the filename. The worker rejected the job twice: `invalid_filename`, then `jobid_mismatch`. | [Observed] | Tier 3 | https://github.com/VedantBhayani/linkedin-carousel-factory/tree/master/docs/evidence/ai-traces, `evidence/actions-logs/run-37735537490.log`, `run-37735894619.log` |
| 3 | Fix for #2: the worker compares trimmed ids and rejects ids unsafe for storage paths (LCF commit 8822c4b, `tests/worker/job-id-contract.test.js`). The v2 prompts trim and validate the id. | [Observed] | Tier 3 | LCF commit 8822c4b; https://github.com/VedantBhayani/linkedin-carousel-factory/tree/master/prompts |
| 4 | Before LCF commit 0199033, every successful Buffer create was reported as "unknown Buffer error": the mutation did not request `__typename`, but the code required it. Runs 37736480784 and 37762672473 logged exactly that. | [Observed] | Tier 3 | `evidence/actions-logs/run-37736480784.log`, `run-37762672473.log`; https://github.com/VedantBhayani/linkedin-carousel-factory/commit/0199033 |
| 5 | Those runs created real duplicate `live-001` drafts in Buffer. The Sheet stores one draft id, `6ac76e61deb9a4752cef8bdc`. | Unverified until a Buffer screenshot exists | — | `evidence/e2e-run/` (to add) |
| 6 | `external:unknown` on launch-video-001: the Cloudinary Admin API rejected with a plain object (`thrown=Object message=`), and the 500-character log cut hid its HTTP status. | [Observed] | Tier 3 | `evidence/actions-logs/run-37660483439.log`; LCF `docs/evidence/fix6-external-unknown.md` |
| 7 | Failed jobs were invisible in Actions. Of 32 runs through 2026-10-08, 26 were green, and in 19 of those the worker had logged `outcome=failed`. The 6 non-green runs never ran the worker. Since LCF commit 5d2c6bc, a failed job exits 1. | [Observed] | Tier 3 | `evidence/actions-logs/run-list-through-2026-10-08.log`, `worker-outcomes-in-green-runs.log` |
| 8 | Wrong-row risk: without a stored draft id, two rows with the same post text could adopt each other's draft. A regression test reproduced it before the fix (job B adopted `draft-of-a`). Found in review and testing, not seen in production. The live Sheet does contain two rows (test-003, live-001) with identical post text. | [Observed] | Tier 3 | LCF `tests/worker/duplicate-post-text.test.js`, `docs/adr/0001-buffer-draft-reconciliation.md`; `evidence/sheet/sheet-before-2026-10-13.csv` (same fingerprint `71e93a3c`) |
| 9 | The overflow check missed text spilling out of its element: an 80-character word rendered to x=6753 px on a 1080 px slide and passed. Fixed in LCF commit 9a77d97. | [Observed] | Tier 3 | LCF `tests/render.test.js` ("overflowing copy…") |
| 10 | Test suite: 144 passed at baseline, 201 after the fixes, and 203 at the current code (local, Windows, Node 24). | [Observed] | Tier 3 | `evidence/tests/baseline-test-run.log`, `after-test-run.log`, `test-run-50088e9.log` |
| 11 | `buffer_draft_url` is empty for a created draft: `live-001` is `draft_created` with a draft id and no URL. | [Observed] | Tier 3 | `evidence/sheet/sheet-before-2026-10-13.csv` |

## To fill from the end-to-end run (`RUN_CHECKLIST.md`)

| # | Claim | Label | Tier | Where to check |
|---|---|---|---|---|
| 12 | Normal case reaches `draft_created`, and the draft belongs to its own row | | | `evidence/e2e-run/normal/buffer-draft.png`, `actions-run-<id>.log` |
| 13 | Messy case: trimmed id used, and the hidden instruction treated as post text | | | `evidence/e2e-run/messy/brief-report.log`, `buffer-draft.png` |
| 14 | Should-stop case: `needs_human`, with no Drive file created | | | `evidence/e2e-run/should-stop/brief-report.log`, `evidence/sheet/sheet-after.csv` |
| 15 | Manual minutes per carousel (you, timed) | [Observed] | Tier 4 | `evidence/e2e-run/before-after/timings.csv` |
| 16 | Agent minutes per carousel (task report and Actions timestamps) | [Observed] | Tier 4 | `evidence/e2e-run/before-after/timings.csv` |
| 17 | Weekly volume or time saved | [Estimated] or [Assumed], with the arithmetic | | |
