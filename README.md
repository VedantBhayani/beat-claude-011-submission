# Beat Claude 011 submission (Option D)

Status: **draft, not submitted.** Submit once, at singlegrain.com/careers, after `RUN_CHECKLIST.md` is complete.

- **Brief:** `challenges/intern-011/brief.md` in https://github.com/ericosiu/beat-claude, brief version 2026-07. [Observed] The file was last changed 2026-07-09, checked 2026-10-13. Re-read it and `SCORING.md` before submitting.
- **Prototype:** https://github.com/VedantBhayani/linkedin-carousel-factory (separate repo).
- **This repo** holds only the packet: the written answer, the evidence log, and the evidence files.

| Path | What |
|---|---|
| `SUBMISSION.md` | Written answer (Parts 1–3) and the 7 packet sections. Max 4 pages. |
| `EVIDENCE_LOG.md` | Each major claim with a source label, a tier, and a file or link to check it. |
| `RUN_CHECKLIST.md` | The end-to-end run for the three test cases, and what to save. |
| `evidence/actions-logs/` | Worker-step excerpts from the GitHub Actions runs cited, the run list, and the worker outcome inside each green run. |
| `evidence/sheet/` | Redacted Sheet export (post text replaced by word count and a fingerprint; Drive links removed). |
| `evidence/tests/` | Full `npm test` output: baseline, after the fixes, and the current code. |
| `evidence/e2e-run/` | Filled during the end-to-end run. |

Notes on the evidence:
- **Masked text in logs:** GitHub masks secret values as `***`. In these logs `{` and `}` also appear as `***`. That is probably because the service-account JSON secret spans several lines and GitHub masks each line, including the lone braces. This is inferred, not verified.
- **Why the excerpts look different:** the excerpts are copied from `gh run view <id> --log`. Older runs label every step "UNKNOWN STEP", so the outcome summary was taken from the whole log, excluding test-runner output.

Pre-screen (from a checkout of beat-claude):

```
python3 scripts/validate_submission.py /path/to/beat-claude-011-submission
```
