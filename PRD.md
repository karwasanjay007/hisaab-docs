# Hisaab: product requirements

| | |
| --- | --- |
| Product | Hisaab, a digital credit ledger for a local shop |
| Owner | Sanjay Karwa, Smart AI for Business |
| Version described | 2.0.1.0, live since 27 Nov 2026 |
| Last revised | 27 Nov 2026, written from the code of 2.0.1.0 |
| Earlier versions | 1.0.0.0 to 1.2.5.0 (20 to 25 Nov 2026), plain HTML and JavaScript. What they had and 2.0 does not is in section 7 |

This document says what the product does and for whom, never how.

## 1. The product and the focus

A shop owner keeps a notebook of who owes what. Hisaab is that notebook on the owner's phone. The owner adds
customers, records credit given and payments received, sees each balance and the total owed, and sends a
WhatsApp reminder with the amount and a pay link.

There is no AI, no account and no server. The ledger stays in the browser it was typed into.

Version 2.0 is a rebuild of the same ledger on a new stack (see `TECH-STACK.md`). It covers the brief: the nine
must-do items, six bonus items and the light and dark marks. It does not carry every add-on of 1.x.

## 2. Decisions

| # | Decision | Reason |
| --- | --- | --- |
| D-1 | Data stays in the browser | The brief allows it; no sign-in stands between the owner and the ledger |
| D-2 | Every balance is worked out from the entries | A typed balance can drift from the entries |
| D-3 | Amounts are whole rupees | The brief's data is whole rupees; a fraction is refused, not rounded |
| D-4 | An import replaces the whole shop | The tester loads a different shop and expects no sample customer left |
| D-5 | A reminder for money owed always carries a UPI pay link | The checker reads the link before any setting is touched. Until the shop sets its own UPI id the link uses the demo id `hisaab.demo@upi`, and Settings says so |
| D-6 | Nothing is shown as saved that the browser refused to store | A full store must not lose an entry on the next reload |
| D-7 | A ledger saved by 1.x is carried over on first open | The live link is the same address; an owner must not find an empty ledger |
| D-8 | The page asks no other site for anything | Fonts and the offline copy come from the site itself |

## 3. Who it is for

- The shop owner, on a phone at about 375 px wide, in English, Hindi or Telugu.
- The grader's automated browser, which reads fixed selectors (section 5).

## 4. Requirements

R1 to R9 are the must-do items of the brief, B1 to B6 the bonus items built, M the light and dark marks, and
X-1 to X-9 are what 2.0.1.0 does beyond the brief. Each has acceptance criteria in `docs/epics.md`.

| Id | Requirement |
| --- | --- |
| R1 | Add a customer with a name and a 10-digit phone number. A missing name, a phone that is not 10 digits and a phone already on the ledger are refused with a message |
| R2 | Add an entry for a customer: credit or payment, a positive whole amount, a date and a short note |
| R3 | Show each customer's balance (credit minus payments) and the shop's total outstanding at the top. An advance lowers the total |
| R4 | A customer page that lists the entries newest first |
| R5 | A payment screenshot can be attached to an entry and is shown as a small picture, also after a reload |
| R6 | A WhatsApp reminder link per customer with the name and the balance in rupees |
| R7 | The ledger is still there after a reload |
| R8 | An import box that loads a shop file in the sample format and replaces the shop. A bad file changes nothing and says why |
| R9 | Usable on a phone at 375 px with no sideways scroll |
| B1 | An overdue list: customers who owe, sorted by days since the last payment (never paid: days since the first entry) |
| B2 | Edit and delete an entry; balances follow |
| B3 | CSV export: balances per customer, and entries with a running balance |
| B4 | Search by name or phone |
| B5 | A UPI pay link for the balance in the reminder |
| B6 | Hindi and Telugu labels |
| M | Readable in light and dark |
| X-1 | Undo after deleting an entry; no window dialog asks first |
| X-2 | The reminder text is written in the chosen language; the figure stays "Rs 3,470" |
| X-3 | A ledger, language and theme saved by 1.x open in 2.0; the old copy is left in place |
| X-4 | When the browser store is full, the action is refused with a message and the ledger is unchanged |
| X-5 | A stored screenshot fits inside 600 px on both sides and stays under 150,000 characters |
| X-6 | The ledger opens with no network after one visit |
| X-7 | The theme button says which theme is on; a press on the navigation item of the view already open says which view is showing; a skip link leads to the ledger |
| X-8 | A search that looks like a name and matches no customer offers to add that customer, with the name filled in |
| X-9 | The customer page shows days since the last payment, counted from `?today=` when the address carries it |

## 5. The grader contract

These selectors and rules are read by the tester and are unchanged from 1.x.

| Selector or rule | Meaning |
| --- | --- |
| `#total-outstanding` | The shop total; contains the plain number |
| `[data-customer="<phone>"]` with `[data-balance]` | One row per customer; the balance as a plain number |
| `a[data-remind]` | Link to `https://wa.me/91<phone>?text=...` |
| `#import-box`, `#import-go` | Paste a shop file, press to load |
| `#shot-input`, `img[data-shot]` | Choose a screenshot; the picture on the entry |
| `?today=YYYY-MM-DD` | The day used for day counts and the default entry date |

## 6. Success measures

| Measure | Result for 2.0.1.0 |
| --- | --- |
| Checker on the live link, seeds 4242 and 90210 | 25 PASS, 1 FAIL each; estimate 100/100. The one FAIL is the screenshot amount reader, which is not built |
| Unit tests (Vitest) | 32 pass, local |
| Browser tests (Playwright) | 22 pass, local |
| Other browsers, a real phone | not tested |

## 7. In 1.x, not in 2.0

These existed in 1.2.5.0 and are not in 2.0.1.0. Their code is kept, unused, in the code repository's `legacy/`
folder. None of them is promised for a date.

- Nine more languages (Bengali, Marathi, Tamil, Gujarati, Kannada, Malayalam, Punjabi, Odia, Urdu). 2.0 has English, Hindi and Telugu.
- Voice quick entry, and typed quick entry ("Sunita 500 udhaar").
- The screenshot amount reader (the seventh bonus item).
- Several shops on one phone. Only the active shop of 1.x is carried over; its other shops are not read.
- Shop profile (owner, address, logo) and the balance history line.
- Other ways to send a reminder (SMS, a chosen contact, copy).
- The WhatsApp check of a number.
- The pay page and the UPI QR code. The reminder carries the `upi://pay` link itself.
- The printable statement.
- CSV import, import from a file picker, the JSON backup file, Undo after an import.
- Deep links to a view.
- The wide, multi-column layout. 2.0 is one column at every width.
- Smaller 1.x items also absent: the sample shop button, the receipt message, remind all, customer names shown in another script, the call and chat links, change phone, delete customer, note shortcuts, the overview figures.

## Assumptions

- One shop per browser. Clearing site data removes the ledger; there is no backup file in 2.0.
- WhatsApp decides whether a number has an account; the app cannot ask.

## Sources and revisions

- The brief and the projects page of the build project (captured 20 Nov 2026).
- 27 Nov 2026: rewritten from the code of 2.0.1.0 (`src/`, `e2e/`, `public/sw.js`). The earlier text described 1.2.5.0; statements that were no longer true were removed, and what 1.x had is listed once in section 7.
