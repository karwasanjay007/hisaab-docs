# Hisaab: specification check

| | |
| --- | --- |
| Source | https://dashboard.freedomwithai.com/redesign/v3/projects (Hisaab section) |
| Captured | 20 Nov 2026, afternoon, Claude in Chrome get_page_text, word for word in `brief/spec-page.md` |
| Recaptured before submitting | To do on 21 Nov before 2:00 pm IST; the page changed once already (both projects may now be submitted) |
| Literal test | `tests/brief.mjs`, live result `tests/last-brief-live.txt` |


Checked against the projects page word for word (`brief/spec-page.md`), not our earlier summary of it.
Every "Done when" line is followed literally by `tests/brief.mjs` in a fresh browser; live result
`tests/last-brief-live.txt`: ALL PASS.

| # | Must do | Done when (abridged) | Result | Evidence |
| --- | --- | --- | --- | --- |
| 1 | Add a customer | Ravi Kumar 9000000101 on the home list; 9 digits or letters refused with a message | Met | brief.mjs: 9 digits, "90000abc01", "9000000101a", "abcdefghij" refused; Ravi added |
| 2 | Credit or payment with amount, date, note | Rs 250 credit, balance reads Rs 250 | Met | brief.mjs: Ravi 250, total 250 |
| 3 | Balances and total at the top | Sample: top 11820, Ravi 670; computed | Met | brief.mjs; check.mjs [1-sample total] and [2-hidden balances] on four seeds |
| 4 | Customer page newest first | 25 Oct Rs 1,500 above 23 Oct Rs 180 | Met | brief.mjs rows 0 and 1 |
| 5 | Payment screenshot as a small picture | Shows after a reload | Met | brief.mjs; check.mjs [5-shot img], [5-shot reload] |
| 6 | WhatsApp reminder | Name and Rs 670 in the message | Met | brief.mjs ("Rs 670"); check.mjs [3-reminder] |
| 7 | Survives a reload | Entry and balance still there | Met | brief.mjs; check.mjs [4-persistence], [2-hidden persistence] |
| 8 | Import box | Sample shows 8 customers; a customer with no entries loads | Met | brief.mjs; check.mjs [2-hidden replace] |
| 9 | Phone, 375 px | No sideways scroll; every button easy to tap | Met | brief.mjs: five views, scroll width 375, no target under 40 px |

| Selector or rule | Result |
| --- | --- |
| `#total-outstanding` contains the number | Met: "₹11,820" plus the plain 11820 |
| `[data-customer]` with `[data-balance]` | Met |
| `a[data-remind]` to `https://wa.me/91<phone>?text=` | Met |
| `#import-box` and `#import-go` | Met (also accept CSV) |
| `#shot-input` and `img[data-shot]` | Met |
| `?today=` | Met: overdue days, default dates and the date limit follow it |

| Bonus (5 each, 6 count) | Result |
| --- | --- |
| Overdue list by days since last payment | Built |
| Edit and delete with balances recalculated | Built, both with Undo |
| CSV export that matches the data | Built (entries and balances; reloads to the same totals) |
| Search by name or phone | Built (also Hindi or Telugu name, +91) |
| UPI pay link `upi://pay?pa=...&am=...` | Built (plus QR) |
| Hindi or Telugu labels | Built (both) |
| Amount from the payment screenshot | Built (English, Hindi, Telugu) |

**Estimate:** 100 of 100 on our own checker (hidden data 60, bonus 30 of 35 possible, phone 5, light and
dark 5), on four random shops. That is our checker, not the graders'.

**What could still cost marks:**
- The graders' tester is an AI reading the page; its exact steps are unknown. The likeliest differences
  are covered: a fresh browser now starts empty, totals carry both the formatted and the plain number, and
  no dialog ever blocks it.
- Total outstanding when a customer is in advance: #total-outstanding holds only the sum of all balances
  (the brief's balance is all credit minus all payments); the owed-only figure is in the note #total-note
  just below it, so a tester that reads only #total-outstanding gets the net figure.
- Reading the amount from a real UPI screenshot is tested only on generated images.
- Found late by reading the page word for word: the first-visit sample would have failed must-do 1 and 2.
  The lesson is in the kickoff kit (capture the page verbatim, test every "Done when" literally).
