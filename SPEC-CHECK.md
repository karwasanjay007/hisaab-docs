# Hisaab: specification check

| | |
| --- | --- |
| Version checked | 2.0.1.0, live at https://hisaab-hazel.vercel.app since 27 Nov 2026 |
| Specification | The Hisaab section of the build project's projects page, captured word for word on 20 Nov 2026 into `brief/spec-page.md` of the code repository |
| Checked on | 27 Nov 2026, against the code of 2.0.1.0 and its tests |
| Literal test | `tests/brief.mjs` walks every "Done when" line. It was written for the 1.x pages and was not run on 2.0.1.0 |

Every line of the specification is rated here against 2.0.1.0. Earlier ratings were of 1.x and are replaced.

## How to read the tables

- "Met, live" means the checker `tests/check.mjs` passed that check on the live link on 27 Nov 2026, seeds 4242 and 90210 (`evidence/last-check-live-2.0.1.0.txt`, `evidence/last-check-live-2.0.1.0-seed-90210.txt`).
- "Met, local" means a Vitest or Playwright test of the code repository covers it. Those tests run on a local build, not on the live link.
- "brief.mjs: not run" marks a rating that rests on those other tests and not on the literal "Done when" walk.
- Test names are quoted as they are written in the test files. `[id]` is a check name printed by `check.mjs`.

## The nine must-do items

| # | Must do | Done when (abridged) | 2.0.1.0 | Where in the code | Test |
| --- | --- | --- | --- | --- | --- |
| 1 | Add a customer | Ravi Kumar 9000000101 is on the home list; 9 digits or letters are refused with a message | Met, local. A short phone and an empty name are held back by the browser's own form check; no test reads that message. A repeated phone and a name of spaces get the app's message | `src/App.tsx` (form), `validateCustomer` in `src/lib/validate.ts`, `addCustomer` in `src/store/useShopStore.ts` | `e2e/criteria.spec.ts` "add a customer by the form; refusals for no name, a short phone and a phone already on the ledger". brief.mjs: not run |
| 2 | Credit or payment with amount, date, note | A credit of Rs 250 makes the balance read Rs 250 | Met, live, for a payment by the form. A credit by the form is covered only through the edit check | `src/App.tsx` (form), `validateEntry` in `src/lib/validate.ts`, `addEntry` in the store | `check.mjs [5-shot balance]`, `[9-bonus edit/delete]`; `e2e/hooks.spec.ts` "P4 payment lowers balance and newest first". brief.mjs: not run |
| 3 | Balances and the total at the top | Sample shop: the top reads 11820, Ravi 670; computed, not typed | Met, live | `balanceOf`, `totalOutstanding` in `src/lib/ledger.ts`; `#total-outstanding` in `src/App.tsx` | `check.mjs [1-sample total]`, `[1-sample balances]`, `[2-hidden total]`, `[2-hidden balances]`; `src/lib/ledger.test.ts` |
| 4 | Customer page, newest first | 25 Oct Rs 1,500 is above 23 Oct Rs 180 | Met, local. On the live link the checker printed the order as information: newest first | `entriesNewestFirst` in `src/lib/ledger.ts` | `src/lib/ledger.test.ts` "lists entries newest first"; `e2e/hooks.spec.ts` "P4 payment lowers balance and newest first". brief.mjs: not run |
| 5 | Payment screenshot as a small picture | The picture shows, also after a reload | Met, live | `#shot-input`, `img[data-shot]` in `src/App.tsx`; `resizeToDataUrl` in `src/lib/images.ts` | `check.mjs [5-shot img]`, `[5-shot reload]` |
| 6 | WhatsApp reminder | The name and Rs 670 are in the message | Met, live | `reminderHref` in `src/lib/ledger.ts`; `a[data-remind]` in `src/components/CustomerRow.tsx` | `check.mjs [3-reminder]`, `[2-hidden reminders]` |
| 7 | Survives a reload | The entry and the balance are still there | Met, live | The store's `persist`, key `hisaab-shop-v2`, in `src/store/useShopStore.ts` | `check.mjs [4-persistence]`, `[2-hidden persistence]` |
| 8 | Import box | The sample shows 8 customers; a customer with no entries loads | Met, live | `src/components/ImportBox.tsx`; `validateImport` in `src/lib/validate.ts` | `check.mjs [1-sample balances]` (8 rows), `[2-hidden balances]` (12 rows, one with no entries), `[2-hidden replace]` |
| 9 | Phone, 375 px | No sideways scroll; every button easy to tap | No sideways scroll: met, live. The size of tap targets: not tested on 2.0.1.0 | `src/index.css`, Tailwind classes in `src/App.tsx` | `check.mjs [7-phone]`. brief.mjs: not run |

