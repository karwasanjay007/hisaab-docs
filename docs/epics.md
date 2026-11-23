# Hisaab: epics, features and acceptance criteria

| | |
| --- | --- |
| Product | Hisaab, a shop credit ledger (vanilla HTML, CSS and JavaScript; data in the browser) |
| Live | https://hisaab-hazel.vercel.app |
| Source of truth | `app.js`, `index.html`, `sw.js`, `styles.css` and the add-on files as built on 23 Nov 2026, version 1.2.3.0 |
| Traces to | `PRD.md` (F-1 to F-5, D-1 to D-4, S-1 to S-4, stage 1 and 2), `brief/spec-page.md` (the projects page word for word, with its "Done when" lines), `brief/brief.md` (an earlier, shorter capture) |
| Last revised | 23 Nov 2026 (version 1.2.3.0) |

This document lists what the app does, grouped as epics, then features, then acceptance criteria. Every
criterion describes behaviour found in the code. Where the PRD asked for something that was built
differently, or not built, the text says so.

## How to read the "Verified by" column

| Short name | What it is |
| --- | --- |
| `check.mjs [id]` | `tests/check.mjs`, the independent checker; `[id]` is the check name it prints (last run: `tests/last-run.txt`, 27 PASS) |
| `nav.mjs` | `tests/nav.mjs`, every nav item at 1440 px and 375 px |
| `quick.mjs "..."` | `tests/quick.mjs`, the quick-entry test; the quoted text is the PASS line it prints |
| `final.mjs "..."` | `tests/final.mjs`, the final-review test (QR, remind all, undo import, validation, search, CSV export, widths) |
| `gaps.mjs "..."` | `tests/gaps.mjs`, the test of the mobile check, UPI check, import checks, CSV import, screenshot reading in Hindi and Undo after an edit |
| `offline.mjs "..."` | `tests/offline.mjs`, one online visit, then the same page offline |
| `brief.mjs "..."` | `tests/brief.mjs`, every "Done when" line of `brief/spec-page.md` followed literally in a fresh browser (last live run: `tests/last-brief-live.txt`, ALL PASS) |
| manual | No script covers it; checked by hand or by reading the code |
| `profile.mjs "..."` | `tests/profile.mjs`, the shop profile and the balance history line, added in 1.1.0.0 (last live run: `evidence/last-profile-live.txt`, 36 checks) |
| `shops.mjs "..."` | `tests/shops.mjs`, several shops on one phone, added in 1.1.0.0 (last live run: `evidence/last-shops-live.txt`, 25 checks) |
| `addons-width.mjs "..."` | `tests/addons-width.mjs`, the add-ons at 320 px and 375 px in three languages, light and dark (last live run: `evidence/last-addons-width-live.txt`, 54 checks) |
| `langs.mjs "..."` | `tests/langs.mjs`, twelve languages, note shortcuts and the overdue total, added in 1.2.0.0 (last live run: `evidence/last-langs-live.txt`, 25 checks) |
| `i18n-check.cjs` | `tests/i18n-check.cjs`, a script that reads the language files without a browser (last run: `evidence/last-i18n-check.txt`, 11 languages, 216 keys each) |
| `customer.mjs "..."` | `tests/customer.mjs`, the customer tools, added in 1.2.0.0 (last live run: `evidence/last-customer-live.txt`, 36 checks) |
| `layout.mjs "..."` | `tests/layout.mjs`, the layout in 12 languages and 5 widths, overview and customer page (120 screens), added in 1.2.1.0 (last live run: `evidence/last-layout-live.txt`, 7 checks) |
| `names.mjs "..."` | `tests/names.mjs`, customer names in the script of each language, added in 1.2.1.0 (last live run: `evidence/last-names-live.txt`, 33 checks) |
| `pay.mjs "..."` | `tests/pay.mjs`, the pay link in the reminder and the pay page, added in 1.2.1.0 (last live run: `evidence/last-pay-live.txt`, 15 checks) |
| `sendways.mjs "..."` | `tests/sendways.mjs`, the other ways to send a reminder, added in 1.2.2.0 (last live run: `evidence/last-sendways-live.txt`, 16 checks) |
| `wacheck.mjs "..."` | `tests/wacheck.mjs`, the WhatsApp check of a number, added in 1.2.3.0 (last live run: `evidence/last-wacheck-live.txt`, 19 checks) |

`quick.mjs`, `final.mjs`, `gaps.mjs`, `offline.mjs` and `brief.mjs` each take the base URL as their first
argument and print ALL PASS when every check passes. Since the app now starts empty, `nav.mjs`,
`quick.mjs`, `final.mjs`, `gaps.mjs` and `offline.mjs` get the sample shop through localStorage from
`tests/seed.mjs`; `brief.mjs` starts from the empty app and loads the sample itself. A "..." at the end of a quoted line stands for the figures the line
prints after it.

## Epics at a glance

| Epic | Title | Features | Acceptance criteria | PRD trace |
| --- | --- | --- | --- | --- |
| E1 | Customers and entries | 7 | 41 | F-1, F-2, F-3, F-5, D-4, S-3, stage 1, stage 2 (screenshot, edit and delete) |
| E2 | Shop overview | 5 | 21 | F-3, F-4, D-3, S-1, stage 1 (balances, total), stage 2 (overdue, search), V-14 |
| E3 | Reminders and getting paid | 5 | 17 | S-2, Q-1, stage 1 (reminder), stage 2 (UPI link), V-15, V-16 |
| E4 | Customer page and statement | 5 | 16 | F-1, stage 1 (customer page newest first), V-16, V-17, V-18 |
| E5 | Quick entry, typed and by voice | 4 | 19 | V-11 (PRD, last section) |
| E6 | Shop data | 6 | 33 | D-1, D-2, F-5, S-4, stage 1 (import), stage 2 (export, amount from screenshot is in E1); CSV import is V-12, backup and balances files V-13 |
| E7 | Languages, themes and layout | 5 | 19 | stage 1 (375 px, light and dark), stage 2 (Hindi and Telugu), V-18, success measures |
| E8 | Navigation, offline and the grader contract | 5 | 25 | F-4, F-5, D-1, brief "How they check", V-19 |
| E9 | Shop profile and balance history (1.1.0.0) | 2 | 13 | V-1, V-2, C-1, C-2 (PRD, last section) |
| E10 | Several shops (1.1.0.0) | 3 | 11 | V-3, C-1, C-2, C-3 (PRD, last section) |
| E11 | More languages (1.2.0.0) | 2 | 8 | V-4, C-1, C-4 (PRD, last section) |
| E12 | Customer tools (1.2.0.0) | 4 | 19 | V-5, V-6, V-7, V-8, C-1 (PRD, last section) |
| E13 | Note shortcuts and overdue total (1.2.0.0) | 2 | 5 | V-9, V-10, C-1 (PRD, last section) |
| E14 | Reaching the customer and a layout that holds (1.2.1.0 to 1.2.3.0) | 5 | 40 | V-20 to V-24, C-1, C-4 (PRD, last section) |
| **Total** | | **60** | **287** | |

---

## E1 Customers and entries

Goal: the owner keeps the notebook in the app. A customer is a name and a 10 digit phone number. Each
entry is a credit (goods given) or a payment (money received), with an amount, a date and a note, and a
payment can carry a screenshot. A balance is always all credit minus all payments, computed from the stored
entries. Mistakes can be edited or deleted, and an edit or a delete can be undone straight away without a
pop-up.

### E1.F1 Add a customer

As the shop owner, I want to add a customer with a name and a phone number so that I can start their account.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E1.F1.1 | The Add a customer form | The owner enters a name and a 10 digit phone number (for example Ravi Kumar, 9000000101) and presses Add customer | The customer is stored, the form clears, "Customer added." shows, the list updates and the entry form selects the new customer | `brief.mjs "Ravi Kumar 9000000101 on the home list: ..."` |
| AC-E1.F1.2 | The name field is empty | The owner presses Add customer | "Enter a name." shows, focus goes to the name field, nothing is saved | manual |
| AC-E1.F1.3 | The phone is not 10 digits after clean-up (for example 9 digits) | The owner presses Add customer | "Enter a 10 digit phone number, digits only." shows, focus goes to the phone field, nothing is saved | `brief.mjs "9 digit phone refused with a message: ..."` |
| AC-E1.F1.4 | The phone is typed as "+91 98765 43210", "919876543210" or "09876543210" | The owner presses Add customer | Spaces and symbols are dropped, a leading 91 (12 digits) or 0 (11 digits) is removed, and 9876543210 is stored | `gaps.mjs "+91 mobile accepted"` |
| AC-E1.F1.5 | A customer with that phone already exists | The owner presses Add customer | "A customer with this phone already exists." shows and nothing is saved (F-2) | manual |
| AC-E1.F1.6 | The phone has 10 digits after clean-up but does not start with 6, 7, 8 or 9 (for example 1234567890) | The owner presses Add customer | The customer is added, and "Customer added." is followed by "Check the number: Indian mobile numbers start with 6, 7, 8 or 9, so WhatsApp may not reach this one." | `gaps.mjs "non-mobile added with a warning: ..."` |
| AC-E1.F1.7 | The phone contains anything other than digits, spaces and + ( ) - . (for example "90000abc01", "9000000101a" or "abcdefghij") | The owner presses Add customer | "Enter a 10 digit phone number, digits only." shows, focus goes to the phone field, nothing is saved | `brief.mjs 'phone "90000abc01" refused with a message: ...'`, `brief.mjs 'phone "9000000101a" refused with a message: ...'`, `brief.mjs 'phone "abcdefghij" refused with a message: ...'` |

Note: any 10 digit number is accepted, as the brief says (`validPhone`); a number that is not an Indian
mobile (`validMobile`) only brings the warning. Import accepts any 10 digits too (E6.F2), with no warning.
Since 1.2.0.0 a customer can be deleted (E12.F4) and given a new phone number (E12.F3).

### E1.F2 Record a credit or a payment

As the shop owner, I want to record a credit or a payment in a few seconds so that the notebook stays current.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E1.F2.1 | The Record credit or payment form on the home view | The owner picks a customer, a type, an amount, a date and a note and presses Save entry | The entry is stored, the form resets (type back to Credit, date back to today), the same customer stays selected and a "Saved." toast shows | `check.mjs [5-shot balance]` |
| AC-E1.F2.2 | The form opens | Nothing is typed yet | The type is Credit, the date is today (or the `?today=` date) and the date field cannot go past today | manual |
| AC-E1.F2.3 | No customer is chosen | The owner presses Save entry | "Choose a customer." shows and focus goes to the customer list | manual |
| AC-E1.F2.4 | The amount is empty, zero, negative or not a number | The owner presses Save entry | "Enter an amount above zero." shows and nothing is saved | manual |
| AC-E1.F2.5 | The amount is above 1,00,00,000 | The owner presses Save entry | "That amount looks too large. One entry can be up to ₹1,00,00,000." shows and nothing is saved | `final.mjs "large amount refused"` |
| AC-E1.F2.6 | The date is after today | The owner presses Save entry | "The date is in the future. Use today or an earlier date." shows and nothing is saved | `final.mjs "future date refused"` |
| AC-E1.F2.7 | The amount is 0.5 | The owner presses Save entry | It is saved; amounts are kept to two decimals and commas in the amount are ignored | `final.mjs "small amount 0.5 saved"` |
| AC-E1.F2.8 | The note is longer than 80 characters | The owner types it | The field stops at 80 characters; the saved note is trimmed of spaces | manual |

