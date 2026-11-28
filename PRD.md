# Hisaab: product requirements

This page describes version 1.5.2, the version on the live link.

Hisaab is a shop credit ledger, the khata a small shop keeps for who owes what. This page says what it must do
and for whom. How it is built is in [the architecture](ARCHITECTURE.md) and [the tech stack](TECH-STACK.md).

## Who it is for

- **The shop owner.** Runs a small shop in India, sells to regular customers on credit and writes it in a
  notebook. Works on a phone, often with a customer waiting, sometimes without signal. May prefer Hindi, Telugu
  or another Indian language to English.
- **The customer.** Receives a polite message with the exact amount owed and a way to pay it.

There is one kind of user of the app itself: the owner. Nobody signs in.

## The problem

A paper khata does not add itself up. The owner cannot see at a glance how much the shop is owed, who has not
paid for longest, or what one customer's balance is today. Asking for money means finding the page, adding the
figures and writing a message by hand, so it is put off. A notebook can also be lost, and it cannot be searched.

Hisaab keeps the same notebook on the owner's phone. It shows the total and every balance, records a sale on
credit or a payment in a few seconds, and opens a WhatsApp reminder with the name, the amount and a pay link
already written.

## Requirements

Each requirement is one line in the owner's words. The last column names the epic that holds its features and
acceptance criteria in [the epics](docs/epics.md).

### From the brief

The product was built against a written brief. Its nine numbered requirements keep the brief's own numbers. The
brief's bonus items and its one checking rule have no numbers there, so they are marked "Bonus" and "Check".

| ID | Requirement | Epic |
| --- | --- | --- |
| 1 | I can add a customer with a name and a 10 digit phone number | Customers |
| 2 | I can add an entry for a customer, credit (I gave goods) or payment (the customer paid), with an amount, a date and a short note | Entries |
| 3 | I see each customer's balance, all credit minus all payments, and the shop's total outstanding at the top | Shop overview |
| 4 | A customer's page lists that customer's entries, newest first | Customer page, statement and tools |
| 5 | I can attach a screenshot to a payment, and it shows as a small picture on the entry | Payment screenshot |
| 6 | Each customer has a WhatsApp reminder button, and the message holds the customer's name and the balance in rupees | Reminders and getting paid |
| 7 | My data is still there after I reload the page | Shop data: load, export and the sample shop |
| 8 | I can paste a shop file in the sample's format into an import box and load a different shop | Shop data: load, export and the sample shop |
| 9 | It works well on a phone 375 px wide | Layout and themes |
| Bonus | I see an overdue list, sorted by days since the last payment | Shop overview |
| Bonus | I can edit and delete an entry, and the balances follow | Entries |
| Bonus | I can export a CSV that matches the data | Shop data: load, export and the sample shop |
| Bonus | I can search by name or phone | Customers |
| Bonus | The reminder carries a UPI pay link | Reminders and getting paid |
| Bonus | I can read the labels in Hindi or Telugu | Languages and names |
| Bonus | The amount is read from the payment screenshot | Payment screenshot |
| Check | Anything that depends on the date uses the date given in the page address when there is one | Links and robustness |

### Beyond the brief: the ledger and getting paid

| Requirement | Epic |
| --- | --- |
| I am warned when a phone number does not look like an Indian mobile number, and no two customers share a number | Customers |
| I can correct a customer's name and its Hindi and Telugu spellings | Customers |
| I can undo a delete, an edit or a load straight away, with no pop-up to answer | Entries |
| After saving an entry I can send the customer a receipt on WhatsApp | Entries |
| I see who owes the most, what was collected and given on credit in the last 30 days, and who has gone longest without paying | Shop overview |
| I see how much has been unpaid for 30 days or more, and from how many customers | Shop overview |
| I can remind every overdue customer one after another with one button | Reminders and getting paid |
| The customer gets a pay link they can tap, and I can show a UPI QR code to scan | Reminders and getting paid |
| If a number is not on WhatsApp I can send the same message by SMS, to a contact I choose, or copy it | Reminders and getting paid |
| When I add a customer I can check that the number is on WhatsApp, so reminders reach them | Reminders and getting paid |
| I am told plainly while reminders point at the demo UPI ID and not my shop's own | Reminders and getting paid |
| I can print or save a customer's statement as a PDF, with a QR to pay | Customer page, statement and tools |
| On a customer's page I see the totals, the days since the last payment and the balance over time, and I can call, open a chat, change the number or delete the customer with undo | Customer page, statement and tools |
| I can type an entry as one sentence, such as "Sunita 500 udhaar", and the form is filled for me to check | Quick entry, typed and by voice |
| I can speak the same sentence, and when voice does not work I am told why and what to do instead | Quick entry, typed and by voice |
| A name the app does not know is offered as a new customer | Quick entry, typed and by voice |

