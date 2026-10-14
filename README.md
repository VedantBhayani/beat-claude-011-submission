# Beat Claude, Intern 011 (Option D): LinkedIn carousel agent

Submission packet for Single Grain's [Intern 011 challenge](https://github.com/ericosiu/beat-claude/tree/main/challenges/intern-011), **brief version 2026-07**.

- **Prototype:** https://github.com/VedantBhayani/linkedin-carousel-factory, pinned at commit [`2b69966`](https://github.com/VedantBhayani/linkedin-carousel-factory/tree/2b6996650bd61c0002c5d0f3135772cbead14347).
- **Start here:** `SUBMISSION.md`, the written answer and the 7 packet sections (4 pages).

| Path | What |
|---|---|
| `SUBMISSION.md` | Written answer (Parts 1–3) and the 7 packet sections. |
| `EVIDENCE_LOG.md` | Each claim with a source label, a tier, and a file or link to check it. |
| `RUN_LOG.md` | How the three test cases were run, and what was not done or not verified. |
| `evidence/e2e-run/` | The three test cases: run logs, task reports, Buffer screenshots, the carousel JSON, task settings and timings. |
| `evidence/actions-logs/` | Excerpts from earlier GitHub Actions runs, including the failures cited, the run list, and the worker outcome inside each green run. |
| `evidence/sheet/` | Redacted Sheet exports: post text replaced by word count and a fingerprint, Drive links removed. |
| `evidence/tests/` | Full `npm test` output: baseline, after the fixes, and the current code. |

Notes on the evidence:
- **Masked text in logs:** GitHub masks secret values as `***`. In these logs `{` and `}` also appear as `***`. That is probably because the service-account JSON secret spans several lines and GitHub masks each line, including the lone braces. This is inferred, not verified.
- **Why the excerpts look different:** the excerpts are copied from `gh run view <id> --log`. Older runs label every step "UNKNOWN STEP", so the outcome summary was taken from the whole log, excluding test-runner output.
- **Redactions:** Drive links are removed from the Sheet exports and blacked out in the task-report screenshots. No secrets, API keys or other people's data are in this repo.

Pre-screen (from a checkout of beat-claude):

```
python3 scripts/validate_submission.py /path/to/beat-claude-011-submission
```