### E1.F3 Attach a payment screenshot

As the shop owner, I want to attach the customer's UPI screenshot to a payment so that I have proof on the entry.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E1.F3.1 | The entry form | The owner chooses a picture in `#shot-input` | The type switches to Payment, the picture is shrunk to at most 640 px on its long side as a JPEG, and a preview with a Remove button shows | `check.mjs [5-shot img]` |
| AC-E1.F3.2 | A payment with a screenshot is saved | The list is drawn | The entry shows a 72 px thumbnail `img[data-shot]` | `check.mjs [5-shot img]`, `brief.mjs "payment shows img[data-shot]"` |
| AC-E1.F3.3 | The page reloads | The entry is drawn again | The screenshot is still shown (it is stored inside the entry) | `check.mjs [5-shot reload]`, `brief.mjs "picture still there after reload"` |
| AC-E1.F3.4 | The chosen file is not a picture | The file is chosen | "That file is not a picture." shows and the file is cleared | manual |
| AC-E1.F3.5 | A preview is shown | The owner presses Remove | The picture is dropped from the form; on an edit, saving then removes it from the entry | manual |

### E1.F4 Read the amount from the screenshot

As the shop owner, I want the amount read from the screenshot so that I do not have to type it.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E1.F4.1 | A screenshot is chosen and the amount field is empty | The text reader (Tesseract, loaded from jsDelivr on first use) finishes | The amount field is filled and "Read Rs {amount} from the screenshot. Check it before saving." shows; nothing is saved | `check.mjs [9-bonus OCR]` |
| AC-E1.F4.2 | The picture text has several numbers | The amount is picked | A number next to ₹, Rs, INR, "amount" or "paid", or next to रु, रुपये, राशि, రూ, మొత్తం or భుగతాన, wins (the largest such); otherwise the largest plain number below 10,00,000 that is not a year | manual |
| AC-E1.F4.3 | The owner has already typed an amount | The reader finishes | The typed amount is kept | manual |
| AC-E1.F4.4 | No amount is found, or the reader cannot load (offline) | The reader finishes | "Could not read an amount from this picture. Type it in." shows | manual |
| AC-E1.F4.5 | The app language is Hindi or Telugu | A screenshot is read | The reader reads English plus that script (`eng+hin` or `eng+tel`; `eng` alone in English, and as the fallback if the combined language fails to load), and Devanagari and Telugu digits are turned into Latin digits before the amount is picked; a Hindi screenshot reading "भुगतान रु ४५०" fills 450 | `gaps.mjs "Hindi screenshot amount read"` |

Note: Tesseract.js downloads the language data on first use; it is not cached for offline.

### E1.F5 Edit an entry

As the shop owner, I want to correct a wrong entry so that the balance is right.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E1.F5.1 | An entry in any list | The owner presses Edit | The form fills with the entry, the button reads "Update entry", Cancel edit shows and the form scrolls into view | `check.mjs [9-bonus edit/delete]` |
| AC-E1.F5.2 | The form is in edit mode | The owner changes the amount and presses Update entry | The same entry is changed in place and every balance and total is recomputed | `check.mjs [9-bonus edit/delete]` |
| AC-E1.F5.3 | The entry has a screenshot and no new one is chosen | The owner saves the edit | The existing screenshot is kept | manual |
| AC-E1.F5.4 | The form is in edit mode | The owner presses Cancel edit | The form resets and the selected customer stays | manual |
| AC-E1.F5.5 | The form is in edit mode | The owner saves the edit | The old entry is copied first, and an "Entry updated." toast with Undo and the Send receipt link shows for 7 seconds; a new entry still shows "Saved." with Send receipt and no Undo | `gaps.mjs "edit offers Undo"`, `gaps.mjs "edit applied"` |
| AC-E1.F5.6 | The "Entry updated." toast is showing | The owner presses Undo | The old entry is put back in its place and every balance is recomputed | `gaps.mjs "undo restored the entry"`, `gaps.mjs "balance back to 3,470"` |
| AC-E1.F5.7 | An edit can still be undone | The owner deletes an entry or loads a shop | The edit can no longer be undone; only the newer action's Undo is offered (one level of undo) | manual |

### E1.F6 Delete an entry with Undo

As the shop owner, I want to delete a wrong entry and undo it at once if I tapped by mistake so that nothing is lost (D-4).

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E1.F6.1 | An entry in any list | The owner presses Delete | The entry is removed at once, with no confirm dialog, balances are recomputed and an "Entry deleted." toast with Undo shows for 7 seconds | `check.mjs [9-bonus edit/delete]`, `check.mjs [dialogs]` |
| AC-E1.F6.2 | The toast is showing | The owner presses Undo | The entry returns at its old place and balances are recomputed | manual |
| AC-E1.F6.3 | The deleted entry was open in the edit form | It is deleted | The form resets | manual |
| AC-E1.F6.4 | Two entries are deleted one after the other | The owner presses Undo | Only the last one returns (one level of undo) | manual |

### E1.F7 Balances and saving

As the shop owner, I want every balance worked out from the entries and kept after a reload so that I can trust the figures.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E1.F7.1 | A customer has entries | Any view is drawn | Balance = sum of credits minus sum of payments, rounded to two decimals (F-1, F-3) | `check.mjs [1-sample balances]`, `check.mjs [2-hidden balances]`, `brief.mjs "Ravi balance reads Rs 250: ..."`, `brief.mjs "Ravi shows 670: ..."` |
| AC-E1.F7.2 | A customer has paid more than they owe | The balance is shown | It shows as a negative amount: the text keeps the minus next to the digits, for example ₹-450, and it is drawn as -₹450 (the minus is a separate span ordered first with CSS); it is not counted as owed | `check.mjs [2-hidden balances]` |
| AC-E1.F7.3 | A payment of 500 is recorded | The page reloads | The payment is still there and the balance is 500 lower (S-3, F-5) | `check.mjs [4-persistence]`, `check.mjs [5-shot balance]`, `brief.mjs "after reload Ravi still 250"`, `brief.mjs "Ravi 670 - 100 = 570"` |
| AC-E1.F7.4 | Browser storage is full (large screenshots) | A change is saved | A toast says "Storage is full. Export a backup, then remove some screenshots." (in English only) | manual |
| AC-E1.F7.5 | The app is open in two tabs | One tab saves | The other tab reloads the data and redraws | manual |

---

## E2 Shop overview

Goal: on opening the app the owner sees how much the shop is owed in total and by whom, who has gone longest
without paying, and the latest entries, and can find any customer by name or phone.

### E2.F1 Total outstanding

As the shop owner, I want the total owed to the shop at the top so that I know where I stand at a glance.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E2.F1.1 | The sample shop is loaded | The app opens | `#total-outstanding` shows ₹11,820 and also holds the plain number 11820 (S-1) | `check.mjs [1-sample total]`, `final.mjs "total text"`, `brief.mjs "top shows 11820"`, `brief.mjs "total shows 250: ..."` (Ravi alone) |
| AC-E2.F1.2 | Some customers are in advance | The total is drawn | `#total-outstanding` holds one figure, the sum of all balances (D-3, A-1), with its plain-number copy, for example "₹1,895 1895"; the note "₹{owed} owed, less ₹{advance} paid in advance" is in `#total-note` just below it, outside `#total-outstanding` | `check.mjs [2-hidden total]`, `brief.mjs "total with an advance holds one figure, 1895"`, `brief.mjs "owed note sits outside #total-outstanding"` |
| AC-E2.F1.3 | Any shop | The total is drawn | `#total-outstanding` carries `data-value` (sum of all balances) | manual |

### E2.F2 Key figures

As the shop owner, I want a few key figures beside the total so that I see the state of the shop without counting.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E2.F2.1 | Any shop | The overview is drawn | "customers owe money" shows the count of customers with a balance above zero; with one it reads "1 customer owes money" (`customerOwes`, English, Hindi and Telugu) | `check.mjs [1-sample customers_with_balance]` (INFO) |
| AC-E2.F2.2 | At least one customer owes | The overview is drawn | "Owes the most" shows the name and balance of the largest debtor as a link to their page, and their row carries an "Owes the most" badge | `check.mjs [1-sample top defaulter]` (INFO) |
| AC-E2.F2.3 | Nobody owes | The overview is drawn | The "Owes the most" figure is hidden | manual |
| AC-E2.F2.4 | Entries dated in the 30 days up to today | The overview is drawn | "Collected, last 30 days" sums payments and "Given on credit, last 30 days" sums credits in that window | manual |
| AC-E2.F2.5 | Someone is overdue | The overview is drawn | "Longest without paying" shows the first overdue customer and their days; with nobody overdue it shows "-" | manual |

### E2.F3 Customer list

As the shop owner, I want every customer with their balance in one list, largest debts first, so that I see who to chase.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E2.F3.1 | Any shop | The list is drawn | There is one `[data-customer="<phone>"]` row per customer, with an initial, the name, the phone, the entry count, the balance in `[data-balance]` and a WhatsApp button | `check.mjs [1-sample balances]` |
| AC-E2.F3.2 | Any shop | The list is drawn | Customers who owe come first, largest balance first; the rest follow by balance, then by name | `check.mjs [1-sample top defaulter]` (INFO) |
| AC-E2.F3.3 | No customers, as on a first visit (E6.F5) | The overview is drawn | "No customers yet. Add your first customer, or load the sample shop to look around." shows with two buttons: Add your first customer (`#add-first`) opens `#/s/add` with focus on the name field, and Load sample shop (`#load-sample-home`) does the same as Load sample shop on the Shop file card | `brief.mjs "fresh app holds only Ravi (no other shop preloaded)"` (empty start); the buttons manual |
| AC-E2.F3.4 | A customer is in advance | The list is drawn | Their `[data-balance]` text reads "₹-200" (sign next to the digits, so a number reader keeps it) with `data-balance="-200"`, drawn as "-₹200"; the row shows an "Advance" label; the customer page balance (`#cust-balance`) is drawn the same way | `brief.mjs "advance customer: text ₹-200 for readers, drawn as -₹200, Advance label, data-balance=-200"` |

### E2.F4 Overdue list

As the shop owner, I want customers who owe sorted by days since they last paid so that I chase the oldest first.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E2.F4.1 | Customers with a balance above zero | The overdue list is drawn | Each shows days since their last payment, sorted most days first, ties by larger balance | `check.mjs [9-bonus overdue]` |
| AC-E2.F4.2 | A customer owes but has never paid | The row is drawn | Days count from their first credit, and the row says "No payment yet, first credit"; otherwise it says "Last paid {date}" | `check.mjs [6-today]` |
| AC-E2.F4.3 | The page address has `?today=2026-12-15` | The list is drawn | Days are counted to that date, not to the device date (F-4); days never go below 0 | `check.mjs [6-today]` |
| AC-E2.F4.4 | Nobody owes | The list is drawn | "Nobody owes anything. Well done." shows | manual |

### E2.F5 Search and recent entries

