# Hisaab: specification check

| | |
| --- | --- |
| Source | https://dashboard.freedomwithai.com/redesign/v3/projects (Hisaab section) |
| Captured | 20 Nov 2026, afternoon, word for word into `brief/spec-page.md` from the projects page |
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
| Overdue list by days since last payment | Built (full sorted list of everyone who owes; nav badge and Remind-all count only 30+ days without paying) |
| Edit and delete with balances recalculated | Built, both with Undo |
| CSV export that matches the data | Built (entries and balances; reloads to the same totals) |
| Search by name or phone | Built (also Hindi or Telugu name, +91) |
| UPI pay link `upi://pay?pa=...&am=...` | Built (plus QR) |
| Hindi or Telugu labels | Built (both) |
| Amount from the payment screenshot | Built (English, Hindi, Telugu) |

**Estimate:** 100 of 100 on our own checker (hidden data 60, bonus 30 of 35 possible, phone 5, light and
dark 5), on four random shops. That is our checker, not the graders'.

**Version 1.0.0.0 (21 Nov 2026, Sanjay Karwa):** entry type labels are **You gave (credit) / You got (payment)** (radio values
unchanged); Overdue badge and Remind-all use a 30-day rule while the list stays fully sorted for the
bonus; phone overview shows Customers and Overdue first, nothing hidden. Local checker still 27/27. See
`evidence/release-1-0-0-0.md` and `VERSION`. Recapture of the projects page under auth is still **To do**.

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

## After the baseline: versions 1.1.0.0 to 1.2.4.0 (22 to 23 Nov 2026)

Every line of the specification above still holds on the live site after the additions. The ids, attributes,
totals and the import that the tester uses are the same as in 1.0.0.0. No earlier test was edited. The earlier
suites were run again on the live site after the 1.2.0.0 deploy, and all pass.

The same holds after the 1.2.1.0, 1.2.2.0, 1.2.3.0 and 1.2.4.0 deploys (baseline 1.2.3.0 approved by the owner on
23 Nov 2026). The independent checker gives 100 out of 100 on the live site (`evidence/last-run-live.txt`), and
every "Done when" line passes (`evidence/last-brief-live.txt`). The bonus line on the UPI pay link in the
reminder still holds: the reminder now opens with a pay link the customer can tap, and the `upi://pay` link
stays in the message after it.

| What is checked | Latest result | Evidence |
| --- | --- | --- |
| The nine must-do items, word for word | ALL PASS | `evidence/last-brief-live.txt` |
| Hidden data, bonus items, phone and light/dark marks (the independent checker, seed 4242) | 27 pass, 0 fail; 100 out of 100 (hidden data 60 of 60, bonus 30, phone 5, light and dark 5) | `evidence/last-run-live.txt` |
| Hidden-data style checks on many shops | ALL PASS on 61 shops | `evidence/last-sweep-live.txt` |
| Import on a phone | ALL PASS (12 checks) | `evidence/last-phone-import-live.txt` |
| Phone width with the add-ons: 320 and 375 px, three languages, light and dark | ALL PASS (54 checks) | `evidence/last-addons-width-live.txt` |
| Light and dark contrast | ALL PASS | `evidence/last-contrast-live.txt` |
| Other earlier suites: final, gaps, critic, navigation, quick entry, voice, offline | ALL PASS | `evidence/last-final-live.txt`, `evidence/last-gaps-live.txt`, `evidence/last-critic-live.txt`, `evidence/last-nav-live.txt`, `evidence/last-quick-live.txt`, `evidence/last-voice-live.txt`, `evidence/last-offline-live.txt` |

That is still our own checker, not the graders'.

### Beyond the specification

None of these is asked for by the brief. Each one lives in an add-on file beside the ledger.

