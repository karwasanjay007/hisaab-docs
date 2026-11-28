# Hisaab: specification check

This page describes version 1.5.2, the version on the live link.

Hisaab was built against a written specification. Each requirement is listed here with whether Hisaab meets
it, how, and the test that shows it. The tests run in a fresh browser against the live link; their latest
results are in [the report](REPORT.md#latest-results-on-the-live-link).

## What it must do

The nine required items, R1 to R9.

| Id | Requirement | Result | How it is met | Test |
| --- | --- | --- | --- | --- |
| R1 | Add a customer with a name and a 10 digit phone number | Met | "Add a customer" takes a name and a phone; nine digits or letters are refused with a message | `tests/brief.mjs` |
| R2 | Add an entry for a customer: credit or payment, with amount, date and a short note | Met | The entry card has "You gave (credit)" and "You got (payment)", amount, date and note | `tests/brief.mjs` |
| R3 | Show each customer's balance (all credit minus all payments) and the shop's total outstanding at the top | Met | Both are worked out from the entries each time the page draws; the sample shows 11820 | `tests/brief.mjs`, `tests/check.mjs`, `tests/sweep.mjs` |
| R4 | A customer page lists that customer's entries, newest first | Met | "Entries, newest first" on the customer page | `tests/brief.mjs` |
| R5 | A payment can have a screenshot attached. It shows as a small picture on the entry | Met | "Choose screenshot" on the entry card; the picture stays after a reload | `tests/brief.mjs`, `tests/check.mjs` |
| R6 | A WhatsApp reminder button per customer, with the customer's name and the balance in rupees in the message | Met | A WhatsApp button on every customer row and "WhatsApp reminder" on the customer page | `tests/brief.mjs`, `tests/check.mjs` |
| R7 | Data is still there after a reload | Met | Every change is written to the browser's own store at once | `tests/brief.mjs`, `tests/check.mjs` |
| R8 | An import box that loads a JSON file in the format of the sample data | Met | "Load a shop" replaces the current shop; a customer with no entries loads too | `tests/brief.mjs`, `tests/phone-import.mjs` |
| R9 | Works well on a phone, 375 px wide | Met | No view scrolls sideways at 375 px | `tests/brief.mjs`, `tests/phone-ledger.mjs` |

### Ids kept stable for automated tests

The specification names the parts of the page a script reads, so the ledger can be tested without a person.

| Part of the page | Id or rule | Result | How it is met | Test |
| --- | --- | --- | --- | --- |
| Total outstanding | `#total-outstanding`; the text contains the number | Met | The element holds the figure with the rupee sign and the plain number | `tests/check.mjs` |
| One row per customer | `[data-customer="<phone>"]`, with the balance inside `[data-balance]` | Met | Each row of the customer list carries both | `tests/check.mjs`, `tests/sweep.mjs` |
| WhatsApp reminder link | `a[data-remind="<phone>"]`; the link is `https://wa.me/91<phone>?text=<message>` | Met | The WhatsApp button of each row is that link | `tests/check.mjs`, `tests/pay.mjs` |
| Import | a text area `#import-box` and a button `#import-go` | Met | The box and the "Load shop" button under "Load a shop" | `tests/check.mjs` |
| Payment screenshot | a file input `#shot-input`; after saving, the entry shows `img[data-shot]` | Met | "Choose screenshot" is that input; the saved entry shows the picture | `tests/check.mjs` |
| Today's date | anything that depends on the date honours `?today=2026-11-15` | Met | Overdue days, the 30 day figures and the default entry date follow it | `tests/check.mjs` |

## Optional features

The seven optional items, B1 to B7. All seven are built.

| Id | Requirement | Result | How it is met | Test |
| --- | --- | --- | --- | --- |
| B1 | Overdue list, sorted by days since last payment | Met | "Overdue" lists everyone who owes, longest without paying first | `tests/check.mjs` |
| B2 | Edit and delete an entry, with balances recalculated | Met | Edit and Delete on each entry, both with Undo | `tests/check.mjs`, `tests/gaps.mjs` |
| B3 | CSV export that matches the data | Met | "Export entries (CSV)" and "Export balances (CSV)" | `tests/check.mjs`, `tests/final.mjs` |
| B4 | Search by name or phone | Met | "Search by name or phone" above the customer list | `tests/check.mjs`, `tests/final.mjs` |
| B5 | A UPI pay link in the reminder (`upi://pay?pa=...&am=...`) | Met | The reminder carries the UPI link and a tappable pay page link | `tests/check.mjs`, `tests/pay.mjs` |
| B6 | Hindi or Telugu labels | Met | Both, chosen in the language box | `tests/check.mjs`, `tests/layout.mjs` |
| B7 | Reading the amount from the payment screenshot | Met | The amount is read in the browser and filled in for the owner to check | `tests/check.mjs`, `tests/gaps.mjs` |

## Quality

| Id | Requirement | Result | How it is met | Test |
| --- | --- | --- | --- | --- |
| M | The figures are right for any shop loaded through the import box, not only the sample | Met | Thirty other shops are loaded and every balance and total compared with figures worked out apart from the app | `tests/sweep.mjs`, `tests/check.mjs` |
| M | No sideways scroll on a phone 375 px wide | Met | Nothing scrolls sideways in any view | `tests/check.mjs`, `tests/phone-ledger.mjs` |
| M | Readable in light and in dark | Met | Text contrast holds in both themes | `tests/check.mjs`, `tests/contrast.mjs`, `tests/brand.mjs` |

## Beyond the specification

More languages, quick entry, several shops, customer tools and a chat-style layout among others. They are
listed in the README under [What Hisaab supports](README.md#what-hisaab-supports).