As the shop owner, I want to find a customer by name or phone and see the latest entries so that I can act quickly.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E2.F5.1 | The customer list | The owner types part of a name or of a phone | Only matching rows stay visible and the count reads "{shown} / {total}" | `check.mjs [9-bonus search]` |
| AC-E2.F5.2 | Names have a Hindi or Telugu form | The owner types "रवि" | The customer Ravi is found (own or transliterated local names are searched) | `final.mjs "Hindi search finds Ravi"` |
| AC-E2.F5.3 | The owner pastes "+91 90000 00102" or types "Ravi 9000" | The list filters | A leading 91 is dropped; name words and digits must both match | `final.mjs "+91 phone search"`, `final.mjs "name + digits narrows to one"` |
| AC-E2.F5.4 | Nothing matches | The list filters | 'No customer matches "{query}".' shows | `final.mjs "no-match message"` |
| AC-E2.F5.5 | Any shop | The overview is drawn | Recent entries shows the newest 8 entries across all customers, each with the customer's name as a link | manual |

---

## E3 Reminders and getting paid

Goal: asking a customer to pay takes one tap, with the right amount already in the message and a way to pay
by UPI. The owner can work through everyone overdue in one go. Nothing is sent automatically: WhatsApp
opens and the owner presses Send.

### E3.F1 WhatsApp reminder link

As the shop owner, I want a one-tap WhatsApp reminder with the name and amount so that I do not have to type it.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E3.F1.1 | A customer owes ₹3,470 | The list is drawn | Their row has `a[data-remind="<phone>"]` pointing to `https://wa.me/91<phone>?text=...`, opening in a new tab | `check.mjs [3-reminder]` |
| AC-E3.F1.2 | A customer owes | The message is built | It reads "Namaste {name}, this is a reminder from {shop}. Your balance is Rs 3,470. UPI pay link: {pay page link} Pay by UPI: {link} Thank you." (S-2). The pay page link is there since 1.2.1.0 (E14.F3) | `check.mjs [3-reminder]`, `check.mjs [2-hidden reminders]`, `brief.mjs "reminder: ..."`, `pay.mjs "a pay link on the app's own address comes before it ..."` |
| AC-E3.F1.3 | A customer is in advance | The message is built | It says "You have Rs {amount} in advance with us." and has no pay link | `check.mjs [2-hidden reminders]` |
| AC-E3.F1.4 | A customer's balance is 0 | The message is built | It says "Your account is settled, Rs 0 due." | manual |
| AC-E3.F1.5 | The language is Hindi or Telugu | The message is built | The message text is in that language; the name stays as stored | manual |

### E3.F2 UPI pay link

As the shop owner, I want a UPI pay link with the exact amount so that the customer can pay in one tap.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E3.F2.1 | A customer owes | The link is built | It is `upi://pay?pa={UPI ID}&pn={shop}&am={balance to 2 decimals}&cu=INR&tn=Hisaab {name}` and is inside every reminder | `check.mjs [9-bonus UPI]` |
| AC-E3.F2.2 | A customer owes | Their page is open | A "UPI pay link" button with the same link shows; it is hidden when the balance is 0 or in advance | `final.mjs "UPI link kept"` |

### E3.F3 Shop UPI ID setting

As the shop owner, I want to set my own UPI ID so that payments come to me (PRD Q-1).

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E3.F3.1 | No UPI ID is set | Links are built | The demo ID `hisaab.demo@upi` is used and Settings warns "Demo UPI ID in use. Put your shop's own UPI ID here before sending real reminders." | manual |
| AC-E3.F3.2 | Shop settings | The owner enters a valid UPI ID and presses Save settings | It is stored, "Settings saved." shows and every link uses it | `gaps.mjs "good UPI saved"`, `gaps.mjs "reminder uses the saved UPI ID"` |
| AC-E3.F3.3 | Shop settings | The owner enters a UPI ID that is not in the form name@bank (for example "not a upi") and presses Save settings | "Enter a UPI ID like shopname@okbank, or leave it empty." shows, focus goes to the UPI field and nothing is saved; an empty field is allowed and the demo ID is then used | `gaps.mjs "bad UPI refused"` |

Note: the check (`validUpi`) is 2 to 256 letters, digits, dots, hyphens or underscores, then @, then a
handle of 2 to 64 letters and digits that starts with a letter. A bad UPI ID in an imported file is handled
in E6.F2.

### E3.F4 Remind all overdue

As the shop owner, I want to go through every overdue customer one chat after another so that a round of reminders takes minutes.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E3.F4.1 | Two or more customers are overdue | The overdue card is drawn | A "Remind all overdue ({n})" button shows, linked to the reminder for the longest overdue; with fewer than two it is hidden | `final.mjs "remind-all label"`, `final.mjs "first link is longest overdue"` |
| AC-E3.F4.2 | The button is showing | The owner taps it | That chat opens in a new tab and the button moves to the next customer, reading "Next reminder: {name} ({i} of {n})" | `final.mjs "remind-all opened a chat tab"`, `final.mjs "next label"` |
| AC-E3.F4.3 | The last chat has been opened | The button is drawn | It reads "All {n} reminders opened"; a further tap starts again from the first | manual |
| AC-E3.F4.4 | The overdue list changes (an entry is saved) | The card is redrawn | The queue starts again from the first customer | manual |

### E3.F5 UPI QR code

As the shop owner, I want to show a QR code for the exact amount so that a customer at the counter can scan and pay.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E3.F5.1 | A customer owes and their page is open | The owner taps the QR button | A card opens with a QR code of the UPI link (library loaded from cdnjs on first use), the amount, the UPI ID and "Scan with any UPI app to pay {shop}." | `final.mjs "QR drawn"`, `final.mjs "QR amount"` |
| AC-E3.F5.2 | The QR library cannot load | The card opens | The UPI link is shown as text instead | manual |
| AC-E3.F5.3 | The QR card is open | The owner opens another customer, or the balance reaches 0 | The card closes; the QR button is hidden when nothing is owed | `final.mjs "QR card closed for another customer"` |

---

## E4 Customer page and statement

Goal: one page per customer shows the balance, the whole history newest first with the balance after each
entry, and lets the owner record, remind, correct the name in local script and give the customer a
statement.

### E4.F1 Customer page

As the shop owner, I want a page per customer so that I see their account in one place.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E4.F1.1 | A customer row | The owner taps the name | The page `#/c/<phone>` opens at the top with name, phone as "+91 XXXXX XXXXX", balance and "owes", "Advance" or "Settled" with "Last paid: {date}" or "never" | `check.mjs [7-phone]` (opens the page) |
| AC-E4.F1.2 | The customer page | The entry form shows | It is the same form without the customer list, already set to this customer | `quick.mjs "customer page fills without name"` |
| AC-E4.F1.3 | The address holds a phone that is not a customer | The page loads | The home view shows instead | manual |

### E4.F2 History, newest first, with running balance

As the shop owner, I want the history newest first with the balance after each entry so that I can explain any figure.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E4.F2.1 | A customer has entries | The page is drawn | Entries are listed newest date first; on the same date the later-recorded one comes first | `check.mjs [4-customer page order]` (INFO), `brief.mjs "25 Oct payment 1,500 (row 0) above 23 Oct credit 180 (row 1)"` |
| AC-E4.F2.2 | A customer has entries | The page is drawn | Each entry shows type, note, date, signed amount and "Balance after ₹{amount}", worked from oldest to newest | manual |
| AC-E4.F2.3 | A customer has no entries | The page is drawn | "No entries yet." shows | manual |

### E4.F3 Statement as PDF or print

As the shop owner, I want to print or save a statement so that I can give the customer a clean account.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E4.F3.1 | The customer page | The owner presses Statement (PDF) | The browser print dialog opens with only the statement: shop name, "Account statement", customer and phone, "Generated on {today}" | manual |
| AC-E4.F3.2 | The statement | It is drawn | A table lists entries oldest first with Date, Note, You gave, You got and Balance, and a Totals row | manual |
| AC-E4.F3.3 | The customer owes and the QR library loads within 1.5 seconds | The statement is drawn | A scan-to-pay QR with "Scan to pay ₹{amount}" and the UPI ID is added | `final.mjs "statement has scan-to-pay QR"` |
| AC-E4.F3.4 | Printing ends | The dialog closes | The page and its title return to normal; the print file is named "Statement - {name} - {shop}" | manual |

### E4.F4 WhatsApp receipt after an entry

As the shop owner, I want to send a receipt after each entry so that the customer has a record too.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E4.F4.1 | An entry is saved | The "Saved." toast shows | It carries a "Send receipt" WhatsApp link for 7 seconds | manual |
| AC-E4.F4.2 | A credit was saved | The receipt is built | "Namaste {name}, {shop}: Rs {amount} added to your account on {date}. Balance now Rs {balance}." | manual |
| AC-E4.F4.3 | A payment was saved | The receipt is built | "Namaste {name}, {shop}: payment of Rs {amount} received on {date}, thank you. Balance now Rs {balance}." | manual |

### E4.F5 Customer details in local script

As the shop owner, I want to correct the customer's name and its Hindi and Telugu spelling so that it reads right.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E4.F5.1 | The customer page | Customer details opens | Name, Name in Hindi and Name in Telugu are filled, the local names by automatic transliteration when none is stored, with "Filled in automatically. Correct it if the spelling is wrong." | manual |
| AC-E4.F5.2 | The details form | The owner edits and presses Save details | The names are stored and "Customer details saved." shows | manual |
| AC-E4.F5.3 | The name is cleared | The owner presses Save details | "Enter a name." shows and nothing is saved | manual |

---

## E5 Quick entry, typed and by voice

Goal: the owner types or says one short line, for example "Sunita 500 udhaar", and the entry form fills
itself. The app never saves on its own: focus moves to Save entry and the owner checks and saves. It was added at the final review
and is stated in the PRD as V-11 since 23 Nov 2026.

### E5.F1 Quick-entry parser

As the shop owner, I want to type the entry as I would say it so that recording takes seconds.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E5.F1.1 | The sample shop | The owner enters "Sunita 500 udhaar" and presses Fill | Customer Sunita, Credit and 500 are filled and the message reads "Filled: {name}, credit ₹500. Check it, then press Save entry." | `quick.mjs "Sunita 500 udhaar -> ..."` |
| AC-E5.F1.2 | The line has "1,500", "₹", "Rs99" or "2.5 hazar" | It is parsed | Commas and currency signs are dropped; hazar, thousand and k multiply by 1,000, sau and hundred by 100, lakh by 1,00,000 | `quick.mjs "fatima ₹2.5 hazar jama -> ..."`, `quick.mjs "Rs 99 kavita -> ..."` |
| AC-E5.F1.3 | The line has a payment word (paid, received, jama, mila, wapas and others) | It is parsed | The type is Payment; a payment word wins over a credit word | `quick.mjs "Ravi paid 200 -> ..."` |
| AC-E5.F1.4 | The line has upi, gpay, phonepe or paytm | It is parsed | The type is Payment and the word goes into the note (upi as "UPI"); "cash" goes into the note without setting the type | `quick.mjs "Ravi 200 upi -> ..."` |
| AC-E5.F1.5 | The line has no type word | It is parsed | Credit is used and the message adds "(no word for paid, so taken as credit)" | `quick.mjs "Anjli 120 -> ..."` |
| AC-E5.F1.6 | The line has words that are not a name, amount or type word | It is parsed | They become the note, for example "biscuits" | `quick.mjs "note filled"` |
| AC-E5.F1.7 | The line has a full 10 digit phone or its last four digits | It is parsed | That customer scores highest | `quick.mjs "9000000105 750 paid -> ..."` |
| AC-E5.F1.8 | The name is misspelt by one letter ("Anjli") or shortened to 3 or more letters | It is parsed | The nearest customer is still found | `quick.mjs "Anjli 120 -> ..."` |

### E5.F2 Hindi and Telugu words

