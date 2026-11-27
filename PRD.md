# Hisaab: product requirements

| | |
| --- | --- |
| Product | Hisaab, a digital credit ledger for a local shop |
| Owner | Sanjay Karwa, Smart AI for Business |
| Status | Approved for build (FWAI Batch 10 build project; brief in `brief/brief.md`) |
| Last revised | 27 Nov 2026 (version 1.4.1.0) |
| Additions | Versions 1.1.0.0 to 1.4.1.0 (22 to 27 Nov 2026): see the last section of this document |

This document says what is being built and for whom, never how.

## 1. The product and the focus

The brief, quoted once:

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
| D-1 | Data stays on the owner's phone; no account or server | Privacy and speed | Brief: "browser storage is fine" |
| D-2 | Loading a shop from a file replaces the current shop, after it is checked | What import means | Brief: tester "loads a different shop" |
| D-3 | Total outstanding is the sum of every customer's balance (all credit minus all payments), so a customer in advance reduces it; when some customers are in advance, the amount owed by those who owe is shown in a note just below the total, not in the same element [A-1]. Revised 20 Nov 2026: an external review noted that the earlier display began with the owed-only figure, so a tester reading the first number would miss a net expected value, and the brief defines balance as all credit minus all payments | Meaning of "total outstanding" | Brief: balance = credit minus payments; external review, 20 Nov 2026 |
| D-4 | Deleting an entry can be undone straight away | Safety without pop-ups | Owner on a phone |

### 2.2 Out of scope
| Item | Why | Where it goes instead |
| --- | --- | --- |
| Sending reminders automatically | Needs a messaging account and consent | Phase 3 |
| Staff logins | One owner, one phone. Several shops on one phone is built since 1.1.0.0 (V-3) | Phase 3 |
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
| 20 Nov 2026 | First version, from the FWAI brief |
| 20 Nov 2026 | D-3 and A-1 revised: total outstanding is the sum of all balances, with the owed figure in brackets when some customers are in advance |
| 20 Nov 2026 | D-3: the owed figure moved out of the total into a note just below it, so the total shows one figure |
| 23 Nov 2026 | Section added for the additions of versions 1.1.0.0 and 1.2.0.0 |
| 23 Nov 2026 | Requirements traced back from the app: V-11 to V-19 added for what 1.0.0.0 does beyond sections 1 to 5; section 2.2 corrected (several shops is built) |
| 23 Nov 2026 | Baseline 1.2.3.0 approved by the owner: V-20 to V-24 added for versions 1.2.1.0, 1.2.2.0 and 1.2.3.0; an out-of-scope row for an automatic check of a number; the note on who the additions are for corrected (the customer now gets a pay link, and a hello message when the owner checks a number) |
| 23 Nov 2026 | Version 1.2.4.0: V-24 now says that a wrong answer is put right with one tap (built in 1.2.3.1); V-25 added, voice quick entry never ends in silence |
| 25 Nov 2026 | Version 1.2.5.0: V-26 added after the project feedback, controls that say what they did and quick entry that offers to add a customer it does not know |
| 27 Nov 2026 | Versions 1.2.6.0 to 1.4.0.0: V-27 to V-30 added after a check against the course build approach, each on top and with nothing removed |
| 27 Nov 2026 | Version 1.4.1.0: V-31 added, on top and with nothing removed |

## Versions 1.1.0.0 to 1.2.4.0: additions (22 to 23 Nov 2026)

Why they were added. After the first release the app was compared with other builds of the same batch. Only
the screens of those builds were read. What they showed that Hisaab did not have was added, in two steps:
three additions in 1.1.0.0 and seven in 1.2.0.0. Versions 1.2.1.0 to 1.2.4.0 came from the owner's own
use of the live site: three additions in 1.2.1.0, one in 1.2.2.0, one in 1.2.3.0 (put right in 1.2.3.1) and
one in 1.2.4.0. The sections above still
describe version 1.0.0.0.

Who they are for. Every addition is for the shop owner. The customer still receives the reminder with the same
amount. Since 1.2.1.0 it also carries a pay link the customer can tap (V-22), and since 1.2.3.0 a customer
added by hand may get a short hello message when the owner checks the number (V-24).

Requirements:

