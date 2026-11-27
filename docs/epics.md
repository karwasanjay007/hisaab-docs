# Hisaab: epics, features and acceptance criteria

| | |
| --- | --- |
| Product | Hisaab, a shop credit ledger (React and TypeScript; data in the browser) |
| Live | https://hisaab-hazel.vercel.app |
| Source of truth | `src/`, `e2e/` and `public/sw.js` of version 2.0.1.0 |
| Traces to | `PRD.md` (R1 to R9, B1 to B6, M, X-1 to X-9) |
| Last revised | 27 Nov 2026 (version 2.0.1.0) |

Every criterion describes behaviour found in the code of 2.0.1.0 and names a test file that exists in the code
repository. The epics of 1.x that were not rebuilt are listed at the end, not described as present.

## How to read the "Verified by" column

| Short name | What it is |
| --- | --- |
| `ledger.test.ts` | `src/lib/ledger.test.ts`, Vitest: sums on the sample shop and a hand-checked shop |
| `fixes.test.ts` | `src/lib/fixes.test.ts`, Vitest: the fixes of 2.0.1.0 |
| `store.test.ts` | `src/store/store.test.ts`, Vitest: import through the store |
| `quota.test.ts` | `src/store/quota.test.ts`, Vitest: carry-over, a full store, edit and delete |
| `hooks.spec.ts` | `e2e/hooks.spec.ts`, Playwright: the grader hooks, P2 to P7 |
| `fixes.spec.ts` | `e2e/fixes.spec.ts`, Playwright: the fixes of 2.0.1.0 in a browser |
| `criteria.spec.ts` | `e2e/criteria.spec.ts`, Playwright: criteria that had no test before |
| `check.mjs [id]` | `tests/check.mjs`, the checker; `[id]` is the check name it prints. Live run: `evidence/last-check-live-2.0.1.0.txt` |

The quoted text after a file name is the name of the test in that file.

## Epics at a glance

| Epic | Title | Features | Criteria | PRD trace |
| --- | --- | --- | --- | --- |
| E1 | Customers and entries | 4 | 14 | R1, R2, R5, B2, X-1, X-5 |
| E2 | Balances, home and overdue | 3 | 9 | R3, R9, B1, B4, X-8 |
| E3 | Reminders | 2 | 6 | R6, B5, X-2 |
| E4 | Customer page | 1 | 3 | R4, X-9 |
| E5 | Shop data | 4 | 16 | R7, R8, B3, X-3, X-4 |
| E6 | Languages, themes and offline | 3 | 8 | B6, M, X-6, X-7 |
| Total | | 17 | 56 | |

Every criterion names a test. Three parts of a criterion have no test and say "not tested" in their row: the
form path of E1.F2 criterion 2, "not after a reload or an import" in E1.F4 criterion 4, and `aria-pressed` in
E6.F2 criterion 2.

## E1 Customers and entries

### E1.F1 Add a customer

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | A name and a 10-digit phone add a customer to the home list | `criteria.spec.ts` "add a customer by the form; refusals for no name, a short phone and a phone already on the ledger" |
| 2 | An empty name, a phone that is not 10 digits, or a phone already on the ledger is refused and nothing is added | the same test |

### E1.F2 Add an entry

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | A payment lowers the customer's balance and the total by its amount | `hooks.spec.ts` "P4 payment lowers balance and newest first"; `check.mjs [5-shot balance]` |
| 2 | The amount must be a positive whole number of rupees | `fixes.test.ts` "still refuses a fraction of a rupee" (import path); the form path: not tested |
| 3 | The date field starts at today, or at `?today=` | `fixes.spec.ts` "?today moves the day count on the customer page" |
| 4 | After a save the form is cleared and no error is shown | `fixes.spec.ts` "saving an entry with a screenshot clears the form and shows no error" |

