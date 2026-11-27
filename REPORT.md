# Hisaab: report

| | |
| --- | --- |
| Version reported | 2.0.1.0, live at https://hisaab-hazel.vercel.app since 27 Nov 2026 |
| What it is | A shop credit ledger. Vite, React, TypeScript, Tailwind CSS, Zustand. Static files, no server, no AI |
| Written | 27 Nov 2026, from the code and the test results of 2.0.1.0 |

Version 2.0 is a rebuild. It replaced 1.2.5.0 at the live link on 27 Nov 2026, and the 1.x code no longer
runs. Everything in this report is about 2.0.1.0, except the version table at the end.

## Status per part

| Part of the brief | Status in 2.0.1.0 | Evidence |
| --- | --- | --- |
| Must-do list (9 items) | Built. Seven pass the checker on the live link; adding a customer and the newest-first order pass in local browser tests | `evidence/last-check-live-2.0.1.0.txt`; `e2e/criteria.spec.ts`, `e2e/hooks.spec.ts` |
| Hidden data (another shop through `#import-box` and `#import-go`) | Built. All balances, the total, the reminders and the reload are correct on two seeds; the import replaces the shop | `check.mjs [2-hidden ...]` |
| Tester hooks (6) | In place, the same ids and attributes as 1.x | `SPEC-CHECK.md` |
| Bonus menu | Six of seven built: UPI link, overdue list, search, CSV export, Hindi and Telugu, edit and delete. Reading the amount from a screenshot is not built | `check.mjs [9-bonus ...]` |
| Phone, 375 px | Built: scroll width 375 on the home and customer pages | `check.mjs [7-phone]` |
| Light and dark | Built: every text meets 4.5:1, or 3:1 for large text | `check.mjs [8-contrast ...]` |
| Live | https://hisaab-hazel.vercel.app, deployed 27 Nov 2026 | |

## What was built

- The ledger: customers, credit and payment entries, each balance worked out from the entries, and the total at the top. An advance lowers the total.
- A customer page with the entries newest first and the days since the last payment.
- A payment screenshot on an entry, stored no larger than 600 px on either side.
- A WhatsApp reminder per customer with the name, the balance as "Rs" and a figure, and a `upi://pay` link for the balance. The link uses the demo id `hisaab.demo@upi` until the shop sets its own in Settings. The sentence is in the chosen language.
- An import box that replaces the shop with a pasted shop file. A bad file changes nothing and says why.
- Overdue list, search, edit and delete with Undo for the last delete, two CSV exports.
- English, Hindi and Telugu; light and dark.
- A ledger, language and theme saved by 1.x open in 2.0; the old copy is left in place.
- A full browser store refuses the action and loses nothing. With storage blocked the page still opens.
- The page asks no other site for anything and opens with no network after one visit.

The first rebuild, 2.0.0.0, scored 95 of 100 on the checker locally. A read-only review found 12 defects. Each
was fixed with a test written first. The list is in `IMPLEMENTATION-PLAN.md`, "Fix pass".

What 1.x had and 2.0.1.0 does not is listed once, in `PRD.md`, section 7.

## Requirement and feature map

R1 to R9 are the nine must-do items of the brief, B1 to B7 the bonus menu (six count), M the light and dark
marks. N/A means the brief does not ask for it. "Live" means the checker passed it on the live link on
27 Nov 2026. "Local" means a Vitest or Playwright test covers it on a local build. Test names are quoted as
written in the test files; `[id]` is a check name printed by `tests/check.mjs`.