### Beyond the brief: the shop, languages and the device

| Requirement | Epic |
| --- | --- |
| I can load my ledger from a spreadsheet saved as CSV, and I am told what was skipped and why | Shop data: load, export and the sample shop |
| I can export entries and balances as CSV and keep a backup file of the shop | Shop data: load, export and the sample shop |
| On a first visit I can load a sample shop to look around | Shop data: load, export and the sample shop |
| I can keep more than one shop on one device, each with its own ledger | Several shops and the shop profile |
| I can set my shop's name, UPI ID, owner name, address and logo, and they show on statements | Several shops and the shop profile |
| I can use the app in twelve languages: English, Hindi, Telugu, Bengali, Marathi, Tamil, Gujarati, Kannada, Malayalam, Punjabi, Odia and Urdu | Languages and names |
| A customer's name is also shown in the script of my language, and Urdu reads right to left | Languages and names |
| I can show numbers in Hindi or Telugu digits | Languages and names |
| On a phone the ledger looks like a chat app: the customers on the first screen, a quick entry pill at the bottom and a "+" menu of all actions | Layout and themes |
| On a desktop the same pill and menu are there, with Overdue and Recent entries in a panel on the right on a wide screen | Layout and themes |
| I can switch between light and dark, and every screen is readable in both | Layout and themes |
| The app opens without signal after one visit, and I can install it on my phone | Offline and install |
| Amounts are in whole rupees, and an amount with paise is not saved until I choose | Checks on what is typed |
| I am told at once when an amount, a date, a phone number or a UPI ID is wrong, and nothing is saved until it is right | Checks on what is typed |
| I am told when a change could not be kept because the storage of the device is full or blocked | Links and robustness |
| A wrong or incomplete link, to a customer or to the pay page, does not leave me on a broken screen | Links and robustness |

## Out of scope

| Not in the product | Why |
| --- | --- |
| Sending reminders by itself | The owner presses Send in WhatsApp or SMS. Sending without the owner needs a messaging account and the customer's consent |
| Accounts, sign-in and staff logins | One owner, one device. There is no server to sign in to |
| The same ledger on two devices at once | Everything a shop records stays in the browser of one device ([where data is kept](docs/data-model.md#where-data-is-kept)). The backup file moves a shop to another device by hand |
| Taking the payment | The app builds a pay link and a QR code. The money moves in the customer's own UPI app, and the owner records the payment |
| Stock, billing and invoices | The brief asks for a credit ledger only |
| Paise | Amounts are whole rupees |
| Looking up whether a number is on WhatsApp | No such lookup exists without a business account and a server. The owner answers after a hello message |

What the product does within its scope but only up to a point is listed under
[known limits](REPORT.md#known-limits).

## Success measures

| Measure | Target |
| --- | --- |
| The sample shop, with the date set to 15 November 2026 in the page address | Total outstanding 11,820 and every balance as the brief's expected file gives it |
| A different shop loaded through the import box | Every total and balance correct |
| Every requirement of the brief and every bonus item | Met, each shown by a test |
| Sideways scroll at 375 px | None, in every language |
| Light and dark | Every screen readable in both |
| A plain visit | No request to any host but the site itself |
| A visit without signal, after one visit with it | The app opens with its data |

The results on the live link are in [the report](REPORT.md#latest-results-on-the-live-link). Each line of the
brief is checked in [the specification check](SPEC-CHECK.md).

## Assumptions

- "Total outstanding" is the sum of every customer's balance, so money paid in advance lowers it. When some
  customers are in advance, a note under the total says how much is owed and how much is in advance. In the
  sample shop every balance is owed, so both readings give 11,820.
- A customer is identified by the 10 digit phone number. Numbers are Indian mobile numbers, and reminders are
  sent to +91.
- The owner has WhatsApp on the device, or can send an SMS.
- The owner sets the shop's own UPI ID before sending real reminders. Until then a demo UPI ID is used and the
  app says so.
- Voice entry and reading an amount from a screenshot need the internet. Everything else works without it.
- The browser keeps what the app stores. An owner who clears the browser's data, or uses a private window, loses
  the ledger unless a backup file was exported.