### E1.F3 Payment screenshot

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | A picture chosen in `#shot-input` is shown on the entry as `img[data-shot]` | `hooks.spec.ts` "P5 reminder hrefs and screenshot"; `check.mjs [5-shot img]` |
| 2 | The picture is still there after a reload | `check.mjs [5-shot reload]` |
| 3 | The stored picture fits inside 600 px on both sides and is never enlarged | `fixes.test.ts` "fits inside 600 by 600 and never enlarges"; `fixes.spec.ts` "a tall screenshot is stored no larger than 600 px on either side and stays small" |
| 4 | A picture that cannot be read stops the save and says so | `criteria.spec.ts` "a picture that cannot be read does not save the entry and says so" |

### E1.F4 Edit, delete and Undo

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | Editing or deleting an entry changes the balance and the total | `hooks.spec.ts` "P6 bonuses: search, edit/delete, csv, overdue, upi, hindi"; `check.mjs [9-bonus edit/delete]` |
| 2 | An edit without a valid date is refused | `quota.test.ts` "an edit with a date that is not a date is refused"; `fixes.spec.ts` "an edit cannot leave an entry without a date" |
| 3 | Delete opens no window dialog; an Undo button puts the entry back | `fixes.spec.ts` "delete asks nothing in a window, and Undo puts the entry back"; `quota.test.ts` "a deleted entry comes back with undo" |
| 4 | Only the entry deleted last can be put back, and not after a reload or an import | `criteria.spec.ts` "Undo brings back only the last deleted entry"; after a reload or an import: by reading `src/store/useShopStore.ts`, not tested |

## E2 Balances, home and overdue

### E2.F1 Balances and total

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | Each balance is credit minus payments, worked out from the entries | `ledger.test.ts` "matches every balance in sample-expected.json", "matches hand-worked balances"; `check.mjs [1-sample balances]`, `[2-hidden balances]` |
| 2 | The total at `#total-outstanding` is the sum of all balances, advances included, and contains the plain number | `ledger.test.ts` "matches total outstanding"; `check.mjs [1-sample total]`, `[2-hidden total]` |
| 3 | Balance, total and the CSV running balance use one sign rule | `fixes.test.ts` "balance, total and the CSV running balance agree" |
| 4 | Money is written with Indian grouping | `ledger.test.ts` "uses Indian grouping" |
| 5 | Nothing scrolls sideways at 375 px on the home and customer pages | `check.mjs [7-phone]` |

### E2.F2 Search

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | Typing part of a name or a phone leaves only the matching rows | `hooks.spec.ts` "P6 bonuses: ..."; `check.mjs [9-bonus search]` |
| 2 | A search of two or more characters that is not all digits and matches no one offers to add a customer with that name | `hooks.spec.ts` "P7 cold open: theme announce, nav announce, empty offer" |

### E2.F3 Overdue list

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | The Overdue view lists customers who owe, most days since the last payment first | `hooks.spec.ts` "P6 bonuses: ..."; `check.mjs [9-bonus overdue]` |
| 2 | With no payment the days count from the first entry; `?today=` sets the day | `ledger.test.ts` "days since last payment honours today and never-paid" |

## E3 Reminders

### E3.F1 WhatsApp reminder

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | Each row has `a[data-remind]` to `https://wa.me/91<phone>?text=` with the name and the balance as "Rs" and a figure | `hooks.spec.ts` "P5 reminder hrefs and screenshot"; `fixes.test.ts` "still starts with the wa.me address"; `check.mjs [3-reminder]`, `[2-hidden reminders]` |
| 2 | In Hindi and Telugu the sentence is in that language and keeps the name, the figure and "Rs" | `fixes.test.ts` "Hindi and Telugu keep the name, the figure and the rupee mark"; `fixes.spec.ts` "the reminder is written in the chosen language and keeps name, figure and rupee mark" |
| 3 | A customer with an advance or a zero balance gets a message that says so | `fixes.test.ts` "reminder text for an advance and for nothing owed" |