| ID | Version | Feature | Requirement |
| --- | --- | --- | --- |
| V-1 | 1.1.0.0 | Shop profile | The owner can save an owner name, an address and a small logo. They show in the header and on the printed statement and travel in the backup file. Nothing shows until they are set |
| V-2 | 1.1.0.0 | Balance history line | On a customer page with two or more entries, a line shows the balance after each entry, with a sentence for screen readers |
| V-3 | 1.1.0.0 | More than one shop on the same phone | The owner can add a shop, switch between shops, remove one in two presses, and load a file as a new shop. Each shop keeps its own ledger |
| V-4 | 1.2.0.0 | Nine more languages | Bengali, Marathi, Tamil, Gujarati, Kannada, Malayalam, Punjabi, Odia and Urdu join English, Hindi and Telugu: twelve in all. Quick entry knows each language's words for "gave" and "got" |
| V-5 | 1.2.0.0 | Customer page totals and days since the last payment | The customer page shows the total given on credit, the total received and the days since the last payment |
| V-6 | 1.2.0.0 | Call and WhatsApp chat links | From a customer page the owner can call the customer or open a WhatsApp chat with no message. The reminder with the name and the balance stays a separate button |
| V-7 | 1.2.0.0 | Change a customer's phone number | The owner can give a customer a new 10 digit number. The customer and every entry move to it. A number another customer has is refused (F-2) |
| V-8 | 1.2.0.0 | Delete a customer with Undo | Deleting takes two presses and removes the customer and their entries. Undo is offered straight away and puts the same entries back (as D-4 does for an entry) |
| V-9 | 1.2.0.0 | Note shortcuts | Six buttons under the note field fill in a common note: groceries, milk, rice, oil, paid on UPI, cash. The entry is saved by the same button as before |
| V-10 | 1.2.0.0 | Total unpaid for 30 days or more, and the date of the figures | A line above the overdue list gives the amount unpaid for 30 days or more and the number of customers. The date the figures are for shows under the total (F-4) |

Built in version 1.0.0.0 but not stated in sections 1 to 5 (found when the requirements were traced back from the app on 23 Nov 2026):

| ID | Version | Feature | Requirement |
| --- | --- | --- | --- |
| V-11 | 1.0.0.0 | Quick entry, typed or spoken | The owner can type or say one line, such as a name, gave or got, and an amount, and the entry form fills in. An unclear line asks the owner to choose, and nothing is saved until the owner saves |
| V-12 | 1.0.0.0 | Load a spreadsheet | Besides a shop file, the owner can load a spreadsheet with name, phone, type, amount and date columns. Rows that cannot be read are skipped and listed |
| V-13 | 1.0.0.0 | Backup and balances files | Besides the entries spreadsheet, the owner can save a balances spreadsheet with a total row and a full backup file that loads back |
| V-14 | 1.0.0.0 | Key figures and recent entries | The overview shows who owes the most, what was collected and given in the last 30 days, who has gone longest without paying, and the newest entries |
| V-15 | 1.0.0.0 | Remind all overdue | One button opens the reminders for every overdue customer, one at a time. The owner still presses Send each time |
| V-16 | 1.0.0.0 | Pay code and statement | A customer page shows a pay code for the balance and gives a statement that can be printed or saved |
| V-17 | 1.0.0.0 | Receipt after an entry | After an entry is saved, the owner can send the customer a receipt message with the new balance |
| V-18 | 1.0.0.0 | Names and digits in the local script | The owner can correct a customer's name and add its Hindi and Telugu spelling. Figures can be shown in Hindi or Telugu digits |
| V-19 | 1.0.0.0 | Works offline and can be installed | After the first visit the ledger opens with no network and can be added to the home screen. Reading the amount from a screenshot still needs a network |

Added in versions 1.2.1.0 to 1.2.4.0 (23 Nov 2026), from the owner's use of the live site:

| ID | Version | Feature | Requirement |
| --- | --- | --- | --- |
| V-20 | 1.2.1.0 | A layout that holds in every language and width | For the owner, who reads the app on a phone in any of the twelve languages. The customer heading never breaks into a column of letters and never runs under the buttons. The customer page has no blank band between cards. Entries stack inside a narrow card, and long labels wrap instead of being cut off. Nothing scrolls sideways |
| V-21 | 1.2.1.0 | Customer names in the script of every language | For the owner who reads one of the nine newer languages, so that names read the same way as the labels. Each name is followed by its spelling in the chosen language's script, as Hindi and Telugu already did. The spelling is made automatically. The total, the rows and the reminder links stay the same |
| V-22 | 1.2.1.0 | A pay link the customer can tap | For the customer, because WhatsApp does not make a UPI link tappable. The WhatsApp reminder carries an ordinary web link first. It opens a small pay page with the shop, the amount and the UPI ID, and its button opens the UPI app. The UPI link stays in the message, as the brief asks. The details in the link are never sent to a server |
| V-23 | 1.2.2.0 | Other ways to send a reminder | For the owner, when WhatsApp refuses a number. The same reminder can go as an SMS to the customer's number, to any contact chosen in WhatsApp, or be copied. A sample number says that it is made up |
| V-24 | 1.2.3.0 | Check a number on WhatsApp | For the owner, so that a refusal is found once, when the customer is added, and not at every reminder. After a customer is added by hand, the owner can send a hello message to the number with the name and the shop and no balance. The owner then says whether the chat opened. The answer is kept for that number, apart from the ledger. A refused number reads "Not on WhatsApp" and offers the other ways to send (V-23). A wrong answer is put right with one tap, without opening WhatsApp again (1.2.3.1). No app can look a number up beforehand; this is a limit, not a choice |
| V-25 | 1.2.4.0 | Voice entry never ends in silence | For the owner who speaks the entry, so that a try that hears nothing is never left showing "Listening" with no word on screen. When the speech service stops without hearing anything, or cannot be reached, the app says so, for example "Did not catch that". Every failure also names the way that always works: the microphone key of the keyboard on a phone or tablet, and the computer's own dictation on a laptop, including a laptop with a touch screen. The message is in the chosen language and fits a phone screen. When the sentence is heard, the form fills and no tip shows |
| V-26 | 1.2.5.0 | Controls that say what they did | For the owner, and for anyone using a screen reader or a keyboard, so that no press looks as if nothing happened. The light and dark button says which theme is on. A press on a navigation item names the view that is showing, also when it was already open. The skip link puts the focus on the ledger. When quick entry holds a name that no customer has, the app says so under its question and offers to add that customer with the name filled in |
| V-27 | 1.2.6.0 | Nothing is lost without a word | For the owner. A change the browser store refused is said plainly, and "Saved." is not left standing. When the browser mode keeps nothing, a lasting notice says so and points to a normal window or a backup file. A picture in an imported file that is not an image or is too large is left out, the entry kept, and the count told. An edit with the date cleared stays open and asks for the date. Screenshots are kept at most 600 px on the long side. The shop name is whole on a phone and small labels are at least 12 px |
| V-28 | 1.3.0.0 | Money in whole rupees | For the owner, as the course guideline asks. A new entry or an edit with paise is not saved: the form stays open and offers the nearest whole amount in one tap. An import with paise is rounded to the nearest rupee, halves up, and says how many were rounded. Amounts already stored with paise are shown as stored, with one line that offers to round them and an Undo. Whole-rupee data shows nothing new |
| V-29 | 1.3.0.0 | The brand look | For the owner and the judges. Navy, cream, slate blue and amber; Fraunces for headings and Inter for text, with the Indian-script fonts kept first for those languages; the sai4b mark first in the top bar beside the Hisaab icon (the mark alone on a phone). No green anywhere: paid, the payment choice, success lines and the WhatsApp buttons are slate and navy |
| V-30 | 1.4.0.0 | The ledger gets the phone screen | For the owner on a phone, like the best chat apps. A 56 px top bar, a compact total, quick entry as one pill at the bottom (type or speak "Sunita 500 udhaar" and send), a slim tab row, and a + sheet with every other action. With the sample shop, the customer rows fully on the first screen go from 0 to 4 at 375 x 667 and from 1 to 8 at 390 x 844. The desktop layout is as before |
| V-31 | 1.4.1.0 | Every word in the chosen language, and nothing from another host | For the owner. The + sheet's labels and button names follow the chosen language in all twelve. The fonts and the QR library are served from the site itself, so a plain visit, a QR code and a language change ask no other host; only the screenshot reader still loads on demand from its host. The ledger maths are tested on their own, and the customer page and the overdue line use the one balance function |

Constraints:

| ID | Constraint |
| --- | --- |
| C-1 | Every addition is an add-on file beside the ledger. The nine "must do" items and the bonus items of the brief behave as before, and the import the tester uses is unchanged |
| C-2 | An addition shows only where it is used or once it is set. A shop with no profile stores nothing extra |
| C-3 | With one shop nothing is stored beyond the ledger's own key. The plain import, the sample and the exports act on the current shop only |
| C-4 | English, Hindi and Telugu stay built in. A further language is fetched only when chosen. The total, the rows and the reminder links are the same in every language |

Out of scope for these versions:

| Item | Why | Where it goes instead |
| --- | --- | --- |
| A server or an account | D-1 still holds: everything stays in the browser, on the owner's phone | Not planned |
| Staff logins | Several shops on one phone is now in (V-3, and the row in section 2.2 now says so); logins need an account | Phase 3 |
| Translation review by a native speaker | The nine new languages were checked by a script for keys, placeholders and script, not read by a native speaker | Still to come |
| Voice entry in the nine new languages | The browser's speech service is used in English | Not planned |
| An automatic check that a number is on WhatsApp | WhatsApp gives no lookup to an ordinary app. The only route is a business account, a server and a cost for each message, and D-1 rules out the server: nothing leaves this phone | Not planned |