As the shop owner, I want to type or say the entry in Hindi or Telugu so that I can use my own words.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E5.F2.1 | The line is "सुनीता 500 उधार" | It is parsed | Sunita, Credit, 500 | `quick.mjs "सुनीता 500 उधार -> ..."` |
| AC-E5.F2.2 | The line is "रवि ने ५०० जमा किए" | It is parsed | Devanagari digits are read, filler words (ने, किए) are ignored: Ravi, Payment, 500 | `quick.mjs "रवि ने ५०० जमा किए -> ..."` |
| AC-E5.F2.3 | The line is "సునీత 300 అప్పు" | It is parsed | Sunita, Credit, 300 (Telugu digits are read too) | `quick.mjs "సునీత 300 అప్పు -> ..."` |

### E5.F3 Unclear lines and the save rule

As the shop owner, I want the app to ask rather than guess so that no wrong entry is saved.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E5.F3.1 | No customer matches ("Zebra 100") on the home view | Fill is pressed | "Which customer? Say the name, for example: Sunita 500 udhaar." shows | `quick.mjs "unknown name asks which customer"` |
| AC-E5.F3.2 | Several customers match equally | Fill is pressed | "More than one customer matches. Pick one:" shows with up to 6 buttons (name and last four digits); a tap fills the form for that customer | manual |
| AC-E5.F3.3 | No amount is given ("Sunita udhaar") | Fill is pressed | The customer is chosen, "Say or type an amount, for example: Sunita 500 udhaar." shows and focus goes to the amount | `quick.mjs "missing amount message"` |
| AC-E5.F3.4 | A line was filled | The form is filled | Nothing is saved; focus moves to Save entry | `quick.mjs "focus on Save"`, `quick.mjs "balance changed"` (only after Save is pressed) |
| AC-E5.F3.5 | The customer page is open | The owner enters "300 paid" | Amount and Payment fill for this customer with no name needed | `quick.mjs "customer page fills without name"` |

### E5.F4 Voice input

As the shop owner, I want to say the entry so that I can record it with my hands full.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E5.F4.1 | The browser has speech recognition (Chrome, Edge) | The page loads | The microphone button shows; where speech recognition is missing it stays hidden | `quick.mjs "mic button shown in Chromium"` |
| AC-E5.F4.2 | The owner taps the microphone | They speak | It listens in en-IN, hi-IN or te-IN by the chosen language, shows the words as heard, and on stop runs the same parser (E5.F1) | manual |
| AC-E5.F4.3 | Nothing is heard, or the microphone is blocked | Listening ends | "Did not catch that. Try again or type it." or "Could not use the microphone. Type the entry instead." shows | manual |

---

## E6 Shop data

Goal: the data stays on the owner's phone (D-1). A shop can be loaded from a shop file (JSON) or a spreadsheet
saved as CSV, which replaces the current shop after it is checked (D-2) and can be undone. The ledger can be exported to spreadsheet files
and a backup file. The app starts with an empty shop; the sample shop is one tap away.

### E6.F1 Load a shop file

As the shop owner, I want to load a shop from a file so that I can move my ledger or start from a backup.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E6.F1.1 | A valid shop file is pasted into `#import-box` | The owner presses `#import-go` | The current shop is replaced, "Loaded {shop}: {c} customers, {e} entries." shows, the view returns to the overview, search clears and every figure is recomputed | `check.mjs [2-hidden total]`, `check.mjs [2-hidden replace]`, `brief.mjs "8 customers after the sample"` |
| AC-E6.F1.2 | A different shop of 15 customers with advances and a customer with no entries | It is loaded | Every balance and the total are correct | `check.mjs [2-hidden balances]`, `check.mjs [2-hidden total]`, `brief.mjs "customer with no entries loads with 0; total 300"` |
| AC-E6.F1.3 | A shop was loaded | The page reloads | The loaded shop is still there | `check.mjs [2-hidden persistence]` |
| AC-E6.F1.4 | The owner uses Choose file with a .json, .csv or .txt file | The file is read | It is put into the box and loaded at once | manual |

### E6.F2 Shop file checks