| Feature | Version | How it is kept apart from what the tester reads | Evidence |
| --- | --- | --- | --- |
| Shop profile: owner, address, logo | 1.1.0.0 | Shown only when set. A plain import into a shop with no profile writes nothing extra to storage | `evidence/last-profile-live.txt` |
| Balance history line | 1.1.0.0 | Only on the customer page, for a customer with two or more entries. It uses none of the attributes the tester reads | `evidence/last-profile-live.txt` |
| More than one shop on the same phone | 1.1.0.0 | The current shop stays under `hisaab.v1` as before. Other shops are parked under `hisaab.shops`, which is not written until a second shop is added. The plain import, the sample and the exports act on the current shop only | `evidence/last-shops-live.txt` |
| Nine more languages, twelve in all | 1.2.0.0 | English, Hindi and Telugu stay built in. A further language is one file, fetched only when chosen. The figures, rows and reminder links are the same in every language | `evidence/last-langs-live.txt`, `evidence/last-i18n-check.txt` |
| Customer page tools: totals, days since the last payment, Call and WhatsApp chat links, change phone number, delete customer with Undo | 1.2.0.0 | A block after the entries on the customer page only. The chat link is not a reminder link, so the count of reminder links is unchanged | `evidence/last-customer-live.txt` |
| Note shortcuts under the note field | 1.2.0.0 | They only fill the note field. The entry is saved by the same button | `evidence/last-langs-live.txt` |
| Total unpaid for 30 days or more, and the date the figures are for | 1.2.0.0 | Drawn beside the existing figures. Nothing is stored | `evidence/last-langs-live.txt` |
| A layout that holds in every language and width | 1.2.1.0 | Layout rules only. No id, attribute or text changed | `evidence/last-layout-live.txt` |
| Customer names in the script of all twelve languages | 1.2.1.0 | The script is shown in brackets after the stored name, as Hindi and Telugu were. The stored name, the total, the rows and the reminder links are the same in every language | `evidence/last-names-live.txt` |
| A pay link the customer can tap in the reminder | 1.2.1.0 | The reminder is still `a[data-remind]` to `https://wa.me/91<phone>?text=`, with the name, the balance and the `upi://pay` link. The pay link is added before the UPI link; its details ride after "#" and are never sent to a server | `evidence/last-pay-live.txt` |
| Other ways to send a reminder: SMS, any WhatsApp contact, copy | 1.2.2.0 | A block on the customer page only. None of its links is a reminder link, so the tester's count of `a[data-remind]` is unchanged | `evidence/last-sendways-live.txt` |
| A WhatsApp check when a customer is added by hand | 1.2.3.0 | Offered only after a customer is added by hand, never after a load. The answer is kept in its own key per number, not in the ledger, and adds no reminder link | `evidence/last-wacheck-live.txt` |
| Voice entry that never ends in silence | 1.2.4.0 | Only what is shown when the browser's speech service fails changed: the silent ending now has a message, and each failure adds a tip. A heard sentence fills the form as before; the earlier voice test passes unchanged | `evidence/last-voice-quiet-live.txt`, `evidence/last-voice-live.txt` |
| Controls that say what they did (`assist.js`) | 1.2.5.0 | `app.js` is not changed. The theme, the views and the skip link behave as before; a status line outside every list and total names what happened. Quick entry keeps its question "Which customer?"; the line and the button under it appear only when a name was typed that no customer has. One reminder link per customer as before | `evidence/last-assist-live.txt` (38), `evidence/last-quick-live.txt`, `evidence/last-brief-live.txt`, `evidence/last-nav-live.txt` |

Not covered by a run: storage-full during a shop switch, and a tab closed between the two writes of a switch
(both traced in the code by the reviewer). The printed statement was checked by its markup, not on paper. No
native speaker has read the nine new languages. Since 1.2.1.0, also not checked by a person: the spellings of
names in the nine newer scripts, the new strings in those languages, and the SMS link, the pay page and the
WhatsApp check on a real phone.

## After the baseline: versions 1.2.6.0 to 1.4.0.0 (27 Nov 2026)

