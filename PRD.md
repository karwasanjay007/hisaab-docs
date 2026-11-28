# Hisaab: product requirements

| | |
| --- | --- |
| Product | Hisaab, a digital credit ledger for a local shop |
| Owner | Sanjay Karwa, Smart AI for Business |
| Status | Approved for build (specification in `brief/brief.md`) |
| Last revised | 20 Nov 2026 |

This document says what is being built and for whom, never how.

## 1. The product and the focus

The specification, quoted once:

> A simple digital ledger for a local shop, like the notebook a kirana keeps for who owes what, with a
> WhatsApp reminder in one tap.

Focus:
- The shop owner knows, at a glance on a phone, how much the shop is owed in total and by whom.
- Recording a sale on credit or a payment takes seconds.
- Asking a customer to pay takes one tap, with the right amount already in the message.

| Stage | What the owner can do at the end of it |
| --- | --- |
| 1 | Add customers and entries, see every balance and the total, send a reminder, keep data after reload, load a shop from a file |
| 2 | Attach payment screenshots, see who is most overdue, correct mistakes, search, export, pay-by-UPI in the reminder, use the app in Hindi or Telugu, read the amount from a screenshot |

## 2. Decisions

### 2.1 Decisions taken
| ID | Decision | What it settles | Source |
| --- | --- | --- | --- |
| D-1 | Data stays on the owner's phone; no account or server | Privacy and speed | Specification: "browser storage is fine" |
| D-2 | Loading a shop from a file replaces the current shop, after it is checked | What import means | Specification: "loads a different shop" |
| D-3 | Total outstanding is the sum of every customer's balance (all credit minus all payments), so a customer in advance reduces it; when some customers are in advance, the amount owed by those who owe is shown in a note just below the total, not in the same element [A-1]. Revised 20 Nov 2026: an external review noted that the earlier display began with the owed-only figure, so anyone reading the first number would miss the net figure, and the specification defines balance as all credit minus all payments | Meaning of "total outstanding" | Specification: balance = credit minus payments; external review, 20 Nov 2026 |
| D-4 | Deleting an entry can be undone straight away | Safety without pop-ups | Owner on a phone |

### 2.2 Out of scope
| Item | Why | Where it goes instead |
| --- | --- | --- |
| Sending reminders automatically | Needs a messaging account and consent | Phase 3 |
| Several shops or staff logins | One owner, one phone | Phase 3 |
| Stock and billing | Not asked | Not planned |

## 3. Open questions

| ID | Question | Options | Holds up | Needed before | Owner |
| --- | --- | --- | --- | --- | --- |
| Q-1 | The shop's own UPI ID for pay links | Set in Settings; a clearly marked demo ID until then | UPI link in reminders | Real use | Shop owner |

## 4. Who it is for

- The shop owner: serves customers on credit, keeps a notebook today, checks it on a phone, wants money
  owed back without awkward conversations.
- The customer: receives a polite reminder with the exact amount and a way to pay.

## 5. Requirements

### 5.1 Framework
| ID | Rule |
| --- | --- |
| F-1 | Balance = all credit minus all payments, for each customer |
| F-2 | A customer is identified by a 10 digit phone number; no two customers share one |
| F-3 | Every figure shown is computed from stored entries; nothing is typed in as a total |
| F-4 | Anything that depends on today's date uses the date given in the page address when present |
| F-5 | Data survives closing and reopening the page |

### 5.2 Features by stage
- Stage 1: add customer; add credit or payment with amount, date, note; balances and total outstanding;
  customer page with entries newest first; WhatsApp reminder with name and balance; import a shop file;
  works at 375 px; light and dark.
- Stage 2: payment screenshot shown on the entry; overdue list by days since last payment; edit and
  delete entries; export to a spreadsheet file; search by name or phone; UPI pay link in the reminder;
  Hindi and Telugu labels; amount read from a screenshot.

### 5.3 Scenarios
| ID | Given | When | Then |
| --- | --- | --- | --- |
| S-1 | The sample shop is loaded | The owner opens the app | Total outstanding shows 11,820 and each balance matches the sample's expected file |
| S-2 | A customer owes 3,470 | The owner taps remind | WhatsApp opens to that number with the name and Rs 3,470 in the message |
| S-3 | A payment of 500 is recorded | The page reloads | The payment is still there and the balance is 500 lower |
| S-4 | A file with a missing phone number is loaded | The owner presses Load | Nothing is replaced and the owner is told what is wrong |

## 6. Success measures

| Measure | Target |
| --- | --- |
| Sample shop numbers match the expected file | 100 per cent |
| A different shop loaded through the import box | All totals correct |
| Sideways scroll at 375 px | None |
| Text contrast in light and dark | WCAG AA everywhere |

## Assumptions

- [A-1] "Total outstanding" is the sum of every customer's balance, so money paid in advance reduces it;
  in the sample every balance is owed, so both readings give 11,820.

## Sources and revisions

| Date | Change |
| --- | --- |
| 20 Nov 2026 | First version, from the specification |
| 20 Nov 2026 | D-3 and A-1 revised: total outstanding is the sum of all balances, with the owed figure in brackets when some customers are in advance |
| 20 Nov 2026 | D-3: the owed figure moved out of the total into a note just below it, so the total shows one figure |
