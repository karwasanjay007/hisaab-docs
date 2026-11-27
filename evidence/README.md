# Hisaab: evidence index

Each file here is the saved output of a test run. Only the files in the first table are results of version
2.0.1.0, the build at the live link since 27 Nov 2026. Every other file is a result of a 1.x version and is
kept as the record; 1.x no longer runs, so those results say nothing about 2.0.1.0.

## Results of 2.0.1.0

| File | What ran | Result |
| --- | --- | --- |
| `last-check-live-2.0.1.0.txt` | The checker `tests/check.mjs` on the live link, seed 4242, 27 Nov 2026 | 25 PASS, 1 FAIL; estimate 100/100 |
| `last-check-live-2.0.1.0-seed-90210.txt` | The same checker on the live link, seed 90210, 27 Nov 2026 | 25 PASS, 1 FAIL; estimate 100/100 |

The one FAIL in both is `[9-bonus OCR]`: reading the amount from a screenshot is not built in 2.0.1.0.
The local Vitest and Playwright runs of 2.0.1.0 have no file here; their counts are in `REPORT.md`.

## Results of 1.x, kept as the record

| File | Version it was run on |
| --- | --- |
| `last-check-live.txt` | 1.2.5.0, the checker on the live link, seed 4242. Not a result of 2.0.1.0 |
| `last-assist-live.txt` | 1.2.5.0 |
| `release-1-0-0-0.md` | 1.0.0.0, the release note of the first baseline |
| Every other `last-*.txt` file | A version from 1.0.0.0 to 1.2.5.0 |

The test scripts behind the 1.x files (`tests/*.mjs` other than `check.mjs`) target pages that 2.0.1.0 does not
have. They were not run on 2.0.1.0.
