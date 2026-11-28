# Hisaab: khata, made simple

This page describes version 1.5.2, the version on the live link.

Hisaab is a shop credit ledger for a small shop owner in India: who owes what, every payment, and a WhatsApp
reminder with a pay link in one tap.

| | |
| --- | --- |
| Live | https://hisaab-hazel.vercel.app |
| Documents | This repository; the map is under [Documents](#documents) |
| Code | Private |
| Built by | Sanjay Karwa, Smart AI for Business |
| Made with | Plain HTML, CSS and JavaScript. No server of its own: the ledger is kept in the browser of the device, and the app works offline after one visit |
| Versions | 1.5.2.1 is live; the first release is kept for comparison. See [what changed in each version](CHANGELOG.md) |
| Status | Live. Every requirement of the specification is met ([the specification check](SPEC-CHECK.md)) and all 32 test records from the live link end in a pass ([latest results](REPORT.md#latest-results-on-the-live-link)) |

## Try it in two minutes

Open https://hisaab-hazel.vercel.app on a phone or a computer. No sign-in is asked for.

1. **Load the sample shop.** A first visit shows an empty shop called "My shop". Under Customers press
   **Load sample shop**. The shop name becomes "Lakshmi Provision Store (fictional)" and eight customers appear,
   with the message "Shop loaded." and an **Undo**.
2. **See the total and who is overdue.** **Total outstanding** at the top reads ₹11,820. Sunita Devi carries the
   badge "Owes the most" (₹3,470). Open **Overdue**: everyone who owes is listed, longest without paying first,
   and those at 30 days or more carry the badge "30+ days". To see the figures as on a fixed day, add
   `?today=2026-11-15` to the address; the date the figures are for shows under the total.
3. **Record a payment.** Tap **Ravi Kumar** (balance ₹670). On his page choose **You got (payment)**, type 200
   in **Amount (₹)** and press **Save entry**. "Saved." appears with a **Send receipt** link, the balance becomes
   ₹470, and the entry is first in "Entries, newest first".
4. **Send a WhatsApp reminder.** On the same page press **WhatsApp reminder**. WhatsApp opens with the message
   ready: his name, the balance in rupees and a line "UPI pay link:" that a customer can tap. Nothing goes until
   you press Send in WhatsApp. The sample numbers are not real, so the page says so and offers **Change phone
   number**; put in your own number to receive the message. **UPI QR code** shows the same payment as a code to
   scan, and **Statement (PDF)** prints the account.
5. **Try quick entry.** Go back to the first screen. In the box "Quick entry: type or speak it" type
   `Sunita 500 udhaar` and press the arrow at the end of the box. It becomes a credit of ₹500 for Sunita Devi,
   dated today, and her balance goes from ₹3,470 to ₹3,970. `Ravi paid 200` becomes a payment. The microphone
   button takes the same sentence by voice where the browser supports it.
6. **Change the language and the theme.** In the language box in the top bar (an icon button on a phone) choose
   हिंदी: every label changes and each name is also shown in Devanagari. Choose اردو and the page runs right to
   left. The button beside it switches light and dark.
7. **Go offline.** Turn on flight mode or switch the network off, then reload. The app opens with the shop
   as you left it, and the QR code still draws.

## What Hisaab supports

Hisaab was built against a written specification. R1 to R9 are its required items, B1 to B7 its optional
features and M its quality requirement. N/A is a feature beyond the specification.

| Requirement | Feature | Supported |
| --- | --- | --- |
| R1 | Add a customer with a name and a 10 digit phone number | Yes; a wrong or repeated number is refused with a message |
| R2 | Record what you gave on credit or what you got as payment, with amount, date and note | Yes; whole rupees, one entry up to ₹1,00,00,000 |
| R3 | See each customer's balance and the shop's total outstanding at the top | Yes; worked out from the entries each time |
| R4 | Open a customer and read the entries, newest first | Yes; each entry also shows the balance after it |
| R5 | Attach a payment screenshot and see it as a small picture on the entry | Yes |
| R6 | Send a WhatsApp reminder with the customer's name and the balance in rupees | Yes; one tap per customer |
| R7 | Find everything still there after a reload | Yes |
| R8 | Load another shop by pasting its file into the import box | Yes; JSON or a spreadsheet saved as CSV, pasted or chosen as a file, with Undo |
| R9 | Use it on a phone 375 px wide | Yes; nothing scrolls sideways |
| B1 | See who is overdue, longest without paying first | Yes; 30 days or more is marked |
| B2 | Edit or delete an entry and see the balances follow | Yes; both with Undo |
| B3 | Export a CSV that matches the data | Yes; entries and balances |
| B4 | Search by name or phone | Yes; also by the name in Hindi or Telugu |
| B5 | Put a UPI pay link in the reminder | Yes; a tappable pay page link and the UPI link itself |
| B6 | Read the labels in Hindi or Telugu | Yes; both |
| B7 | Have the amount read from the payment screenshot | Yes; you check it before saving |
| M | Read it in light and in dark | Yes; follows the device, with a button to switch |
| N/A | Twelve languages | English, Hindi, Telugu, Bengali, Marathi, Tamil, Gujarati, Kannada, Malayalam, Punjabi, Odia and Urdu; Urdu runs right to left |
| N/A | Customer names in the script of the chosen language | Yes; the owner can correct the Hindi and Telugu spelling |
| N/A | Quick entry by typing a sentence or speaking it | Yes; voice where the browser has speech recognition |
| N/A | UPI QR code for the balance | Yes; on the customer page and on the statement |
| N/A | Statement of a customer's account as a PDF | Yes; through the browser's print |
| N/A | Remind every overdue customer in turn | Yes; one press per chat |
| N/A | A WhatsApp receipt after an entry | Yes; a link on the "Saved." message |
| N/A | Other ways to send a reminder | SMS, a contact chosen in WhatsApp, or copy the message |
| N/A | Check that a customer's number is on WhatsApp | Yes; the answer is remembered per number |
| N/A | Customer tools | Totals, days since the last payment, call, WhatsApp chat, change phone number, delete with Undo |
| N/A | Balance over time for a customer | Yes; a line on the customer page |
| N/A | Several shops on one device | Yes; add, switch, remove, import as a new shop |
| N/A | Shop profile | Owner name, address and logo, shown in the top bar and on statements |
| N/A | Overview figures beyond the total | Owes the most, collected and given in the last 30 days, longest without paying, total unpaid for 30 days or more |
| N/A | Note shortcuts | Six words to tap into the note |
| N/A | Backup file and a sample shop | A JSON backup to download; a sample shop in one tap |
| N/A | Chat-style layout | Quick entry as one bar at the bottom with a "+" menu of every action; a right panel on wide screens |
| N/A | Works offline after one visit | Yes; fonts and the QR library come from the site itself |
| N/A | Says so when a change could not be kept | Notices for a full or blocked browser store, with a backup button |

Each feature with its acceptance criteria and the test that proves it is in [the epics](docs/epics.md).

## Documents

| Document | Read it for |
| --- | --- |
| [PRD.md](PRD.md) | Who it is for, the problem, the requirements, what is out of scope |
| [SPEC-CHECK.md](SPEC-CHECK.md) | Each requirement of the specification, whether it is met and the test that shows it |
| [REPORT.md](REPORT.md) | The latest results on the live link, known limits, what was not tested |
| [ARCHITECTURE.md](ARCHITECTURE.md) | The parts, the files, hosting, offline |
| [TECH-STACK.md](TECH-STACK.md) | Each technology chosen, why, and what it costs |
| [IMPLEMENTATION-PLAN.md](IMPLEMENTATION-PLAN.md) | How to run it, test it and release it |
| [docs/epics.md](docs/epics.md) | Epics, features and acceptance criteria, each with its test |
| [docs/journeys.md](docs/journeys.md) | Twelve journeys step by step, each with its diagram |
| [docs/data-model.md](docs/data-model.md) | Where every piece of data is kept and its fields |
| [docs/schema/](docs/schema/) | The shop file, the CSV exports and the links the app builds |
| [evidence/](evidence/) | The test records from the live link, one file per test |
| [CHANGELOG.md](CHANGELOG.md) | The two versions kept, what differs, and what changed in each version |

## Privacy

- **What is kept, and where.** The shop, its customers, entries, screenshots and settings are kept in the browser
  of the device you use. Hisaab has no server of its own, no account and no analytics
  ([where data is kept](docs/data-model.md#where-data-is-kept)).
- **Messages leave only when you send them.** A reminder, a receipt, an SMS, a call or a UPI payment is a link
  you tap. It opens WhatsApp, the phone's message app or a UPI app with the customer's name, the shop name, the
  balance and the pay link filled in, and you press Send there.
- **The screenshot reader comes from a CDN.** When you choose a payment screenshot, the browser fetches the
  reader from `cdn.jsdelivr.net`. The picture itself is read in the browser.
- **Voice uses the browser's speech service.** When you press the microphone, the browser handles the audio,
  and in most browsers that means it goes to the browser maker's service.
- **Clearing the browser's data removes the ledger.** So does removing the site's data on that device. Download
  the **Backup file (JSON)** first; it holds everything except the screenshots.
- **The pay page keeps nothing.** The shop, the amount and the UPI ID travel in the part of the link after `#`,
  which a browser does not send to a server.