### E3.F2 UPI pay link

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | A reminder for money owed ends with `upi://pay?pa=<id>&am=<balance>&cu=INR`, before any setting is touched; the id is `hisaab.demo@upi` until the shop sets its own | `fixes.test.ts` "uses the demo UPI id when the shop has none, with am = balance"; `fixes.spec.ts` "every reminder carries a UPI pay link for the balance before any setting is touched"; `check.mjs [9-bonus UPI]` |
| 2 | Once a UPI id is typed in Settings the link uses it | `fixes.test.ts` "uses the shop UPI id when one is set"; `hooks.spec.ts` "P6 bonuses: ..." |
| 3 | When nothing is owed there is no pay link | `fixes.test.ts` "has no pay link when nothing is owed" |

## E4 Customer page

### E4.F1 Entries and day count

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | Opening a customer lists the entries newest first | `ledger.test.ts` "lists entries newest first"; `hooks.spec.ts` "P4 payment lowers balance and newest first" |
| 2 | Entries of the same date keep the order of the imported file, later row first; an entry added afterwards sorts above them | `fixes.test.ts` "keeps file order for entries on the same date (later row is newer)", "an entry added after an import sorts above imported rows of the same date" |
| 3 | The page shows days since the last payment, or "No payment yet" with the days since the first entry, counted from `?today=` | `fixes.spec.ts` "?today moves the day count on the customer page" |

## E5 Shop data

### E5.F1 Import

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | Pasting a shop file into `#import-box` and pressing `#import-go` replaces the shop; no earlier customer remains | `store.test.ts` "imports sample then my-shop with replace-all (no sample customer left)"; `hooks.spec.ts` "P2 import replace-all leaves no sample customer"; `check.mjs [2-hidden replace]` |
| 2 | Text that is not JSON changes nothing and says so | `store.test.ts` "rejects broken JSON and leaves data untouched"; `hooks.spec.ts` "P2 bad JSON leaves data" |
| 3 | An entry with a type other than credit or payment refuses the whole file | `store.test.ts` "rejects invalid entry type and leaves data untouched" |
| 4 | An amount written as digits in a string is read; a fraction is refused | `fixes.test.ts` "reads an amount written as a string of digits", "still refuses a fraction of a rupee" |
| 5 | A file with customers and no entries list loads | `fixes.test.ts` "loads a shop file with no entries list" |
| 6 | A picture that is not an image data URL, or is too large, is dropped and the entry kept | `fixes.test.ts` "drops a picture that is not an image data URL or is too large, and keeps the entry" |
| 7 | Customers with no entries and with an advance load with balances 0 and below 0 | `ledger.test.ts` "has 12 customers including zero-balance and overpaid" |

### E5.F2 Persistence and a full store

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | The ledger is the same after a reload | `check.mjs [4-persistence]`, `[2-hidden persistence]` |
| 2 | An entry the browser could not store is not shown as saved; the action reports that storage is full | `quota.test.ts` "an entry that could not be written is not shown as saved" |
| 3 | Add customer, edit, delete and import report a full store the same way and change nothing | `quota.test.ts` "add customer, edit, delete and import also report it and change nothing" |
| 4 | With storage blocked the page still opens | `fixes.spec.ts` "blocked storage does not blank the page" |

### E5.F3 Carry-over from 1.x

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | A ledger saved by 1.x opens in 2.0 with its customers, entries, shop name and UPI id; the old copy stays | `fixes.test.ts` "reads customers, entries, shop name and UPI id"; `quota.test.ts` "shows the ledger version 1 saved, and leaves the old copy where it was" |
| 2 | A broken row is skipped and the rest is kept; unreadable data gives an empty ledger | `fixes.test.ts` "skips a broken row instead of losing the ledger", "returns nothing for no data or unreadable data" |
| 3 | The language and theme chosen in 1.x are used | `fixes.spec.ts` "a ledger saved by version 1 opens here, with its language and theme" |