| Req | Feature | In 2.0.1.0 | Where in the code | Test |
| --- | --- | --- | --- | --- |
| R1 | Add a customer with a name and a 10-digit phone | Built; local | `src/App.tsx`, `validateCustomer` in `src/lib/validate.ts`, `addCustomer` in `src/store/useShopStore.ts` | `e2e/criteria.spec.ts` "add a customer by the form; refusals for no name, a short phone and a phone already on the ledger" |
| R2 | Add an entry: credit or payment, amount, date, note | Built; live | `src/App.tsx`, `validateEntry`, `addEntry` in the store | `[5-shot balance]`; `e2e/hooks.spec.ts` "P4 payment lowers balance and newest first" |
| R3 | Each balance, and the total outstanding at the top | Built; live | `balanceOf`, `totalOutstanding` in `src/lib/ledger.ts` | `[1-sample total]`, `[1-sample balances]`, `[2-hidden total]`, `[2-hidden balances]` |
| R4 | Customer page lists the entries, newest first | Built; local | `entriesNewestFirst` in `src/lib/ledger.ts` | `src/lib/ledger.test.ts` "lists entries newest first"; `e2e/hooks.spec.ts` "P4 ..." |
| R5 | Payment screenshot shown on the entry | Built; live | `#shot-input` in `src/App.tsx`, `src/lib/images.ts` | `[5-shot img]`, `[5-shot reload]` |
| R6 | WhatsApp reminder with the name and the balance | Built; live | `reminderHref` in `src/lib/ledger.ts`, `src/components/CustomerRow.tsx` | `[3-reminder]`, `[2-hidden reminders]` |
| R7 | Data still there after a reload | Built; live | The store's `persist`, key `hisaab-shop-v2` | `[4-persistence]`, `[2-hidden persistence]` |
| R8 | Import box for a shop file in the sample format | Built; live | `src/components/ImportBox.tsx`, `validateImport` in `src/lib/validate.ts` | `[2-hidden replace]`; `src/store/store.test.ts` |
| R9 | Works at 375 px | Built; live | `src/index.css`, `src/App.tsx` | `[7-phone]` |
| B1 | Overdue list sorted by days since the last payment | Built; live | `overdueList` in `src/lib/csv.ts`, `daysSinceLastPayment` in `src/lib/ledger.ts` | `[9-bonus overdue]` |
| B2 | Edit and delete an entry, balances recalculated | Built; live | `src/store/useShopStore.ts` | `[9-bonus edit/delete]` |
| B3 | CSV export that matches the data | Built; live | `balancesCsv`, `entriesCsv` in `src/lib/csv.ts` | `[9-bonus CSV]` |
| B4 | Search by name or phone | Built; live | `src/App.tsx` | `[9-bonus search]` |
| B5 | UPI pay link in the reminder | Built; live | `reminderHref`, `DEMO_UPI` in `src/lib/ledger.ts` | `[9-bonus UPI]` |
| B6 | Hindi and Telugu labels | Built; live | `src/i18n/translations.ts`, `src/hooks/useLang.ts` | `[9-bonus language]` |
| B7 | Reading the amount from the payment screenshot | Not built | none | `[9-bonus OCR]` FAIL |
| M | Readable in light and dark | Built; live | `src/index.css`, `src/hooks/useTheme.ts` | `[8-contrast light home]`, `[8-contrast light customer]`, `[8-contrast dark home]`, `[8-contrast dark customer]` |
| N/A | Undo after deleting an entry | Built; local | `undoDelete` in the store | `e2e/fixes.spec.ts` "delete asks nothing in a window, and Undo puts the entry back"; `e2e/criteria.spec.ts` "Undo brings back only the last deleted entry" |
| N/A | Reminder text in the chosen language; a message for an advance and for nothing owed | Built; local | `reminderHref` | `src/lib/fixes.test.ts` "Hindi and Telugu keep the name, the figure and the rupee mark", "reminder text for an advance and for nothing owed" |
| N/A | A ledger, language and theme saved by 1.x open in 2.0 | Built; local | `src/lib/legacy.ts` | `e2e/fixes.spec.ts` "a ledger saved by version 1 opens here, with its language and theme" |
| N/A | A full browser store refuses the action and loses nothing | Built; local | `commit` in `src/store/useShopStore.ts` | `src/store/quota.test.ts` |
| N/A | A picture that cannot be read does not save the entry | Built; local | `src/App.tsx`, `src/lib/images.ts` | `e2e/criteria.spec.ts` "a picture that cannot be read does not save the entry and says so" |
| N/A | Opens with no network after one visit; asks no other site for anything | Built; local | `public/sw.js` | `e2e/fixes.spec.ts` "the page asks no other site for anything, and opens again with no network" |
| N/A | The theme button and the navigation say what they did; a skip link | Built; local | `src/App.tsx`, `src/hooks/useTheme.ts` | `e2e/hooks.spec.ts` "P7 cold open: theme announce, nav announce, empty offer"; `e2e/criteria.spec.ts` "the skip link moves focus to the ledger" |
| N/A | The language is remembered | Built; local | `src/hooks/useLang.ts` | `e2e/criteria.spec.ts` "language is remembered after a reload" |
| N/A | A searched name with no match offers to add that customer | Built; local | `src/App.tsx` | `e2e/hooks.spec.ts` "P7 cold open: ..." |
| N/A | Days since the last payment on the customer page | Built; local | `daysSinceLastPayment` | `e2e/fixes.spec.ts` "?today moves the day count on the customer page" |

Each acceptance criterion and its test is in `docs/epics.md`. The brief, line by line, is in `SPEC-CHECK.md`.

## Measured results