Each version was added on top, with nothing removed. The ids, attributes, totals and the import that the tester
uses are the same. One rule changed at the owner's word, as the course guideline asks: money is kept in whole
rupees. An import with paise is rounded to the nearest rupee, halves up, and says how many; four shops of the
sweep that hold paise now expect whole rupees. Screenshots are kept at most 600 px (640 px before).

| What is checked | Latest result | Evidence |
| --- | --- | --- |
| Hidden data, bonus items, phone and light/dark marks (the independent checker, seed 4242, live at 1.4.0.0) | 27 pass, 0 fail; 100 out of 100 | `evidence/last-check-live.txt` |
| Storage and input guards (`guard.mjs`) | ALL PASS (48 checks) | `evidence/last-guard-live.txt` |
| Whole rupees (`rupees.mjs`) | ALL PASS (28 checks) | `evidence/last-rupees-live.txt` |
| Brand look, no green (`brand.mjs`) | ALL PASS (184 checks) | `evidence/last-brand-live.txt` |
| Phone layout (`phone-ledger.mjs`) | ALL PASS (147 checks) | `evidence/last-phone-ledger-live.txt` |
| Layout, navigation, quick entry | ALL PASS | `evidence/last-layout-live.txt`, `evidence/last-nav-live.txt`, `evidence/last-quick-live.txt` |

The other suites passed on the local copy at 1.4.0.0 and were not rerun on the live link for this version. No
fresh reviewer has read 1.3.0.0 or 1.4.0.0 yet.

## Version 1.4.1.0 (27 Nov 2026)

Added on top, with nothing removed; the ids, attributes, totals and the import the tester uses are the same. The
+ sheet follows the chosen language; the fonts and the QR library are served from the site itself; the ledger
maths have unit tests; the customer page and the overdue line use the one balance function.

| What is checked | Latest result | Evidence |
| --- | --- | --- |
| Hidden data, bonus items, phone and light/dark marks (the independent checker, seed 4242, live at 1.4.1.0) | 27 pass, 0 fail; 100 out of 100 | `evidence/last-check-live.txt` |
| No other host on a plain visit, a QR code or a language change; the + sheet in Hindi, Telugu and Urdu (`local-assets.mjs`) | ALL PASS (13 checks) | `evidence/last-local-assets-live.txt` |
| Phone layout (`phone-ledger.mjs`) | ALL PASS (147 checks) | `evidence/last-phone-ledger-live.txt` |
| Ledger maths (`ledger.cjs`, in Node, no browser) | ALL PASS (27 checks) | run on the code |

The other suites passed on the local copy at 1.4.1.0 and were not rerun on the live link for this version.

Version 1.5.0.0 (27 Nov 2026), on top with nothing removed: the desktop overview of a shop with customers is laid out like the best chat apps (`compact.css`, `compact.js`); live, the checker 27 pass, 0 fail (100 out of 100, `evidence/last-check-live.txt`), and the desktop layout (93 checks) and the phone layout (147 checks) ALL PASS (`evidence/last-desk-ledger-live.txt`, `evidence/last-phone-ledger-live.txt`).

Version 1.5.1.0 (27 Nov 2026), on top with nothing removed: Urdu reads right to left and the desktop holds a 40-customer shop; live, the checker 27 pass, 0 fail (100 out of 100), and desk-ledger (149 checks), phone-ledger (147) and langs (25) ALL PASS (`evidence/last-desk-ledger-live.txt`, `evidence/last-phone-ledger-live.txt`, `evidence/last-langs-live.txt`).

Version 1.5.1.0 (27 Nov 2026), on top with nothing removed: Urdu reads right to left and the desktop holds a 40-customer shop; live, the checker 27 pass, 0 fail (100 out of 100), and desk-ledger (149 checks), phone-ledger (147) and langs (25) ALL PASS (`evidence/last-desk-ledger-live.txt`, `evidence/last-phone-ledger-live.txt`, `evidence/last-langs-live.txt`).