As the shop owner, I want a bad file refused with a clear reason so that I never lose my ledger to a broken file (S-4).

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E6.F2.1 | The box is empty | Load is pressed | 'The file needs "customers" and "entries" lists.' shows and nothing changes | manual |
| AC-E6.F2.2 | The text starts with { or [ but is not valid JSON | Load is pressed | "This is not valid JSON: {parser message}" shows and nothing changes; text that does not start with { or [ is read as CSV (E6.F6) | manual |
| AC-E6.F2.3 | The JSON has no `customers` or `entries` list | Load is pressed | 'The file needs "customers" and "entries" lists.' shows and nothing changes | manual |
| AC-E6.F2.4 | No row in the file is usable (no valid customer at all) | Load is pressed | The first three problems show, for example 'customer 1: phone "123"', and nothing changes | manual |
| AC-E6.F2.5 | Some rows are bad (a missing phone, an unknown type, a negative amount) but others are good | Load is pressed | The good rows replace the shop and the message adds "Skipped {n}: " (in English, Hindi or Telugu) and up to three problems | `gaps.mjs "import message: ..."` |
| AC-E6.F2.6 | Rows use other spellings | Load is pressed | Phones are cleaned as in AC-E1.F1.4 and any 10 digits are accepted, not only mobile numbers; debit, udhar and sale mean credit; paid, jama and receipt mean payment; Rs, INR, ₹, commas and spaces in amounts are dropped; DD/MM/YYYY dates are read; shop_name, name, upi_id, name_hi and name_te are accepted | manual |
| AC-E6.F2.7 | An entry's phone is not in the customer list | Load is pressed | A customer is created for it, named from the entry or by the phone | manual |
| AC-E6.F2.8 | The same phone appears twice in customers | Load is pressed | The first is kept and the second is dropped without a message | manual |
| AC-E6.F2.9 | Imported text contains HTML | It is shown | It is escaped and shown as text | manual |
| AC-E6.F2.10 | An entry's amount is missing, blank, not a number, negative or above 1,00,00,000 | Load is pressed | That entry is skipped and listed under "Skipped {n}: "; the good rows load | `gaps.mjs "import message: ..."`, `gaps.mjs "balance from good rows only (500 - 100): ..."` |
| AC-E6.F2.11 | An entry's date is missing or cannot be read | Load is pressed | The entry is kept, dated today (or the `?today=` date), and the message adds "{n} entries had no date and are dated today." | `gaps.mjs "import message: ..."` |
| AC-E6.F2.12 | The file's `upi` or `upi_id` is not a valid UPI ID (E3.F3) | Load is pressed | It is listed under "Skipped {n}: " and the current UPI ID is kept | manual |

Built differently from the PRD: S-4 says a file with a missing phone number changes nothing. The code only
refuses the file when it is not JSON or CSV, lacks the lists, or no customer at all can be read. When some
rows are good, the shop is replaced by the good rows and the bad ones are listed as skipped; Undo (E6.F3)
puts the old shop back. Also, on import an amount of 0 and a future date are accepted, which the entry form
refuses.

### E6.F3 Undo a load

As the shop owner, I want to undo loading a file so that a wrong file does not wipe my ledger.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E6.F3.1 | A shop file has just been loaded | The toast shows | "Shop loaded." with Undo shows for 7 seconds, with no confirm dialog | `final.mjs "undo offered after import"`, `check.mjs [dialogs]` |
| AC-E6.F3.2 | The toast is showing | The owner presses Undo | The shop as it was before the load comes back, saved and redrawn | `final.mjs "undo restored the shop"` |

### E6.F4 Export

As the shop owner, I want to export my ledger to a spreadsheet and a backup file so that I have a copy.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E6.F4.1 | Any shop | The owner presses Export entries (CSV) | `hisaab-{shop}-entries.csv` downloads with `date,name,phone,type,amount,note,has_screenshot`, oldest first, sums matching the data | `check.mjs [9-bonus CSV]`, `final.mjs "entries CSV downloads"` |
| AC-E6.F4.2 | Any shop | The owner presses Export balances (CSV) | `hisaab-{shop}-balances.csv` downloads with `name,phone,total_credit,total_payment,balance,last_payment` per customer and a TOTAL OUTSTANDING row | manual |
| AC-E6.F4.3 | Any shop | The owner presses Backup file (JSON) | `hisaab-{shop}.json` downloads with shop, UPI ID, customers and entries in the shop file format, so it can be loaded back | manual |
| AC-E6.F4.4 | A note or name starts with =, +, -, @, a tab or a return | It is exported to CSV | The cell is prefixed with an apostrophe so a spreadsheet does not run it as a formula; numbers are not changed; cells with commas, quotes or line breaks are quoted | manual |

Note: the JSON backup leaves out screenshots, so a backup loaded back has no pictures.

### E6.F5 Sample shop and shop settings

As the shop owner, I want to start my own ledger, with a sample one tap away, and to name my shop so that I can try it and then make it mine.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E6.F5.1 | First visit, nothing stored, or stored data that cannot be read | The app opens | An empty shop ("My shop", no customers, no entries) is created and saved, and the overview shows the empty state with its two buttons (AC-E2.F3.3) | `brief.mjs "fresh app holds only Ravi (no other shop preloaded)"` |
| AC-E6.F5.2 | The Shop file card, or the overview's empty state | The owner presses Load sample shop | `sample-data.json` (Lakshmi Provision Store, 8 customers) is fetched and loaded like any file (with Undo); if the fetch fails the built-in copy is used | `brief.mjs "top shows 11820"` (sample pasted into `#import-box`); the button manual |
| AC-E6.F5.3 | Shop settings | The owner changes the shop name and saves | The header, page title, reminders and statement use the new name; a blank name keeps the old one | manual |
| AC-E6.F5.4 | Any time | The owner reads the footer | "Your ledger stays on this phone. Nothing is sent anywhere." | manual |

### E6.F6 Load a spreadsheet saved as CSV

As the shop owner, I want to load my ledger from a spreadsheet so that I can bring in a hand-made sheet or
another app's export.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E6.F6.1 | A spreadsheet saved as CSV or tab-separated text is pasted into `#import-box`, or chosen as a .csv or .txt file | Load is pressed | Text that does not start with { or [ is read as CSV (`csvRows`, `fromCsv`), turned into a shop file and passed through the same checks as E6.F2; the current shop name is kept and Undo is offered as in E6.F3 | `gaps.mjs "hand-made CSV (900 + 450): ..."` |
| AC-E6.F6.2 | The header row | It is read | Headers are matched case-insensitively on letters only: name (name, customer, customername, naam), phone (phone, mobile, phonenumber, mobilenumber, number, contact), type (type, entrytype, kind; credit when missing), amount (amount, amt, rs, inr, rupees), date (date, day, entrydate; YYYY-MM-DD or DD/MM/YYYY) and note (note, notes, item, items, details, remark) | `gaps.mjs "hand-made CSV (900 + 450): ..."` |
| AC-E6.F6.3 | The sheet has a credit column (credit, udhaar, udhar, gave, given, debit) and a payment column (payment, paid, jama, received, got) | A row is read | It gives a credit entry and a payment entry from those columns, zero or blank cells ignored, as well as one typed entry from an amount column if there is one | `gaps.mjs "hand-made CSV (900 + 450): ..."` |
| AC-E6.F6.4 | A row has only a name and a phone | It is read | The customer is added with no entries | manual |
| AC-E6.F6.5 | The app's own entries export (E6.F4), with quoted cells, doubled quotes and the leading-apostrophe formula guard | It is pasted back and loaded | The cells are read correctly and the shop loads back to the same totals (₹11,820 for the sample) | `gaps.mjs "CSV round trip keeps 11,820: ..."`, `gaps.mjs "CSV customers loaded: ..."` |
| AC-E6.F6.6 | The text has no phone column, or none of amount, credit, payment or name, or fewer than two rows | Load is pressed | "Paste a shop file (JSON) or a spreadsheet saved as CSV with name, phone, type, amount and date columns." shows and nothing changes | `gaps.mjs "unreadable text explained"` |
| AC-E6.F6.7 | The Shop file card in English, Hindi or Telugu | The owner reads the hint | It says a shop file (JSON) or a spreadsheet saved as CSV can be pasted | manual |

---

## E7 Languages, themes and layout

Goal: the app reads in English, Hindi or Telugu, works in light and dark with readable contrast, and fits
every screen from a 375 px phone to a 2560 px monitor with no sideways scroll.

### E7.F1 English, Hindi and Telugu

As the shop owner, I want the app in my language so that I and my staff can read it.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E7.F1.1 | The language list in the top bar | The owner picks हिंदी or తెలుగు | Labels, placeholders, messages and reminder text change to that language and the page `lang` is set | `check.mjs [9-bonus language]` |
| AC-E7.F1.2 | A language was chosen | The page reloads | The choice is kept | manual |
| AC-E7.F1.3 | Hindi or Telugu is chosen | Names are shown | Each name is followed by its local script in brackets, for example "Ravi Kumar (रवि कुमार)", from the stored local name or a dictionary of common name words plus a phonetic fallback | manual |
| AC-E7.F1.4 | Hindi or Telugu is chosen | Dates are shown | Dates use that locale | manual |

Known gaps: a few strings stay in English in every language: the storage-full toast, the shop-file problem
descriptions listed after "Skipped {n}: " in the import message, the screenshot alt text and the statement
footer.

### E7.F2 Native digits

As the shop owner, I want numbers in Hindi or Telugu digits if I prefer them so that the screen reads naturally.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E7.F2.1 | The language is English | Shop settings opens | The native digits option is hidden; money uses ₹ with Indian grouping and Latin digits | manual |
| AC-E7.F2.2 | Hindi or Telugu, option ticked | Figures are drawn | Money, days, dates and the KPI counts use Devanagari or Telugu digits; the choice is stored with the shop | manual |
| AC-E7.F2.3 | Native digits are on | The grader reads the page | `data-balance`, `data-value` and the plain total copy keep Latin digits | manual |

Known gap: the customer count, the search count and the nav badges stay in Latin digits.

### E7.F3 Light and dark themes

As the shop owner, I want light and dark themes so that the app is readable in a bright shop and at night.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E7.F3.1 | No theme chosen | The app opens | It follows the device setting | `check.mjs [8-theme]` (INFO) |
| AC-E7.F3.2 | The theme button | The owner taps it | The theme switches and the choice is kept, applied before the page draws | manual |
| AC-E7.F3.3 | Either theme | Text is drawn on the home view and the customer page | Every text pair meets WCAG AA (4.5:1, or 3:1 for large text), including acted-on states (toasts, messages, edit mode, QR, the primary button hover with `--primary-hover`, the amber link on the green tile with `--accent-on-brand`) | `check.mjs [8-contrast light home]`, `check.mjs [8-contrast dark home]`, `check.mjs [8-contrast light customer]`, `check.mjs [8-contrast dark customer]`, `tests/contrast.mjs` |

### E7.F4 Phone layout

As the shop owner, I want the app to fit my phone so that I can use it with one hand.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E7.F4.1 | A 375 px wide screen | The home view and a customer page are shown | There is no sideways scroll | `check.mjs [7-phone]`, `quick.mjs "no sideways scroll at 375"`, `brief.mjs "375 {hash}: scrollWidth 375; small targets: none"` |
| AC-E7.F4.2 | A 375 px wide screen | The app is shown | A bottom bar carries the main sections and a More button opens a sheet with the rest | `nav.mjs` |
| AC-E7.F4.3 | A long shop name at 375 px | The header is drawn | The name is cut short with an ellipsis and does not overlap the language list | manual |
| AC-E7.F4.4 | Buttons and fields | The owner taps them | Edit, Delete, the language list, the icon buttons and the logo link are at least 44 px tall, and customer-name links in entry lists have a 44 px tall tap area | `brief.mjs "375 {hash}: scrollWidth 375; small targets: none"` (on `#/`, `#/s/record`, `#/s/customers`, `#/s/data` and a customer page; the test flags any visible button, link, field, list or label button under 40 px, except inline text links) |
| AC-E7.F4.5 | Any shop | The app is shown | The Customers item in the menu shows the number of customers and the Overdue item the number of overdue customers, marked as a warning; a count of 0 shows nothing | manual |

### E7.F5 Wide layouts

As the shop owner, I want the app to use a large screen in full so that I see more at once at the counter PC.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E7.F5.1 | 375, 768, 1366, 1920 and 2560 px wide | The overview, the Record view and a customer page are shown | No sideways scroll and the content reaches the right edge | `final.mjs "{width} {hash}: scrollWidth ..."` |
| AC-E7.F5.2 | 1024 px and wider | The app is shown | A left menu replaces the bottom bar and the overview has two columns | `nav.mjs` (1440 px) |
| AC-E7.F5.3 | 1280 px and wider | A section view is open | One related card shows beside it (for example Customers beside Overdue) | manual |
| AC-E7.F5.4 | 1600 px and wider | The overview is open | It shows three columns | manual |

---

## E8 Navigation, offline and the grader contract

Goal: each part of the app has its own address, the Back button works, the app opens without a signal, and
the page carries every hook the brief's automated tester reads, with no pop-up dialogs to stall it.

### E8.F1 Hash routes

As the shop owner, I want each part of the app to open as its own view so that I find things fast and Back works.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E8.F1.1 | Any view | The owner taps Overview, or the address is `#/` or empty | The overview shows every card | `nav.mjs` |
| AC-E8.F1.2 | Any view | The owner taps a nav item | The address becomes `#/s/record`, `customers`, `overdue`, `recent`, `add`, `data` or `settings`, only that card shows, at the top of the page, within 400 ms, and the item is marked current | `nav.mjs` |
| AC-E8.F1.3 | Any view | The address is `#/c/<10 digit phone>` | That customer's page opens and Customers is marked current | `check.mjs [7-phone]` |
| AC-E8.F1.4 | A view was opened | The owner presses Back | The previous view returns | `nav.mjs` (INFO line) |
| AC-E8.F1.5 | Record is tapped | The view opens | Focus goes to the customer list, or to the amount on a customer page | manual |
| AC-E8.F1.6 | The More sheet is open on a phone | The owner presses Escape or taps outside | The sheet closes | manual |

### E8.F2 Date override

As the tester, I want to fix today's date in the address so that date-based figures can be checked.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E8.F2.1 | The address has `?today=YYYY-MM-DD` with a valid date | The app runs | Overdue days, 30-day KPIs, the default entry date, the latest allowed entry date, the statement date and the import fallback date all use it (F-4) | `check.mjs [6-today]` |
| AC-E8.F2.2 | `?today=` is missing or not a valid date | The app runs | The device date is used | manual |

### E8.F3 Grader selectors

As the tester, I want the selectors named in the brief so that the hidden-data test can read the numbers.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E8.F3.1 | Any shop | The page is read | `#total-outstanding` contains the total as ₹ text and as a plain number | `check.mjs [1-sample total]`, `check.mjs [2-hidden total]` |
| AC-E8.F3.2 | Any shop | The page is read | Each customer has `[data-customer="<phone>"]` with `[data-balance]` holding the plain balance as its attribute | `check.mjs [1-sample balances]`, `check.mjs [2-hidden balances]` |
| AC-E8.F3.3 | Any shop | The page is read | Each row has `a[data-remind="<phone>"]` to `https://wa.me/91<phone>?text=` | `check.mjs [3-reminder]` |
| AC-E8.F3.4 | The home view | The tester pastes JSON | `#import-box` and `#import-go` load it (E6.F1) | `check.mjs [2-hidden replace]` |
| AC-E8.F3.5 | The entry form on the home view | The tester attaches a file | `#shot-input` takes it and the saved entry shows `img[data-shot]` | `check.mjs [5-shot img]` |
| AC-E8.F3.6 | A test script | It calls `window.hisaabParseQuick(text)` | It returns amount, type, matching phones and note without touching the form | `quick.mjs` (all parser lines) |

### E8.F4 Offline and install

As the shop owner, I want the app to open without a signal so that I can record a sale anywhere.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E8.F4.1 | The app is served over HTTPS or from localhost | It loads | A service worker (`sw.js`) is registered | manual |
| AC-E8.F4.2 | The network is up | One of the app's own files, qrcode-generator from cdnjs or a Google Fonts file is requested | The network copy is used and a copy is kept in cache `hisaab-v4`, so a new release shows at once | manual |
| AC-E8.F4.3 | The network is down | A page file is requested | The kept copy is used, or `index.html` as a last resort | `offline.mjs "offline reload shows the ledger"` |
| AC-E8.F4.4 | The browser offers install | The manifest is read | `manifest.webmanifest` lets the app be added to the home screen | manual |
| AC-E8.F4.5 | The first visit | The service worker installs and activates | The app shell (`./`, `index.html`, `styles.css`, `app.js`, `sample.js`, `sample-data.json`, `icon.svg`, `manifest.webmanifest`) is cached at once, and older caches are deleted | manual |
| AC-E8.F4.6 | One online visit on which a QR code was shown | The network goes down and the page reloads | The ledger opens with ₹11,820 and the QR code still draws | `offline.mjs "offline reload shows the ledger"`, `offline.mjs "QR draws offline"` |

Note: the QR library is cached the first time it loads, so it needs a signal once. Tesseract.js (jsDelivr)
and its language data are not cached, so the screenshot reader needs a signal.

### E8.F5 No dialogs, no errors, accessible

As the shop owner, I want an app that never stalls on a pop-up and works with a screen reader so that anyone can use it.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E8.F5.1 | A full test run | Every action is taken | No `alert`, `confirm` or `prompt` dialog opens | `check.mjs [dialogs]`, `brief.mjs "no dialogs"` |
| AC-E8.F5.2 | A full test run | Every action is taken | No console or page error is logged | `check.mjs [console]`, `quick.mjs "no console errors"`, `final.mjs "no console errors"`, `gaps.mjs "no console errors"`, `brief.mjs "no page errors"` |
| AC-E8.F5.3 | A keyboard user | They press Tab first | A "Skip to the ledger" link jumps to the main content | manual |
| AC-E8.F5.4 | A screen reader user | The total, quick-entry messages, screenshot messages or a toast change | They are announced (polite live regions); hidden labels name the language list, search, import box and entry type | manual |
| AC-E8.F5.5 | A screen reader user | They use the microphone, QR and More buttons | `aria-pressed` and `aria-expanded` report their state | manual |

---

## E9 Shop profile and balance history

Goal: added in version 1.1.0.0. The owner can put a name, an address and a small logo on the shop, and can see
how one customer's balance moved over time. Both live in an add-on file beside the ledger. Nothing shows until
it is set, and the total, the rows and the import read the same as before.

### E9.F1 Shop profile

As the shop owner, I want my name, address and logo on the app and on the statement so that a customer knows who the statement is from.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E9.F1.1 | No profile has been set | The owner opens Shop settings | The profile form is there with a status message, and the header shows no logo | `profile.mjs "the profile form sits in Shop settings with a status message"`, `profile.mjs "no logo in the header until one is set"` |
| AC-E9.F1.2 | The profile form | The owner types an owner name and an address and saves | "Profile saved." shows, both are still there after a reload, and the shop name in the header is untouched | `profile.mjs "saving says so ..."`, `profile.mjs "owner and address survive a reload"`, `profile.mjs "the shop name in the header is untouched ..."` |
| AC-E9.F1.3 | The profile form | The owner chooses a picture as the logo | The logo shows in the header; a large picture is brought down to 96 px on its longer side and stored under 40 KB | `profile.mjs "a chosen PNG shows as the logo in the header"`, `profile.mjs "a 600 by 400 picture is brought down to 96 px on its longer side ..."`, `profile.mjs "the stored logo is a data URL under 40 KB ..."` |
| AC-E9.F1.4 | A profile and a logo are set | The owner opens a customer's statement | The statement carries the owner and the address under the shop name, and shows the logo | `profile.mjs "the statement carries owner and address under the shop name"`, `profile.mjs "the statement shows the logo, keeps the shop name, and the print call was made once (stubbed)"` |
| AC-E9.F1.5 | A profile and a logo are set | The owner exports the backup file and loads it again | The file carries owner, address and logo, and the load brings them back; a backup made without a profile has no profile fields | `profile.mjs "the backup file carries owner, address and logo"`, `profile.mjs "importing the backup brings the profile back, fields and header included"`, `profile.mjs "a backup without a profile has no profile fields"` |
| AC-E9.F1.6 | A profile and a logo are set | The owner clears the profile | The header logo goes away | `profile.mjs "the profile can be cleared; the header logo goes away"` |
| AC-E9.F1.7 | The sample shop | The page is read with the add-on and without it | The total and the customer rows are the same, and the add-on adds none of the attributes the tester reads | `profile.mjs "total outstanding is the same with and without profile.js ..."`, `profile.mjs "the customer rows are the same with and without profile.js ..."`, `profile.mjs "the add-on adds none of the reserved data attributes"` |
| AC-E9.F1.8 | A phone 320 px or 375 px wide | A logo is set | There is no sideways scroll, and the header is no taller than before | `profile.mjs "the header is no taller with a logo at 320 px ..."`, `addons-width.mjs "320 px en light #/s/settings: no sideways scroll ..."` |
| AC-E9.F1.9 | A logo is set | The owner presses Remove logo | The logo is dropped from the header and the shop, and the owner name and address stay | manual |

Note: the printed statement was checked by its markup, not on paper.

### E9.F2 Balance history line

As the shop owner, I want a line of the balance after each entry so that I see at a glance whether a customer is paying down or building up.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E9.F2.1 | A customer with two or more entries (six in the test) | The customer page opens | The balance history shows in a card just before the entries | `profile.mjs "the balance chart shows for a customer with 6 entries"`, `profile.mjs "the chart card sits in the customer grid just before the entries"` |
| AC-E9.F2.2 | A customer with one entry | The customer page opens | No balance history shows | `profile.mjs "no chart for a customer with one entry"` |
| AC-E9.F2.3 | The balance history is drawn | The page is read | It is one inline SVG, with no canvas or image, and it carries a sentence for screen readers that follows the chosen language | `profile.mjs "the chart is one inline SVG: a line, the last point, labels, a viewBox; no canvas or image"`, `profile.mjs 'the chart is role="img" with a sentence label ...'`, `profile.mjs "the chart label follows the language ..."` |
| AC-E9.F2.4 | A phone 320 px or 375 px wide | A customer page with the balance history opens | The line stays inside the screen and there is no sideways scroll | `profile.mjs "no sideways scroll on a customer page at 320 px, chart inside the screen ..."`, `addons-width.mjs "375 px customer page with the balance chart: no sideways scroll ..."` |

---

## E10 Several shops

Goal: added in version 1.1.0.0. An owner with more than one shop keeps each ledger on the same phone and
moves between them. The current shop stays under the ledger's own key, `hisaab.v1`, exactly as before. The
other shops are parked under `hisaab.shops`, which is not written until a second shop is added. There is still
no login and no server.

### E10.F1 Add a shop and switch

As the shop owner, I want to add a second shop and switch between shops so that each ledger stays separate.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E10.F1.1 | One shop | The app is used | Nothing is stored beyond the ledger's own key: `hisaab.shops` is not written, and the only shop has no Switch or Remove button | `shops.mjs "one shop: nothing is written under hisaab.shops"`, `shops.mjs "the only shop has no Switch or Remove button"` |
| AC-E10.F1.2 | The Shops block in Shop settings | The owner types a name and presses Add shop | The new shop starts empty, is current and carries the name; the earlier shop is parked under `hisaab.shops` | `shops.mjs "a new shop starts empty ..."`, `shops.mjs "the new shop is current and named ..."`, `shops.mjs "hisaab.shops holds the parked shop, and the active one has data: null"` |
| AC-E10.F1.3 | Two shops | The owner presses Switch on the first shop | The first shop comes back exactly, with its total | `shops.mjs "switching back restores the first shop exactly ..."` |
| AC-E10.F1.4 | The second shop is current | A file is loaded through the plain import | It loads into the current shop only; the first shop is untouched | `shops.mjs "a plain import in the second shop loads into that shop ..."`, `shops.mjs "the first shop is untouched by that import ..."` |
| AC-E10.F1.5 | Several shops | The page reloads | Every shop is still there and a parked shop still opens | `shops.mjs "everything survives a reload ..."`, `shops.mjs "the parked shop opens after a reload ..."` |
| AC-E10.F1.6 | The sample shop | The page is read with the add-on and without it | The total and the customer rows are the same, the add-on uses none of the attributes the tester reads, and nothing reaches past the screen at 320 px | `shops.mjs "total and customer rows are the same with and without shops.js ..."`, `shops.mjs "none of the grader's attributes are used by the add-on"`, `shops.mjs "at 320 px nothing of the Shops card or the new import button reaches past the screen ..."` |

### E10.F2 Remove a shop

As the shop owner, I want removing a shop to take two presses so that one slip does not lose a ledger.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E10.F2.1 | A parked shop | The owner presses Remove once | The app asks again and the shop is still listed | `shops.mjs "the first press on Remove only asks again ..."` |
| AC-E10.F2.2 | The app has asked again | The owner presses Remove a second time | The shop is removed from the list | `shops.mjs "the second press removes the shop ..."` |

### E10.F3 Import as a new shop

As the shop owner, I want to load a file as a new shop so that the shop I have open is not replaced.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E10.F3.1 | A valid shop file in the import box | The owner presses "Import as a new shop" | The current shop is parked and a new shop holds the file | `shops.mjs '"Import as a new shop" makes a third shop holding the file ...'` |
| AC-E10.F3.2 | A file that cannot be read in the import box | The owner presses "Import as a new shop" | The app returns to the previous shop and no extra shop is left | `shops.mjs 'a broken file with "Import as a new shop" returns to the previous shop, no extra shop ...'` |
| AC-E10.F3.3 | The file was refused | The owner reads the import card | The import's own error message stays on screen | `shops.mjs "the import's own error stays on screen ..."` |

Note: a full storage during a switch, and a tab closed between the two writes of a switch, were traced in the
code by the reviewer and not run.

---

## E11 More languages

Goal: added in version 1.2.0.0. Nine more languages join English, Hindi and Telugu: Bengali, Marathi, Tamil,
Gujarati, Kannada, Malayalam, Punjabi, Odia and Urdu, twelve in all. The first three stay built into the app.
Each further language is one file, fetched only when it is chosen. The figures do not depend on the language.

### E11.F1 Twelve languages

As the shop owner, I want the app in my own language so that I can read every label.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E11.F1.1 | Any view | The owner opens the language menu | Twelve languages are listed, the first three as before | `langs.mjs "the language menu has twelve languages, the first three as before ..."` |
| AC-E11.F1.2 | The app is in English | The page loads | A visit in English fetches no language file | `langs.mjs "a visit in English fetches no language file"` |
| AC-E11.F1.3 | The sample shop | The owner chooses one of the nine new languages | Its file is fetched once; the labels show in its own script; the total, the rows and the reminder links are the same in every language; there is no sideways scroll at 375 px | `langs.mjs "bn: labels in its own script, the same total, rows and reminder links, no sideways scroll at 375 px ..."` (one line for each of the nine), `langs.mjs "each language file was fetched once, when chosen ..."` |
| AC-E11.F1.4 | A new language was chosen | The page reloads | The chosen language is still there | `langs.mjs "the chosen language is still there after a reload ..."` |
| AC-E11.F1.5 | A language whose file cannot be fetched | The owner chooses it | The app falls back to English | `langs.mjs "a language whose file cannot be fetched falls back to English"` |
| AC-E11.F1.6 | Several languages were used in turn | The owner goes back to Hindi, then to English | Hindi, built in, still works, and English reads exactly as at the start | `langs.mjs "Hindi, built in, still works after the others"`, `langs.mjs "back in English everything reads exactly as at the start"` |
| AC-E11.F1.7 | The language files | The checking script runs | Each language has the same 216 keys, every text is in its own script, and none is left identical to English | `i18n-check.cjs` |

Note: the nine new languages were written by the build's own translation pass and checked by the script. No
native speaker has read them yet. Dates stay in the English short form in the nine new languages, and Urdu is
laid out left to right like the rest of the page.

### E11.F2 Quick-entry words in each language

As the shop owner, I want quick entry to know my language's words for "gave" and "got" so that the example on the screen works as written.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E11.F2.1 | The app is in Bengali, Tamil, Urdu or Kannada (the four the test covers) | The owner types that language's own example, a name, 500 and its word for credit | The form fills with the customer, 500 and credit | `langs.mjs 'bn: its own example "Sunita 500 ..." fills the form: Sunita Devi, 500, credit ...'` (one line each for bn, ta, ur and kn) |

Note: voice entry in the nine new languages uses the browser's English speech service. A name spoken in another
script is not matched to a customer saved in Latin letters.

---

## E12 Customer tools

Goal: added in version 1.2.0.0. A block after the entries on the customer page, "More for this customer",
gives the owner the customer's totals, a way to call or message them, a way to correct a wrong phone number and
a way to delete a customer. It replaces the earlier notes in this document that a customer could not be deleted
and a phone number could not be changed. The block is on the customer page only.

### E12.F1 Totals and days since the last payment

As the shop owner, I want a customer's totals and the days since they last paid so that I know where the account stands.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E12.F1.1 | Any shop | The owner opens the home view, then a customer page | The block is not on the home view; on the customer page it is a labelled card right after the entries | `customer.mjs "nothing of the add-on shows on the home view"`, `customer.mjs "it is a labelled card in the customer grid right after the entries"` |
| AC-E12.F1.2 | The sample shop | A customer page opens | Total given on credit and total received are the sums of that customer's entries | `customer.mjs "total given on credit is the sum from the sample file ..."`, `customer.mjs "total received is the sum from the sample file ..."` |
| AC-E12.F1.3 | A customer who has paid before, and a date in the page address | A customer page opens | The days since the last payment are counted to that date | `customer.mjs "days since the last payment, counted to 2026-11-15 ..."` |

Note: a customer with no payment shows "No payment yet" (not covered by a script).

### E12.F2 Call and WhatsApp chat links

As the shop owner, I want to call or message a customer from their page so that I do not have to look up the number.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E12.F2.1 | A customer page | The owner taps Call | The phone app opens with the customer's number | `customer.mjs "the Call link carries the number ..."` |
| AC-E12.F2.2 | A customer page | The owner taps WhatsApp chat | A chat opens with that number and no message | `customer.mjs "the chat link is wa.me with the number, no message, opens apart ..."` |
| AC-E12.F2.3 | Any shop | The page is read with the add-on | The chat link is not a reminder link: the count of reminder links and the total are the same as before | `customer.mjs "home view: same number of a[data-remind] and the same total as before the script ..."`, `customer.mjs "customer page: as many a[data-remind] as before the script ..."`, `customer.mjs "the block has none of the reserved data attributes"` |

### E12.F3 Change a customer's phone number

As the shop owner, I want to correct a wrong phone number so that reminders reach the right person.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E12.F3.1 | A customer page | The owner presses the change number button | The form was closed until then; it opens with a phone field and a status message | `customer.mjs "the phone form is closed until asked for"`, `customer.mjs "the button opens a tel field with a status message"` |
| AC-E12.F3.2 | The change number form | The owner saves a number that is not 10 digits | "Enter a 10-digit mobile number." shows and nothing changes | `customer.mjs "a short number is refused ..."` |
| AC-E12.F3.3 | The change number form | The owner saves a phone number another customer has | It is refused with "Another customer already has this number." and nothing is moved | `customer.mjs "the number of another customer is refused, nothing moved ..."` |
| AC-E12.F3.4 | The change number form | The owner saves a new 10 digit number | The customer and every entry move to the new number, in the stored shop too; the page address follows; "Phone number changed." shows | `customer.mjs "a good number changes the address ..."`, `customer.mjs "the customer and all 6 entries moved to the new number, in the stored shop too ..."`, `customer.mjs "it says so, and the block follows the new number ..."` |
| AC-E12.F3.5 | A phone number was changed | The owner opens the home view | The total outstanding is the same as before | `customer.mjs "the total on the home view is the same after the phone change ..."` |
| AC-E12.F3.6 | An entry was just deleted or edited, so the ledger's own Undo is on offer | The owner changes the customer's number | The ledger's own Undo is cleared, so an entry cannot come back under the old number | manual |

### E12.F4 Delete a customer with Undo

As the shop owner, I want to delete a customer who should not be in the ledger, and to take it back if I slipped.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E12.F4.1 | A customer page | The owner presses Delete customer once | The app asks for a second press and names the customer; nothing is removed | `customer.mjs "the first press only asks again ..."`, `customer.mjs "the page heading is untouched ..."` |
| AC-E12.F4.2 | The app has asked again | The owner presses a second time | The customer, the entries and the row are removed, the overview opens, and the total drops by that customer's balance | `customer.mjs "the second press removes the customer, the entries and the row, and goes home"`, `customer.mjs "the total drops by that customer's balance ..."` |
| AC-E12.F4.3 | A customer was just deleted | The overview shows | A bar says who was deleted and offers Undo, for eight seconds | `customer.mjs "a status bar says who was deleted and offers Undo ..."` (the eight seconds: manual) |
| AC-E12.F4.4 | The Undo bar is showing | The owner presses Undo | Undo puts the customer and the same entries back, in the stored shop too, and the row and the total return | `customer.mjs "Undo puts the customer and the same 6 entries back, in the stored shop too"`, `customer.mjs "the row and the total are back ..."` |
| AC-E12.F4.5 | A customer page | The owner taps Delete twice in quick succession | The two taps count as one press, so the customer is not deleted | manual |
| AC-E12.F4.6 | A full test run of the block | Every action is taken | No confirm, alert or prompt dialog opens and no page error is logged | `customer.mjs "no confirm, alert or prompt was used"`, `customer.mjs "no page errors"` |
| AC-E12.F4.7 | A phone 320 px wide, light or dark | The block shows with the form open | There is no sideways scroll and every control is at least 44 px tall | `customer.mjs "no sideways scroll on the customer page at 320 px, form open and the long delete label showing ..."`, `customer.mjs "every control in the block is at least 44 px tall ..."`, `customer.mjs "the block also shows in the dark theme at 320 px"` |

Note: after the eight seconds, a reload or a switch to another shop, the deletion stands. After Undo the
entries can come back in a slightly different order; none is lost.

---

## E13 Note shortcuts and overdue total

Goal: added in version 1.2.0.0. Small helps on screens the owner already uses: one tap for the common notes,
one line for the money that has waited longest, and the date the figures are for. Each is drawn beside what
was there; nothing new is stored.

### E13.F1 Note shortcuts

As the shop owner, I want the common notes one tap away so that I type less when recording an entry.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E13.F1.1 | The entry form | The page is read | Six shortcuts sit under the note field: groceries, milk, rice, oil, paid on UPI and cash, in the chosen language | `langs.mjs "six note shortcuts under the note field ..."` |
| AC-E13.F1.2 | The entry form | The owner taps one shortcut, then another | The first fills the note; the second is added after a comma | `langs.mjs "a shortcut fills the note, a second one is added after a comma ..."` |
| AC-E13.F1.3 | The note is close to its longest length | The owner taps a shortcut | The note stays within the field's length, and the entry is saved only by the same button as before | manual |

### E13.F2 Overdue total and the date of the figures

As the shop owner, I want the total unpaid for 30 days or more, and the date the figures are for, so that I know how much to chase and how fresh the numbers are.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E13.F2.1 | A shop with overdue customers | The overdue list shows | A line above it gives the amount unpaid for 30 days or more and the number of customers | `langs.mjs "the overdue line gives the unpaid total and the number of customers ..."` |
| AC-E13.F2.2 | Any shop | The overview shows | The date the figures are for shows under the total | manual |

---

## E14 Reaching the customer and a layout that holds

Goal: added in versions 1.2.1.0 to 1.2.3.0, from the owner's own use of the live site. The layout holds in
every language and width, names show in the script of every language, the reminder carries a pay link the
customer can tap, a reminder can go another way when WhatsApp refuses a number, and a number added by hand
can be checked on WhatsApp once. Each is an add-on beside the ledger. The ids, attributes, totals, reminder
links and the import that the tester reads are unchanged.

### E14.F1 A layout that holds (1.2.1.0)

As the shop owner, I want every screen to read cleanly in my language on my phone so that nothing is broken, hidden or cut off.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E14.F1.1 | The sample shop, each of the 12 languages, at 5 widths | The overview and a customer page are drawn | Nothing scrolls sideways in any of the 120 screens | `layout.mjs "no sideways scroll in 120 screens ..."` |
| AC-E14.F1.2 | The same screens | The text is laid out | No text is broken into a column of letters, and the customer name stays within three lines | `layout.mjs "no text is broken into a column of letters, and the customer name stays within three lines"` |
| AC-E14.F1.3 | A customer page | The heading is laid out | The customer heading never runs under the action buttons | `layout.mjs "the customer heading never runs under the action buttons"` |
| AC-E14.F1.4 | Any button or menu, in any language | It is drawn | Its label wraps instead of being cut off | `layout.mjs "no button or menu cuts its label off"` |
| AC-E14.F1.5 | A customer page at 1366 and 1920 px | The cards are laid out in columns | There is no blank band between cards in a column | `layout.mjs "the customer page has no blank band between cards in a column ..."` |
| AC-E14.F1.6 | The entry and add forms | They are drawn | No form button touches the text above it | `layout.mjs "no form button touches the text above it"` |
| AC-E14.F1.7 | A narrow card | The customer's entries are drawn | Each entry stacks its date, note and amount inside the card | manual |

### E14.F2 Names in the script of every language (1.2.1.0)

As the shop owner, I want customer names in the script of my language so that the list reads the same way as the labels.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E14.F2.1 | The sample shop in English | The list is drawn | No name has a bracket | `names.mjs "English shows no bracket ..."` |
| AC-E14.F2.2 | Hindi or Telugu is chosen | The list is drawn | Names read as before, for example "Sunita Devi (सुनीता देवी)" | `names.mjs "Hindi: Sunita Devi reads as before ..."`, `names.mjs "Telugu: a Telugu-script bracket as before ..."` |
| AC-E14.F2.3 | One of the nine newer languages is chosen | The list is drawn | Every name shows a bracket in that language's script, with no Latin letter and no unassigned character | `names.mjs "bn: every name shows a bracket in its own script, no Latin, no unassigned character ..."` and the same line for mr, ta, gu, kn, ml, pa, or, ur |
| AC-E14.F2.4 | One of the nine newer languages is chosen | A customer page opens | Its heading shows the name in that script | `names.mjs "bn: the customer page heading shows the name in its script ..."` and the same line for the other eight |
| AC-E14.F2.5 | Any of the twelve languages | The figures are read | The total, the rows and the reminder links are the same as in English (₹11,820 for the sample) | `names.mjs "bn: the same total, rows and reminder links ..."` and the same line for each language |

Note: the spelling is made automatically from the stored name. The owner can correct the Hindi and Telugu
spelling (E4.F5); there is no field for the nine newer scripts.

### E14.F3 A pay link the customer can tap (1.2.1.0)

As the customer, I want a link I can tap in the WhatsApp reminder so that I can pay without copying anything.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E14.F3.1 | A customer owes | The reminder is built | It is still a wa.me link to the customer and still names the customer and the balance in rupees | `pay.mjs "the reminder is still a wa.me link to the customer"`, `pay.mjs "it still names the customer and the balance in rupees"` |
| AC-E14.F3.2 | A customer owes | The reminder is built | The `upi://pay` link with the UPI ID and the balance is still in the message | `pay.mjs "the upi://pay link with the UPI ID and the balance is still in the message ..."` |
| AC-E14.F3.3 | A customer owes | The reminder is built | A pay link on the app's own address stands first, before the UPI link | `pay.mjs "a pay link on the app's own address comes before it ..."`, `pay.mjs "the tappable link stands first"` |
| AC-E14.F3.4 | A shop name with brackets | The pay link is built | It has no bracket or other character that would end the link early, and ends in a digit | `pay.mjs "the pay link has no bracket or other character that would end the link early, and ends in a digit"` |
| AC-E14.F3.5 | The pay link | It is opened | The details ride after "#", and no request carries the shop, the UPI ID or the amount | `pay.mjs "the details ride after "#", so they are never sent to a server"`, `pay.mjs "no request carries the shop, the UPI ID or the amount"` |
| AC-E14.F3.6 | The sample shop | Every reminder is built | All 8 reminders carry both links | `pay.mjs "all 8 reminders carry both links ..."` |
| AC-E14.F3.7 | The pay page | It opens | It shows the shop, the amount and the UPI ID, and its button opens the same `upi://` link as the message | `pay.mjs "the pay page shows the shop, the amount and the UPI ID ..."`, `pay.mjs "its button opens the same upi:// link as the message"` |
| AC-E14.F3.8 | The pay page on a phone | It is drawn at 375 px | It fits with no sideways scroll | `pay.mjs "the pay page fits a 375 px phone ..."` |
| AC-E14.F3.9 | A broken link, or a shop name with markup | The pay page opens | It shows "not complete" and runs nothing from the link; markup is shown as text | `pay.mjs "a broken link shows "not complete" and nothing from the link is run"`, `pay.mjs "a shop name with markup is shown as text"` |

Note: the pay page is in English only. A customer in advance or settled gets no pay link, as before.

### E14.F4 Other ways to send a reminder (1.2.2.0)

As the shop owner, I want another way to send the reminder when WhatsApp refuses a number so that the customer still gets it.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E14.F4.1 | A customer page | It is drawn | "Other ways to send" shows under the reminder buttons | `sendways.mjs "the customer page offers "Other ways to send" under the reminder buttons"` |
| AC-E14.F4.2 | A customer with a sample number | The page opens | The block opens by itself and says the number is made up | `sendways.mjs "for a sample number it opens by itself and says the number is made up"` |
| AC-E14.F4.3 | The block is open | The owner picks SMS | The SMS goes to the customer's number with the same message as the WhatsApp reminder | `sendways.mjs "the SMS link goes to the customer's number with the same message as the WhatsApp reminder"` |
| AC-E14.F4.4 | The block is open | The owner picks WhatsApp without a number | The same message opens, to send to any contact chosen in WhatsApp | `sendways.mjs "the WhatsApp link without a number carries the same message, to send to any contact"` |
| AC-E14.F4.5 | The block is open | The owner presses Copy | "Message copied" shows and the clipboard holds the message | `sendways.mjs "the copy button holds the same message"`, `sendways.mjs "Copy says "Message copied" and the clipboard holds the message ..."` |
| AC-E14.F4.6 | A customer with a real number | The page opens | There is no sample note and the SMS goes to that number | `sendways.mjs "for a real number there is no sample note and the SMS goes to that number"` |
| AC-E14.F4.7 | The block is open | The links are counted | None of the new links counts as a reminder link; the overview has one reminder link per customer as before | `sendways.mjs "none of the new links counts as a reminder link"`, `sendways.mjs "the overview has one reminder link per customer as before ..."` |
| AC-E14.F4.8 | Hindi, Telugu, Tamil, Bengali or Urdu, at 375 px | The block is open | It is in that language's script and nothing scrolls sideways | `sendways.mjs "no sideways scroll at 375 px with the block open ..."`, `sendways.mjs "hi: the block is in its own script and fits 375 px ..."` and the same line for te, ta, bn, ur |

### E14.F5 Check a number on WhatsApp (1.2.3.0)

As the shop owner, I want to find out once, when I add a customer, whether the number is on WhatsApp so that a refusal does not surprise me at every reminder.

| ID | Given | When | Then | Verified by |
| --- | --- | --- | --- | --- |
| AC-E14.F5.1 | A shop file is loaded | Eight customers arrive at once | No check is offered; they are not "just added" | `wacheck.mjs "loading a shop file asks for no check: eight customers at once are not "just added""` |
| AC-E14.F5.2 | The owner adds a customer by hand | The customer is saved | The add form offers "Check on WhatsApp" | `wacheck.mjs "after adding a customer by hand, the add form offers "Check on WhatsApp" ..."` |
| AC-E14.F5.3 | The offer shows | The owner presses it | A hello message to that number opens, with the name and the shop and no balance | `wacheck.mjs "it opens a hello message to that number, with the name and the shop and no balance ..."` |
| AC-E14.F5.4 | The hello message was opened | The app moves to that customer | It asks whether the chat opened; until answered the number reads as not checked | `wacheck.mjs "the app moves to that customer and asks whether the chat opened"`, `wacheck.mjs "until answered, the number reads as not checked"` |
| AC-E14.F5.5 | The question shows | The owner answers "Yes, the chat opened" | The number is marked as on WhatsApp | `wacheck.mjs ""Yes, the chat opened" marks the number as on WhatsApp"` |
| AC-E14.F5.6 | The question shows | The owner answers "No" | The number reads "Not on WhatsApp" and the other ways to send open (E14.F4) | `wacheck.mjs ""No" marks it as not on WhatsApp and opens the other ways to send"` |
| AC-E14.F5.7 | An answer was given | The storage and the page are read after a reload | The answer is kept in its own key for that number; the customer's ledger entry has no new field; the answer is still there | `wacheck.mjs "the answer is kept in its own key; the ledger entry of the customer has no new field ..."`, `wacheck.mjs "the answer is still there after a reload"` |
| AC-E14.F5.8 | Any customer page | The owner wants to check the number | The check can be started from there | `wacheck.mjs "the check can be started from any customer page"` |
| AC-E14.F5.9 | The check is shown or answered | The links are counted at 375 px | No new reminder link; one reminder link per customer as before; the offer is gone once answered; nothing scrolls sideways | `wacheck.mjs "no sideways scroll at 375 px with the offer shown ..."`, `wacheck.mjs "no new reminder link, and no sideways scroll at 375 px"`, `wacheck.mjs "the overview has one reminder link per customer as before, and the offer is gone once answered"` |
| AC-E14.F5.10 | Hindi or Tamil | The check is shown | The status, the button and the hello message are in that language's script | `wacheck.mjs "hi: status, button and hello message in its own script ..."`, `wacheck.mjs "ta: status, button and hello message in its own script ..."` |
| AC-E14.F5.11 | A number has an answer (added in 1.2.3.1) | The owner presses the button that offers the other answer | The answer switches with one tap, without opening WhatsApp again, and can be switched back | `wacheck.mjs "one tap switches it to "on WhatsApp", and the button now offers "No""`, `wacheck.mjs "and back again; still no sideways scroll at 375 px"` |

Note: no app can look a number up on WhatsApp beforehand. The check depends on the owner's answer.

---

## Traceability: PRD requirement to features

| PRD requirement | What it says, in short | Features |
| --- | --- | --- |
| F-1 | Balance = all credit minus all payments | E1.F7, E4.F2 |
| F-2 | Customer is a 10 digit phone; no two share one | E1.F1, E6.F2 |
| F-3 | Every figure computed from stored entries | E1.F7, E2.F1, E2.F2 |
| F-4 | Dates follow the date in the page address | E2.F4, E8.F2 |
| F-5 | Data survives closing and reopening | E1.F7, E6.F1, E8.F4 |
| D-1 | Data stays on the phone; no account or server | E6.F5, E8.F4 |
| D-2 | Loading a file replaces the shop, after checks | E6.F1, E6.F2, E6.F3, E6.F6 |
| D-3 | Total = sum of all balances, advances netted; owed figure in a note below the total | E2.F1, E2.F3, E1.F7 |
| D-4 | Delete can be undone straight away | E1.F6, E1.F5 (edit), E6.F3 |
| S-1 | Sample shows 11,820 and matching balances | E2.F1, E2.F3, E6.F5 |
| S-2 | Remind opens WhatsApp with name and Rs 3,470 | E3.F1 |
| S-3 | A payment of 500 survives reload | E1.F7 |
| S-4 | A bad file replaces nothing and says why | E6.F2 (built differently, see the note there), E6.F3 |
| Q-1 | Shop's own UPI ID, demo ID until set | E3.F3, E6.F2 |
| Stage 1: add customer, credit or payment | | E1.F1, E1.F2 |
| Stage 1: balances and total | | E1.F7, E2.F1, E2.F3 |
| Stage 1: customer page newest first | | E4.F1, E4.F2 |
| Stage 1: WhatsApp reminder | | E3.F1 |
| Stage 1: import a shop file | | E6.F1, E6.F2 |
| Stage 1: 375 px, light and dark | | E7.F3, E7.F4 |
| Stage 2: payment screenshot | | E1.F3 |
| Stage 2: overdue list | | E2.F4, E3.F4 |
| Stage 2: edit and delete | | E1.F5, E1.F6 |
| Stage 2: export to a spreadsheet file | | E6.F4 |
| Stage 2: search by name or phone | | E2.F5 |
| Stage 2: UPI link in the reminder | | E3.F2, E3.F5 |
| Stage 2: Hindi and Telugu labels | | E7.F1, E7.F2 |
| Stage 2: amount read from a screenshot | | E1.F4 |
| Success: no sideways scroll at 375 px; WCAG AA | | E7.F3, E7.F4, E7.F5 |
| Brief "How they check" selectors | | E8.F3 |
| Projects page "Done when" lines (`brief/spec-page.md`) | Followed literally by `tests/brief.mjs` in a fresh browser | E1.F1, E1.F3, E1.F7, E2.F1, E2.F3, E3.F1, E4.F2, E6.F1, E6.F5, E7.F4, E8.F5 |
| V-11, V-15, V-16, V-17, V-19 (added at final review; in the PRD since 23 Nov 2026) | Quick entry, receipt, statement, QR, remind all, offline | E5, E4.F3, E4.F4, E3.F4, E3.F5, E8.F4 |
| V-12, V-13, V-14, V-18 (in the PRD since 23 Nov 2026) | Spreadsheet import, backup and balances files, key figures and recent entries, local-script names and digits | E6.F6, E6.F4, E2.F2, E2.F5, E4.F5, E7.F2 |
| V-1 to V-10, C-1 to C-4 (PRD, last section) | The additions of 1.1.0.0 and 1.2.0.0, each an add-on beside the ledger | E9, E10, E11, E12, E13 |
| V-20 to V-24 (PRD, last section) | Layout that holds, names in every script, tappable pay link, other ways to send, WhatsApp check | E14.F1, E14.F2, E14.F3, E14.F4, E14.F5 |
| Not in the PRD (added after the final review; CSV import is V-12) | CSV import, screenshot reading in Hindi and Telugu, Undo after an edit, an empty shop on the first visit with the empty-state buttons, the warning for a number that is not a mobile | E6.F6, E1.F4, E1.F5, E6.F5, E2.F3, E1.F1 |

## Not built, or deliberately left out

From the PRD (section 2.2, out of scope):
- Sending reminders automatically. Every reminder, including Remind all overdue, opens a WhatsApp chat and
  the owner presses Send.
- Staff logins. There is no login. Several shops on one phone is built since 1.1.0.0 (E10).
- Stock and billing.

Found in the code against the PRD or the reviews:
- S-4 is built differently: a file with some bad rows loads its good rows and lists the skipped ones;
  only a file with no usable customer changes nothing (see E6.F2).
- Import applies only part of the entry form's checks: missing, blank, negative, unreadable and over
  ₹1 crore amounts are skipped, but an amount of 0 and a future date are accepted, and a missing or
  unreadable date becomes today (counted in the message).
- Any 10 digit phone is accepted, not only mobile numbers, as the brief says: a typed number that does not
  start with 6, 7, 8 or 9 is added with a warning (E1.F1), and import accepts it with no warning (E6.F2).
- The JSON backup leaves out payment screenshots.
- Undo is one level deep. For an entry or a load it lasts as long as the toast (7 seconds); for a deleted
  customer it lasts eight seconds (E12.F4).
- A few strings stay in English in every language (the storage-full toast, the shop-file problem
  descriptions, the screenshot alt text and the statement footer), and some counts stay in Latin digits
  (E7.F1, E7.F2).
- The screenshot reader needs a network: Tesseract.js and its language data are not cached for offline.
- Ideas from `review/industry.md` not built: opening balance when adding a customer, a credit limit per
  customer, a backup reminder and a "today's collection round". The customer pay page opened from the
  reminder is built since 1.2.1.0 (E14.F3).
- An automatic check that a number is on WhatsApp. WhatsApp gives no lookup to an ordinary app; it would need
  a business account, a server and a cost. The check in E14.F5 rests on the owner's answer.
- A field to correct a name in the nine newer scripts. Those spellings are made automatically; only the Hindi
  and Telugu spellings can be corrected (E4.F5).
- The pay page in languages other than English (E14.F3).