| What | Where it ran | Result |
| --- | --- | --- |
| Checker `tests/check.mjs`, seed 4242 | live link, 27 Nov 2026 | 25 PASS, 1 FAIL; estimate 100/100 |
| Checker `tests/check.mjs`, seed 90210 | live link, 27 Nov 2026 | 25 PASS, 1 FAIL; estimate 100/100 |
| Vitest | local | 32 pass |
| Playwright | local | 22 pass |

- The one FAIL on both seeds is `[9-bonus OCR]`: reading the amount from a screenshot is not built. It is a seventh bonus item; six count, and six pass.
- The estimate is the checker's own: hidden data 60/60, bonus 30/30, phone 5/5, light and dark 5/5. It is not the grader's score.
- The checker reported no window dialogs and no console or page errors.
- The checker printed "could not confirm" for `?today=` on the home page, as information, not a failure. The home rows show no day count. The day count on the customer page is covered by a local Playwright test.
- Evidence: [evidence/last-check-live-2.0.1.0.txt](evidence/last-check-live-2.0.1.0.txt) for seed 4242 and [evidence/last-check-live-2.0.1.0-seed-90210.txt](evidence/last-check-live-2.0.1.0-seed-90210.txt) for seed 90210. The index of the folder is [evidence/README.md](evidence/README.md).

## Judges' note

Hisaab is a khata for a shop counter, on the owner's phone: who owes what, the total owed, and a WhatsApp
reminder with the amount and a UPI pay link.

What to try at https://hisaab-hazel.vercel.app:

1. The ledger starts empty. Add a customer, or paste a shop file into the import box and press Import.
2. Open a customer, add a credit or a payment, and attach a payment screenshot.
3. Press Remind on a row: WhatsApp opens with the name, the balance and a pay link.
4. Open Overdue, search by name or phone, export CSV from Settings, switch to हिन्दी or తెలుగు, switch to dark.

What it is: version 2.0.1.0, a rebuild on Vite, React, TypeScript, Tailwind CSS and Zustand. No AI, no
account, no server. Balances are worked out from the entries, never typed in. The six tester hooks are in
place. Six bonus items are built: overdue list, edit and delete, CSV export, search, UPI pay link, Hindi and
Telugu.

Limits: the ledger lives in this browser only, with no backup file and no sync. Reminders carry the demo UPI id
until the shop sets its own. Reading the amount from a screenshot is not built. Tested in Chromium only.

Built by Sanjay Karwa, Smart AI for Business.
Documentation: https://github.com/karwasanjay007/hisaab-docs

## Not tested

- The Vitest and Playwright tests against the live link. They ran on a local build.
- `tests/brief.mjs`, the literal walk through every "Done when" line of the brief. It targets the 1.x pages.
- The other 1.x test scripts. They target pages 2.0.1.0 does not have.
- Other browsers than Chromium, and a real phone.
- The size of tap targets at 375 px.
- Undo after a reload or an import (read in the code, no test).
- A credit added by the form, and a fraction typed into the amount field (covered through the edit check and the import path only).
- `npm audit` reports 3 advisories in build tools. They were not looked into.

## Versions

One line per version. The results of 1.x were measured on code that no longer runs; their output files are
kept in `evidence/` as the record.

| Version | Date | What it was | Result measured then |
| --- | --- | --- | --- |
| 2.0.1.0 | 27 Nov 2026 | The rebuild with its 12 defects fixed; live | See "Measured results" |
| 2.0.0.0 | 26 Nov 2026 | First rebuild on the new stack | Local: checker 23 PASS, 2 FAIL, estimate 95/100 |
| 1.2.5.0 | 25 Nov 2026 | Controls say what they did (theme, navigation, skip link) | Live, 26 Nov 2026: full regression, all suites pass; checker 100/100 |
| 1.2.4.0 | 23 Nov 2026 | Voice quick entry says why it heard nothing | Live: its suite and the checker pass |
| 1.2.3.0, 1.2.3.1 | 23 Nov 2026 | WhatsApp check when a customer is added | Live: its suite passes |
| 1.2.2.0 | 23 Nov 2026 | Other ways to send a reminder | Live: its suite passes |
| 1.2.1.0 | 23 Nov 2026 | Layout for every language and width; names in nine more scripts; pay page | Live: its suites pass |
| 1.2.0.0 | 22 to 23 Nov 2026 | Nine more languages; customer page tools | Live: full regression passes |
| 1.1.0.0 | 22 Nov 2026 | Shop profile, balance history line, several shops | Live: full regression passes; checker 100/100 |
| 1.0.0.0 | 21 Nov 2026 | First baseline, plain HTML and JavaScript; all seven bonus items | Live: checker 27 PASS, 0 FAIL, estimate 100/100 |