## The tester hooks

| Selector or rule | 2.0.1.0 | Where in the code | Test |
| --- | --- | --- | --- |
| `#total-outstanding` contains the number | Met, live: "Rs 11,820" and the plain 11820 | `src/App.tsx` | `check.mjs [1-sample total]`, `[2-hidden total]` |
| `[data-customer]` with `[data-balance]` | Met, live | `src/components/CustomerRow.tsx` | `check.mjs [1-sample balances]`, `[2-hidden balances]` |
| `a[data-remind]` to `https://wa.me/91<phone>?text=` | Met, live | `src/components/CustomerRow.tsx`, `reminderHref` | `check.mjs [3-reminder]` |
| `#import-box` and `#import-go` | Met, live. JSON in the sample format only; CSV is not read | `src/components/ImportBox.tsx` | `check.mjs [2-hidden replace]` |
| `#shot-input` and `img[data-shot]` | Met, live | `src/App.tsx` | `check.mjs [5-shot img]` |
| `?today=YYYY-MM-DD` | Met, local: it sets the day count on the customer page, the overdue order and the default entry date. The home rows show no day count, so the checker printed "could not confirm" as information | `todayFromUrl` in `src/lib/ledger.ts` | `e2e/fixes.spec.ts` "?today moves the day count on the customer page"; `src/lib/ledger.test.ts` "days since last payment honours today and never-paid" |

## The bonus menu (5 marks each, six count)

| Bonus | 2.0.1.0 | Where in the code | Test |
| --- | --- | --- | --- |
| Overdue list by days since the last payment | Met, live | `overdueList` in `src/lib/csv.ts`, `daysSinceLastPayment` in `src/lib/ledger.ts` | `check.mjs [9-bonus overdue]` |
| Edit and delete with balances recalculated | Met, live. Delete has Undo; an edit has none | The edit action, `deleteEntry` and `undoDelete` in `src/store/useShopStore.ts` | `check.mjs [9-bonus edit/delete]` |
| CSV export that matches the data | Met, live, for the balances file. The entries file: local | `balancesCsv`, `entriesCsv` in `src/lib/csv.ts` | `check.mjs [9-bonus CSV]`; `src/lib/fixes.test.ts` "balance, total and the CSV running balance agree" |
| Search by name or phone | Met, live | `src/App.tsx` | `check.mjs [9-bonus search]` |
| UPI pay link `upi://pay?pa=...&am=...` | Met, live. The id is the demo `hisaab.demo@upi` until the shop sets its own | `reminderHref`, `DEMO_UPI` in `src/lib/ledger.ts` | `check.mjs [9-bonus UPI]` |
| Hindi or Telugu labels | Met, live: both | `src/i18n/translations.ts`, `src/hooks/useLang.ts` | `check.mjs [9-bonus language]` |
| Amount read from the payment screenshot | Not met: not built in 2.0.1.0 | none | `check.mjs [9-bonus OCR]` FAIL |

## Phone and light and dark marks

| Part | 2.0.1.0 | Where in the code | Test |
| --- | --- | --- | --- |
| Phone, 375 px (5 marks) | Met, live: scroll width 375 on the home and customer pages | `src/index.css`, `src/App.tsx` | `check.mjs [7-phone]` |
| Light and dark (5 marks) | Met, live: every text meets 4.5:1, or 3:1 for large text, in both themes | `src/index.css`, `src/hooks/useTheme.ts` | `check.mjs [8-contrast light home]`, `[8-contrast light customer]`, `[8-contrast dark home]`, `[8-contrast dark customer]` |
| No window dialogs, no console errors | Met, live | | `check.mjs [dialogs]`, `[console]` |

## Estimate

The checker's own estimate is 100 of 100 on both seeds: hidden data 60 of 60, bonus 30 of 30 (six count; six
pass), phone 5 of 5, light and dark 5 of 5. That is our checker, not the graders' score.

## What could still cost marks

- The graders' tester is an AI reading the page; its exact steps are not known.
- The literal "Done when" walk was not run on 2.0.1.0. Must-do 1, 2, 4 and 9 rest in part on other tests, as marked above.
- A short phone or an empty name is refused by the browser's form check. A tester that looks for the app's own message there would not find one.
- The total nets off advances: `#total-outstanding` is the sum of all balances. A tester that expects only the money owed would read a lower figure when a customer is in advance.
- The seventh bonus item, reading the amount from a screenshot, is not built. Six count and six pass.

## Not tested

- `tests/brief.mjs` on 2.0.1.0.
- The Vitest and Playwright tests against the live link; they ran on a local build.
- Other browsers than Chromium, and a real phone.
- The size of tap targets at 375 px.
