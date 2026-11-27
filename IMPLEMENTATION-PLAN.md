# Hisaab: implementation plan

| | |
| --- | --- |
| Version described | 2.0.1.0, as built |
| Last revised | 27 Nov 2026 |

## Rebuild phases (2.0.0.0, 26 Nov 2026)

| Phase | What | Done when | Result |
| --- | --- | --- | --- |
| P0 | Vite, React, TypeScript, Tailwind, Zustand, Vitest, Playwright, theme button | The empty app builds; tests run with one command | Done |
| P1 | Ledger sums: balance, total, newest first, days since the last payment | The sample shop and a second, hand-checked shop pass in Vitest (`src/lib/ledger.test.ts`) | Done |
| P2 | Store and import: replace the shop, refuse a bad file | `src/store/store.test.ts`; `e2e/hooks.spec.ts` P2 | Done |
| P3 | Home: total, customer rows, the grader selectors | `e2e/hooks.spec.ts` P3 | Done |
| P4 | Customer page: add an entry, newest first | `e2e/hooks.spec.ts` P4 | Done |
| P5 | Reminder link and payment screenshot | `e2e/hooks.spec.ts` P5 | Done |
| P6 | Six bonus items: search, edit and delete, CSV, overdue, UPI link, Hindi and Telugu | `e2e/hooks.spec.ts` P6 | Done |
| P7 | First open: theme and navigation say what they did; a name with no match offers to add it | `e2e/hooks.spec.ts` P7 | Done |

At the end of P7: Vitest 10, Playwright 7, checker 23 PASS and 2 FAIL (no pay link by default; the screenshot
amount reader), estimate 95/100.

## Fix pass (2.0.1.0, 26 to 27 Nov 2026)

A read-only review of the rebuild found 12 defects. Each was fixed with a new test written first
(`src/lib/fixes.test.ts`, `src/store/quota.test.ts`, `e2e/fixes.spec.ts`).

| # | Fix |
| --- | --- |
| 1 | A pay link in every reminder for money owed; the demo UPI id until the shop sets its own |
| 2 | A ledger, language and theme saved by 1.x are carried over on first open |
| 3 | Saving an entry with a screenshot clears the form |
| 4 | Nothing is shown as saved that storage refused; the store rolls the change back |
| 5 | A screenshot fits inside 600 px on both sides and under 150,000 characters |
| 6 | Delete has Undo and no window dialog |
| 7 | An edit needs a date |
| 8 | Total and CSV use the same sign rule as the balance |
| 9 | Import reads an amount written as digits in a string, and a file with no entries list |
| 10 | Import keeps the order of the file for entries on the same date |
| 11 | Reminder text follows the chosen language |
| 12 | Fonts come with the site; a new service worker replaces the 1.x one and clears its caches |

One earlier assertion was changed: `e2e/hooks.spec.ts` P6 had expected no pay link by default, against the checker.

Result: Vitest 32, Playwright 22 (local). Deployed 27 Nov 2026. Checker on the live link, seeds 4242 and 90210:
25 PASS, 1 FAIL (the screenshot amount reader, not built), estimate 100/100.

## Not planned in 2.0

The features of 1.x that were not rebuilt are listed in `PRD.md`, section 7. No date is set for any of them.

## Earlier plan (1.x)

Versions 1.0.0.0 to 1.2.5.0 were built in slices as plain scripts and add-on files between 20 and 25 Nov 2026.
The results of those versions are in `REPORT.md`. That code no longer runs.