### E5.F4 CSV export

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | "Balances CSV" downloads `hisaab-balances.csv` with `phone,name,balance` and one row per customer | `hooks.spec.ts` "P6 bonuses: ..."; `check.mjs [9-bonus CSV]` |
| 2 | "Entries CSV" downloads `hisaab-entries.csv` with a running balance per customer, oldest first | `fixes.test.ts` "balance, total and the CSV running balance agree" |

## E6 Languages, themes and offline

### E6.F1 Languages

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | The language list offers English, Hindi and Telugu; choosing one changes the labels | `hooks.spec.ts` "P6 bonuses: ..."; `check.mjs [9-bonus language]` |
| 2 | The choice is remembered | `criteria.spec.ts` "language is remembered after a reload" |
### E6.F2 Light and dark

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | Every text meets 4.5:1 (3:1 for large text) in light and dark, home and customer page | `check.mjs [8-contrast light home]`, `[8-contrast light customer]`, `[8-contrast dark home]`, `[8-contrast dark customer]` |
| 2 | The status line says which theme is on after the theme button is pressed (the button also carries `aria-pressed`, which is not tested) | `hooks.spec.ts` "P7 cold open: theme announce, nav announce, empty offer" |
| 3 | A press on the navigation item of the view already open writes the view's name to the status line | `hooks.spec.ts` "P7 cold open: ..." |
| 4 | The skip link, the first stop of the Tab key, moves the focus to the ledger | `criteria.spec.ts` "the skip link moves focus to the ledger" |

### E6.F3 Offline and no outside requests

| # | Criterion | Verified by |
| --- | --- | --- |
| 1 | The page asks no other site for anything | `fixes.spec.ts` "the page asks no other site for anything, and opens again with no network" |
| 2 | After one visit the page opens again with no network | the same test |

## Traceability: PRD requirement to features

| PRD | Features |
| --- | --- |
| R1 | E1.F1 |
| R2 | E1.F2 |
| R3 | E2.F1 |
| R4 | E4.F1 |
| R5 | E1.F3 |
| R6 | E3.F1 |
| R7 | E5.F2 |
| R8 | E5.F1 |
| R9 | E2.F1 (criterion 5) |
| B1 | E2.F3 |
| B2 | E1.F4 |
| B3 | E5.F4 |
| B4 | E2.F2 |
| B5 | E3.F2 |
| B6 | E6.F1 |
| M | E6.F2 |
| X-1 | E1.F4 |
| X-2 | E3.F1 |
| X-3 | E5.F3 |
| X-4 | E5.F2 |
| X-5 | E1.F3 |
| X-6 | E6.F3 |
| X-7 | E6.F2 |
| X-8 | E2.F2 |
| X-9 | E4.F1 |

## In 1.x, not in 2.0

These epics and features of 1.2.5.0 were removed from this document because 2.0.1.0 does not have them. Their
tests (`tests/*.mjs` other than `check.mjs`) target the 1.x pages and do not apply to 2.0.

| 1.x epic or feature | Status in 2.0.1.0 |
| --- | --- |
| Quick entry, typed and by voice (E5 of 1.x) | Not built |
| Reading the amount from a screenshot | Not built; the checker's one FAIL |
| UPI QR code, pay page, printable statement, receipt message, remind all | Not built |
| CSV import, file picker, JSON backup, Undo after an import or an edit, sample shop button | Not built |
| Shop profile and balance history (E9) | Not built |
| Several shops (E10) | Not built |
| Nine more languages (E11) and names in another script | Not built |
| Customer tools: totals, call, chat, change phone, delete customer (E12) | Not built, except the day count on the customer page |
| Note shortcuts and the overdue total (E13) | Not built |
| Other ways to send, the WhatsApp check (E14) | Not built |
| Deep links, the wide layout, the overview figures | Not built |
