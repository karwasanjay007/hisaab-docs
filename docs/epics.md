# Hisaab: epics, features and acceptance criteria

This page describes version 1.5.2, the version on the live link.

Everything Hisaab does, as a reader can check it. An epic is one subject. Under it each feature has one
story (who wants it and why) and a table of acceptance criteria: the starting point (Given), what the person
does (When) and what must happen (Then). The last column names the test that proves the row. Tests are plain
scripts in the `tests/` folder of the code; "checked by hand" means the row was tried on the live link and no
script covers it. `tests/seed.mjs` is a helper the tests share, not a test.

How to run the tests is in [the implementation plan](../IMPLEMENTATION-PLAN.md#test-it); the latest results on
the live link are in [the report](../REPORT.md#latest-results-on-the-live-link).

## Epics at a glance

| Epic | Features | Criteria |
| --- | --- | --- |
| [E1 Customers](#e1-customers) | 5 | 24 |
| [E2 Entries](#e2-entries) | 6 | 26 |
| [E3 Payment screenshot](#e3-payment-screenshot) | 3 | 13 |
| [E4 Shop overview](#e4-shop-overview) | 6 | 25 |
| [E5 Reminders and getting paid](#e5-reminders-and-getting-paid) | 7 | 31 |
| [E6 Customer page, statement and tools](#e6-customer-page-statement-and-tools) | 6 | 21 |
| [E7 Quick entry, typed and by voice](#e7-quick-entry-typed-and-by-voice) | 7 | 31 |
| [E8 Shop data: load, export and the sample shop](#e8-shop-data-load-export-and-the-sample-shop) | 7 | 28 |
| [E9 Several shops and the shop profile](#e9-several-shops-and-the-shop-profile) | 4 | 16 |
| [E10 Languages and names](#e10-languages-and-names) | 6 | 21 |
| [E11 Layout and themes](#e11-layout-and-themes) | 6 | 26 |
| [E12 Offline and install](#e12-offline-and-install) | 4 | 11 |
| [E13 Checks on what is typed](#e13-checks-on-what-is-typed) | 3 | 10 |
| [E14 Links and robustness](#e14-links-and-robustness) | 7 | 23 |
| **Total** | **77** | **306** |

70 of the 306 criteria are checked by hand; the rest name the script that checks them.

## E1 Customers

### E1.F1 Add a customer

As a shop owner I add a customer with a name and a phone number, so that I can keep their account.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E1.F1.1 | "Add a customer" is open | I type a name and a 10 digit phone and press "Add customer" | The customer is on the customer list with a balance of ₹0 and the message reads "Customer added." | `tests/brief.mjs` |
| AC-E1.F1.2 | "Add a customer" is open | I leave the name empty | Nothing is added and the message reads "Enter a name." | checked by hand |
| AC-E1.F1.3 | "Add a customer" is open | I type a phone with 9 digits, or one that holds letters | Nothing is added and the message reads "Enter a 10 digit phone number, digits only." | `tests/brief.mjs` |
| AC-E1.F1.4 | A customer has the phone 9000000101 | I add another customer with the same phone | Nothing is added and the message reads "A customer with this phone already exists." | checked by hand |
| AC-E1.F1.5 | "Add a customer" is open | I type the phone as "+91 90000 00101" | The number is kept as the 10 digits 9000000101 | `tests/gaps.mjs` |
| AC-E1.F1.6 | "Add a customer" is open | I add a 10 digit phone that does not start with 6, 7, 8 or 9 | The customer is added and a warning reads "Check the number: Indian mobile numbers start with 6, 7, 8 or 9, so WhatsApp may not reach this one." | `tests/gaps.mjs` |

### E1.F2 Check a number on WhatsApp

As a shop owner I find out whether a customer's number is on WhatsApp, so that reminders reach them.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E1.F2.1 | I have just added a customer by hand | The add form shows again | A line reads "{name} added. Check the number on WhatsApp now, so reminders reach them." with the button "Check on WhatsApp" | `tests/wacheck.mjs` |
| AC-E1.F2.2 | A customer page shows "This number has not been checked on WhatsApp yet." | I press "Check on WhatsApp" | WhatsApp opens for that number with a hello message that names the customer and the shop and carries no balance | `tests/wacheck.mjs` |
| AC-E1.F2.3 | The page asks "Did WhatsApp open a chat with this number?" | I press "Yes, the chat opened" | The page reads "Checked: this number is on WhatsApp." and the answer is still there after a reload | `tests/wacheck.mjs` |
| AC-E1.F2.4 | The page asks the same question | I press "No, it said the number is not on WhatsApp" | The page reads "Not on WhatsApp. Send by SMS, or change the number." and "Other ways to send" opens | `tests/wacheck.mjs` |
| AC-E1.F2.5 | I gave the wrong answer | I press the one button that offers the other answer | The answer is switched with that one tap | `tests/wacheck.mjs` |
| AC-E1.F2.6 | I load a shop file with eight customers | The shop is loaded | No check is asked for | `tests/wacheck.mjs` |

### E1.F3 Names in other scripts

As a shop owner I see each customer's name in the script of the language I chose, so that I can read the list quickly.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E1.F3.1 | The language is English | I look at the customer list | Each name shows as typed, with no bracket after it | `tests/names.mjs` |
| AC-E1.F3.2 | The language is one of the nine newer languages | I look at the customer list | Each name is followed in brackets by the name in that language's script, with no Latin letter in the bracket | `tests/names.mjs` |
| AC-E1.F3.3 | The language is Hindi and a customer is called Ravi Kumar | I type the name in Hindi letters in the search box | That customer is found | `tests/final.mjs` |
| AC-E1.F3.4 | A customer page is open | I change "Name", "Name in Hindi" or "Name in Telugu" under "Customer details" and press "Save details" | The message reads "Customer details saved." and my own spelling is the one shown | checked by hand |

### E1.F4 Change a customer's phone number

As a shop owner I correct a customer's number, so that the account and its reminders follow the right phone.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E1.F4.1 | "More for this customer" is on the customer page | I press "Change phone number" | A box "New phone number (10 digits)" opens with "Save number" and "Cancel" | `tests/customer.mjs` |
| AC-E1.F4.2 | The box is open | I save a number that is too short | Nothing changes and the message reads "Enter a 10-digit mobile number." | `tests/customer.mjs` |
| AC-E1.F4.3 | The box is open | I save a number another customer has | Nothing changes and the message reads "Another customer already has this number." | `tests/customer.mjs` |
| AC-E1.F4.4 | The box is open | I save a new, free number | The message reads "Phone number changed.", the page address follows the new number, every entry of the customer moves with it and the total is the same | `tests/customer.mjs` |

### E1.F5 Delete a customer with Undo

As a shop owner I remove a customer and can take it back, so that a slip does not lose an account.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E1.F5.1 | "More for this customer" is on the customer page | I press "Delete customer" once | Nothing is deleted; the button reads "Press again to delete {name} and all their entries" for 5 seconds | `tests/customer.mjs` |
| AC-E1.F5.2 | The button asks me to press again | I press it again | The customer and all their entries are gone, the customer list shows, and the total drops by that customer's balance | `tests/customer.mjs` |
| AC-E1.F5.3 | A bar reads "Deleted {name}." with "Undo", for 8 seconds | I press "Undo" | The customer and the same entries are back, the total is the same again, and the bar reads "Restored {name}." | `tests/customer.mjs` |
| AC-E1.F5.4 | I delete a customer | Each step runs | No browser confirm box is used | `tests/customer.mjs` |

## E2 Entries

### E2.F1 Record a credit or a payment

As a shop owner I record what I gave on credit and what I got back, so that each account is up to date.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E2.F1.1 | A customer exists | I choose the customer, "You gave (credit)", an amount of 250 and press "Save entry" | The customer's balance rises by ₹250, the total rises by ₹250, and the message reads "Saved." | `tests/brief.mjs` |
| AC-E2.F1.2 | A customer owes ₹670 | I save "You got (payment)" of 100 | The balance reads ₹570 | `tests/brief.mjs` |
| AC-E2.F1.3 | The entry form is open | I press "Save entry" with no customer chosen | Nothing is saved and the message reads "Choose a customer." | checked by hand |
| AC-E2.F1.4 | I have saved entries | I reload the page | Every balance and the total are the same | `tests/check.mjs` |
| AC-E2.F1.5 | The entry form is open | I tap a note shortcut under "Note" (groceries, milk, rice, oil, paid on UPI, cash) | An empty note is filled with it; a second shortcut is added after a comma | `tests/langs.mjs` |

### E2.F2 Amount, date and note rules

As a shop owner I am told at once when an entry cannot be right, so that the ledger stays clean.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E2.F2.1 | The entry form is open | I save with the amount empty or zero | Nothing is saved and the message reads "Enter an amount above zero." | checked by hand |
| AC-E2.F2.2 | The entry form is open | I save an amount above 1,00,00,000 | Nothing is saved and the message reads "That amount looks too large. One entry can be up to ₹1,00,00,000." | `tests/final.mjs` |
| AC-E2.F2.3 | The entry form is open | I clear the date and save | Nothing is saved and the message reads "Enter a date." | checked by hand |
| AC-E2.F2.4 | The entry form is open | I open the date picker | Today is filled in and the picker offers no day after today | checked by hand |
| AC-E2.F2.5 | The entry form is open | I type a note | The box takes at most 80 characters | checked by hand |

### E2.F3 Whole rupees

As a shop owner I keep money in whole rupees, so that balances never carry stray paise.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E2.F3.1 | The entry form is open | I save an amount of 20.75 | Nothing is saved, the form keeps what I typed, and the message reads "Amounts are in whole rupees, without paise. This was not saved yet." with the button "Use Rs 21" | `tests/rupees.mjs` |
| AC-E2.F3.2 | The button "Use Rs 21" shows | I press it and save | The entry is saved as ₹21 | `tests/rupees.mjs` |
| AC-E2.F3.3 | I type an amount of 0.5 | I save | The entry is held and the nearest whole amount, at least Rs 1, is offered | `tests/final.mjs` |
| AC-E2.F3.4 | I edit an entry to 30.4, or send "Asha 20.5" from quick entry | I save | Nothing is saved and the form offers "Use Rs 30" or "Use Rs 21" | `tests/rupees.mjs` |
| AC-E2.F3.5 | The browser already holds amounts with paise | I open the app | They are shown as stored, with the line "{n} stored amounts have paise. They are shown as stored." and the button "Round them to whole rupees", which rounds halves up and can be undone once | `tests/rupees.mjs` |

### E2.F4 Edit an entry

As a shop owner I correct an entry, so that a mistake does not stay in the account.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E2.F4.1 | An entry is listed | I press "Edit" on it | The form is filled with that entry, the button reads "Update entry" and "Cancel edit" shows | `tests/critic.mjs` |
| AC-E2.F4.2 | An edit is open | I change the amount and press "Update entry" | The message reads "Entry updated." with "Undo", and the balance follows the new amount | `tests/gaps.mjs` |
| AC-E2.F4.3 | The message "Entry updated." shows | I press "Undo" | The entry is as it was and the balance is back | `tests/gaps.mjs` |
| AC-E2.F4.4 | An edit is open | I clear the date and save | The edit stays open and the message reads "A date is needed. Choose the date, then save." | `tests/guard.mjs` |

### E2.F5 Delete an entry with Undo

As a shop owner I delete a wrong entry and can take it back, so that a slip costs nothing.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E2.F5.1 | An entry is listed | I press "Delete" on it | The entry is gone at one press, the balance changes, and the message reads "Entry deleted." with "Undo" | `tests/check.mjs` |
| AC-E2.F5.2 | The message "Entry deleted." shows | I press "Undo" | The entry is back at the same place and the balance is the same again | checked by hand |
| AC-E2.F5.3 | A message with "Undo" shows | I wait 7 seconds | The message closes and that change can no longer be undone | checked by hand |

### E2.F6 Balances

As a shop owner I see what each customer owes worked out from their entries, so that the figure is always right.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E2.F6.1 | A customer has credit and payment entries | I look at their row | The balance is all credit less all payments | `tests/ledger.cjs` |
| AC-E2.F6.2 | A customer has paid ₹200 more than they took | I look at their row | The balance reads -₹200 with the word "Advance" | `tests/brief.mjs` |
| AC-E2.F6.3 | A balance is ₹1,18,200 | I look at the row | It is one figure with Indian grouping | `tests/critic.mjs` |
| AC-E2.F6.4 | Any hidden shop file is loaded | I read each balance | Every balance matches the figure worked out from the file | `tests/sweep.mjs` |

## E3 Payment screenshot

### E3.F1 Attach a payment screenshot

As a shop owner I keep the customer's payment screenshot with the entry, so that I have proof of what was paid.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E3.F1.1 | The entry form is open | I press "Choose screenshot" and pick a picture | The type switches to "You got (payment)" | checked by hand |
| AC-E3.F1.2 | A picture is chosen | I save the entry | The entry shows a small picture, and it is still there after a reload | `tests/brief.mjs` |
| AC-E3.F1.3 | A picture is chosen | I press "Save entry" at once, without waiting | The entry is still saved with its picture | `tests/critic.mjs` |
| AC-E3.F1.4 | The entry form is open | I pick a file that is not an image | The message reads "That file is not a picture." | checked by hand |
| AC-E3.F1.5 | I pick a picture of 900 by 2700 pixels | I save the entry | The stored picture is no more than 600 pixels on its longer side | `tests/guard.mjs` |

### E3.F2 Read the amount from the screenshot

As a shop owner I have the amount read from the screenshot, so that I type less.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E3.F2.1 | I choose a screenshot that shows "Rs 120", "₹ 500" or "Rs. 2,340.00" | The reading finishes | "Amount (₹)" is filled and the message reads "Read Rs {amount} from the screenshot. Check it before saving." | `tests/critic.mjs` |
| AC-E3.F2.2 | I typed an amount before choosing the screenshot | The reading finishes | My typed amount is left alone | checked by hand |
| AC-E3.F2.3 | The picture has no amount the reader can find | The reading finishes | The message reads "Could not read an amount from this picture. Type it in." and the picture stays attached | checked by hand |
| AC-E3.F2.4 | The language is Hindi | I choose a screenshot written in Hindi | The amount is read | `tests/gaps.mjs` |

### E3.F3 Where the reader comes from, and its limits

As a shop owner I know what the screenshot reader needs, so that I am not surprised when it cannot read.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E3.F3.1 | I open the app and never choose a screenshot | The page loads | No file is asked from any other site | `tests/local-assets.mjs` |
| AC-E3.F3.2 | I choose a screenshot for the first time | The message "Reading the amount from the screenshot..." shows | The reader is fetched from `cdn.jsdelivr.net`; the picture itself is read in the browser | checked by hand |
| AC-E3.F3.3 | The phone is offline | I choose a screenshot | The picture is attached and I type the amount myself | checked by hand |
| AC-E3.F3.4 | The language is not English, Hindi or Telugu | I choose a screenshot | The reader looks for English text only | checked by hand |

## E4 Shop overview

### E4.F1 Total outstanding

As a shop owner I see one figure for what the whole shop is owed, so that I know where I stand.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E4.F1.1 | The sample shop is loaded | I look at "Total outstanding" | It reads ₹11,820, the sum of every customer's balance | `tests/check.mjs` |
| AC-E4.F1.2 | One customer is ₹200 in advance and the others owe ₹2,095 | I look at the total | It reads ₹1,895 and a note beside it reads "₹2,095 owed, less ₹200 paid in advance" | `tests/brief.mjs` |
| AC-E4.F1.3 | Any hidden shop file is loaded | I look at the total | It matches the figure worked out from the file | `tests/sweep.mjs` |
| AC-E4.F1.4 | The address ends in `?today=2026-11-15` | I look under the total | The date the figures are for is shown, and every count of days is made to that date | `tests/check.mjs` |

### E4.F2 Key figures

As a shop owner I see the few figures that matter beside the total, so that I do not have to add anything up.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E4.F2.1 | The app is open on a phone | I look at the strip under the total | It shows three figures in one row: "Customers" (those who owe), "Overdue" and "Collected, last 30 days", each label readable in full | `tests/review-fixes.mjs` |
| AC-E4.F2.2 | The strip is showing | I press its "…" button | The full overview opens with "Owes the most", "Collected, last 30 days", "Given on credit, last 30 days" and "Longest without paying" | checked by hand |
| AC-E4.F2.3 | Nobody owes anything yet | I look at "Longest without paying" | It reads "Nobody yet" | `tests/guard.mjs` |
| AC-E4.F2.4 | Entries are dated over several months | I look at "Collected, last 30 days" | Only payments dated today or in the 29 days before are counted | checked by hand |

### E4.F3 The customer list and its order

As a shop owner I see who owes the most first, so that I know whom to speak to.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E4.F3.1 | Several customers owe | I look at the customer list | Those who owe come first, the largest balance at the top, with the badge "Owes the most" on the first | `tests/sweep.mjs` |
| AC-E4.F3.2 | The list is showing | I look at a row | It shows the name, the phone, the number of entries, the balance and a WhatsApp reminder button | `tests/check.mjs` |
| AC-E4.F3.3 | The shop is empty | I open the app | The list reads "No customers yet. Add your first customer, or load the sample shop to look around." with "Add your first customer" and "Load sample shop" | `tests/guard.mjs` |
| AC-E4.F3.4 | The app is open on a phone or a desktop | The first screen shows | The customer list is on the first screen, above the quick-entry pill | `tests/phone-ledger.mjs`, `tests/desk-ledger.mjs` |

### E4.F4 The overdue rule

As a shop owner I see who has gone longest without paying, so that I remind the right people.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E4.F4.1 | Several customers owe | I open "Overdue" | Everyone who owes is listed, the longest without paying first; a customer who never paid is counted from their first credit | `tests/check.mjs` |
| AC-E4.F4.2 | A customer has gone 30 days or more without paying | I look at their overdue row | It carries the badge "30+ days"; a customer at 29 days does not | `tests/final.mjs` |
| AC-E4.F4.3 | Some customers are 30 days or more overdue | I look above the overdue list | A line reads "{amount} unpaid for 30 days or more, from {n} customers" | `tests/langs.mjs` |
| AC-E4.F4.4 | Nobody owes anything | I open "Overdue" | It reads "Nobody owes anything. Well done." | `tests/guard.mjs` |
| AC-E4.F4.5 | The overdue list is showing | I read the hint | It reads "Remind after 30 days without paying" | checked by hand |

### E4.F5 Search

As a shop owner I find a customer by name or number, so that a long list is no trouble.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E4.F5.1 | The sample shop is loaded | I type "Sunita" in "Search by name or phone" | Only Sunita Devi's row shows and the count reads the rows shown out of all | `tests/check.mjs` |
| AC-E4.F5.2 | The sample shop is loaded | I type a phone with +91 in front | That customer's row shows | `tests/final.mjs` |
| AC-E4.F5.3 | The sample shop is loaded | I type a name nobody has | A line reads 'No customer matches "{what I typed}".' | `tests/final.mjs` |
| AC-E4.F5.4 | The app is open on a phone | I look at the search box | It is at least 44 pixels high | `tests/review-fixes.mjs` |

### E4.F6 Recent entries and the list filters

As a shop owner I see the latest entries of the whole shop, so that I can check what was just recorded.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E4.F6.1 | The shop has more than 8 entries | I open "Recent entries" | The newest 8 entries of all customers show, each with "Edit" and "Delete" | checked by hand |
| AC-E4.F6.2 | "Recent entries" is open on a phone | I press "Edit" on an entry | The entry form comes into view, filled | `tests/critic.mjs` |
| AC-E4.F6.3 | The window is 1200 pixels wide or more | I look at the right panel | It holds "Record an entry", "Overdue" and "Recent entries", with no scroll box of its own | `tests/desk-ledger.mjs` |
| AC-E4.F6.4 | The right panel is showing | I type in the filter box of "Overdue" or "Recent entries" | Only the rows that hold the typed text stay in that list | `tests/desk-ledger.mjs` |

## E5 Reminders and getting paid

### E5.F1 The WhatsApp reminder

As a shop owner I send a customer a reminder in one tap, so that asking for money takes no typing.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E5.F1.1 | A customer owes | I look at their WhatsApp reminder button | Its link is `https://wa.me/91<10 digit phone>?text=<message>` | `tests/check.mjs` |
| AC-E5.F1.2 | A customer owes | I read the message | It begins "Namaste {name}, this is a reminder from {shop}. Your balance is Rs {amount}." and ends "Thank you." | `tests/brief.mjs` |
| AC-E5.F1.3 | Any hidden shop file is loaded | I read the reminder of the customer who owes the most | It carries that customer's name and balance | `tests/sweep.mjs` |
| AC-E5.F1.4 | A customer is in advance, or settled | I read the message | It reads "Namaste {name}, this is {shop}. You have Rs {amount} in advance with us. Thank you." or "Namaste {name}, this is {shop}. Your account is settled, Rs 0 due. Thank you." | checked by hand |
| AC-E5.F1.5 | I tap the reminder button | WhatsApp opens | The message is ready and I press Send myself; the app sends nothing | checked by hand |

### E5.F2 The pay links and the pay page

As a customer I tap a link in the reminder and my UPI app opens with the amount filled in, so that paying takes a moment.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E5.F2.1 | A customer owes | I read the reminder | It holds a line "UPI pay link:" with an https link to the pay page, and after it "Pay by UPI:" with a link that starts `upi://pay?pa=<UPI ID>&am=<balance with two decimals>` | `tests/pay.mjs` |
| AC-E5.F2.2 | The reminder is built | I look at the pay page link | The shop, the customer name, the UPI ID and the amount come after "#", so they are not sent to any server | `tests/pay.mjs` |
| AC-E5.F2.3 | I open the pay page from the link | The page shows | It shows "Pay to", the shop, the amount, the UPI ID with "Copy", and the button "Open your UPI app", which opens the same UPI link as the message | `tests/pay.mjs` |
| AC-E5.F2.4 | The pay page link is cut short or changed | I open it | The page reads "This payment link is not complete. Ask the shop to send it again." | `tests/pay.mjs` |
| AC-E5.F2.5 | A customer page is open and the balance is above zero | I look at the buttons | "UPI pay link" and "UPI QR code" show; with nothing owed they do not | `tests/final.mjs` |

### E5.F3 The shop's UPI ID

As a shop owner I set my own UPI ID once, so that every pay link pays my shop.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E5.F3.1 | "Shop settings" is open | I type a UPI ID such as shopname@okbank and press "Save settings" | Every reminder and pay link uses that UPI ID | `tests/gaps.mjs` |
| AC-E5.F3.2 | "Shop settings" is open | I save a UPI ID that is not of the form name@bank | It is not saved and the message reads "Enter a UPI ID like shopname@okbank, or leave it empty." | `tests/gaps.mjs` |
| AC-E5.F3.3 | No shop UPI ID is set | I read a reminder | The pay links use the demo ID `hisaab.demo@upi` | `tests/critic.mjs` |
| AC-E5.F3.4 | No shop UPI ID is set | I look under the customer list or on a customer page | A notice reads "Reminders now ask customers to pay the demo UPI ID hisaab.demo@upi, not your shop." with the link "Set your shop's UPI ID" | `tests/review-fixes.mjs` |
| AC-E5.F3.5 | The notice is showing | I save my own UPI ID | The notice is gone and the reminder links are otherwise the same | `tests/review-fixes.mjs` |

### E5.F4 The UPI QR code

As a shop owner I show a customer a QR code for exactly what they owe, so that they can pay at the counter.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E5.F4.1 | A customer owes | I press "UPI QR code" on their page | A QR code is drawn with the amount, the UPI ID and the line "Scan with any UPI app to pay {shop}." | `tests/final.mjs` |
| AC-E5.F4.2 | The QR card is open | I open another customer's page | The card is closed | `tests/final.mjs` |
| AC-E5.F4.3 | I open a customer's QR code | The code is drawn | The QR library comes from the site itself; no other site is asked | `tests/local-assets.mjs` |
| AC-E5.F4.4 | I visited the app once online | I open a QR code while offline | The code is still drawn | `tests/offline.mjs` |

### E5.F5 Remind all overdue

As a shop owner I work through everyone who is overdue with one button, so that the weekly round is quick.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E5.F5.1 | Four customers are 30 days or more overdue | I open "Overdue" | A button reads "Remind all overdue (4)" | `tests/final.mjs` |
| AC-E5.F5.2 | The button is showing | I press it | WhatsApp opens for the customer who is longest overdue, and the button reads "Next reminder: {name} (2 of 4)" | `tests/final.mjs` |
| AC-E5.F5.3 | I have pressed it for every customer | I look at the button | It reads "All 4 reminders opened" | checked by hand |
| AC-E5.F5.4 | Fewer than two customers are 30 days or more overdue | I open "Overdue" | The button is not shown | checked by hand |
| AC-E5.F5.5 | The "+" menu is open | I press "Remind all overdue" | The same round starts, or "Overdue" opens when the button is not showing | checked by hand |

### E5.F6 Other ways to send

As a shop owner I can still reach a customer whose number is not on WhatsApp, so that no reminder is lost.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E5.F6.1 | A customer page is open | I open "Other ways to send" | It offers "Send as SMS", "Choose a contact in WhatsApp" and "Copy the message" | `tests/sendways.mjs` |
| AC-E5.F6.2 | "Other ways to send" is open | I look at "Send as SMS" | It opens an SMS to the customer's number with the same message as the WhatsApp reminder | `tests/sendways.mjs` |
| AC-E5.F6.3 | "Other ways to send" is open | I press "Copy the message" | The message is on the clipboard and the button says "Message copied" | `tests/sendways.mjs` |
| AC-E5.F6.4 | The customer is one of the sample shop | I open their page | The block opens by itself and reads "This is a sample number, not the customer's own. To try a reminder, first change it to your own number." | `tests/sendways.mjs` |

### E5.F7 The receipt after an entry

As a shop owner I send the customer a receipt straight after an entry, so that both of us have the same record.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E5.F7.1 | I save an entry | The message "Saved." shows | It carries the link "Send receipt" for 7 seconds | checked by hand |
| AC-E5.F7.2 | I saved a credit | I tap "Send receipt" | WhatsApp opens for the customer with "Namaste {name}, {shop}: Rs {amount} added to your account on {date}. Balance now Rs {balance}." | checked by hand |
| AC-E5.F7.3 | I saved a payment | I tap "Send receipt" | The message reads "Namaste {name}, {shop}: payment of Rs {amount} received on {date}, thank you. Balance now Rs {balance}." | checked by hand |

## E6 Customer page, statement and tools

### E6.F1 The customer page

As a shop owner I open one customer and see their whole account, so that I can answer them at the counter.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E6.F1.1 | The customer list is showing | I tap a customer | Their page opens with the name, the phone as "+91 XXXXX XXXXX", "Balance" and "Last paid" with a date or "never" | `tests/brief.mjs` |
| AC-E6.F1.2 | A customer page is open | I look at the buttons | "WhatsApp reminder" and "Statement (PDF)" show, and "All customers" leads back to the list | checked by hand |
| AC-E6.F1.3 | A customer page is open | I record an entry there | The form needs no customer chosen and the entry goes to this customer | `tests/quick.mjs` |
| AC-E6.F1.4 | The address names a phone no customer has | The page loads | The home view shows | checked by hand |

### E6.F2 History with running balance

As a shop owner I see every entry of a customer with the balance after it, so that I can show how the figure came about.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E6.F2.1 | A customer has several entries | I look at "Entries, newest first" | The newest entry is at the top; a payment of 25 October stands above a credit of 23 October | `tests/brief.mjs` |
| AC-E6.F2.2 | The list is showing | I look at an entry | It shows the date, the note, the amount, "Balance after", and "Edit" and "Delete" | checked by hand |
| AC-E6.F2.3 | Two entries have the same date | I look at the list | The one recorded later is shown first, as the list runs newest first | `tests/ledger.cjs` |

### E6.F3 Statement as PDF or print

As a shop owner I give a customer a statement, so that they have the account on paper or as a file.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E6.F3.1 | A customer page is open | I press "Statement (PDF)" | The browser's print box opens with a page headed by the shop and "Account statement", the customer, the phone and the date | `tests/profile.mjs` |
| AC-E6.F3.2 | The statement is built | I read its table | It has the columns Date, Note, You gave, You got and Balance, and a totals row | checked by hand |
| AC-E6.F3.3 | The customer owes | I read the statement | It carries a QR code with "Scan to pay" | `tests/final.mjs` |
| AC-E6.F3.4 | A shop profile is saved | I read the statement | The owner, the address and the logo show under the shop name | `tests/profile.mjs` |
| AC-E6.F3.5 | The print box is open | I choose to save as PDF | The browser saves the statement as a PDF file | checked by hand |

### E6.F4 Totals and days since the last payment

As a shop owner I see a customer's totals at a glance, so that I know how they pay.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E6.F4.1 | A customer page is open | I look at "More for this customer" | "Total given on credit" and "Total received" are the sums of that customer's entries | `tests/customer.mjs` |
| AC-E6.F4.2 | The customer last paid on 25 October and today is 15 November | I read the block | It reads "21 days since the last payment" | `tests/customer.mjs` |
| AC-E6.F4.3 | The customer has never paid | I read the block | It reads "No payment yet" | checked by hand |

### E6.F5 Call and WhatsApp chat links

As a shop owner I ring or message a customer from their page, so that I do not look the number up.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E6.F5.1 | "More for this customer" is showing | I look at "Call" | Its link is `tel:+91<phone>` | `tests/customer.mjs` |
| AC-E6.F5.2 | "More for this customer" is showing | I look at "WhatsApp chat" | Its link is `https://wa.me/91<phone>` with no message, and it opens apart from the app | `tests/customer.mjs` |
| AC-E6.F5.3 | The block is showing | I count the reminder buttons on the page | There are as many as without the block; the chat link is not a reminder | `tests/customer.mjs` |

### E6.F6 The balance history line

As a shop owner I see how a customer's balance has moved, so that I can tell a slow payer from a steady one.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E6.F6.1 | A customer has two or more entries | I open their page | "Balance over time" shows a line of the balance after each entry | `tests/profile.mjs` |
| AC-E6.F6.2 | The line is showing | A screen reader reads it | It says "Balance went from {first} to {last} over {n} entries" and "Highest {figure}, lowest {figure}" | `tests/profile.mjs` |
| AC-E6.F6.3 | A customer has one entry | I open their page | No line is shown | `tests/profile.mjs` |

## E7 Quick entry, typed and by voice

### E7.F1 The quick-entry pill

As a shop owner I type one short line and the entry is recorded, so that I keep up with a busy counter.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E7.F1.1 | The app is open on a phone | I type "Sunita 500 udhaar" in the pill and press the send arrow | The entry is filled and saved and Sunita Devi's balance rises by ₹500 | `tests/phone-ledger.mjs` |
| AC-E7.F1.2 | The app is open on a desktop | I type the same line in the pill at the bottom of the customer list and press Enter | The entry form is filled, with the cursor on "Save entry", and the message reads "Filled: {name}, gave ₹500. Check it, then press Save entry." | `tests/quick.mjs` |
| AC-E7.F1.3 | The pill is empty | I press the send arrow | Nothing is saved and the cursor goes into the box | checked by hand |
| AC-E7.F1.4 | The pill is showing | I press "+" | The menu of all actions opens with its fifteen items, listed under [E11.F3](#e11f3-the--menu) | checked by hand |
| AC-E7.F1.5 | A customer page is open | I type "200 paid" with no name | The entry is filled for this customer | `tests/quick.mjs` |

### E7.F2 The parser and its rules

As a shop owner I write the line the way I speak, so that I do not learn a format.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E7.F2.1 | The sample shop is loaded | I type "sunita devi 1,500 credit groceries" | The amount is 1,500, the type is "You gave (credit)" and the note is "groceries" | `tests/quick.mjs` |
| AC-E7.F2.2 | The sample shop is loaded | I type "Ravi paid 200" or "Ravi 200 upi" | The type is "You got (payment)"; with "upi" the note reads "UPI" | `tests/quick.mjs` |
| AC-E7.F2.3 | The sample shop is loaded | I type "fatima ₹2.5 hazar jama" | The amount is 2,500 and the type is payment | `tests/quick.mjs` |
| AC-E7.F2.4 | The sample shop is loaded | I type "9000000105 750 paid" | The customer with that phone is chosen | `tests/quick.mjs` |
| AC-E7.F2.5 | The sample shop is loaded | I type "Anjli 120", one letter off and with no word for the type | Anjali is chosen, the entry is taken as credit, and the message adds "(no word for paid, so taken as gave)" | `tests/quick.mjs` |

### E7.F3 Words in other languages

As a shop owner I write or say the line in my own language, so that quick entry works for me.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E7.F3.1 | The sample shop is loaded | I type "सुनीता 500 उधार" | Sunita Devi is chosen, 500, credit | `tests/quick.mjs` |
| AC-E7.F3.2 | The sample shop is loaded | I type "रवि ने ५०० जमा किए", with Hindi digits | Ravi Kumar is chosen, 500, payment | `tests/quick.mjs` |
| AC-E7.F3.3 | The sample shop is loaded | I type "సునీత 300 అప్పు" | Sunita Devi is chosen, 300, credit | `tests/quick.mjs` |
| AC-E7.F3.4 | I choose one of the nine newer languages | The language is loaded | Its word for credit, the one in its own example line, is understood by quick entry | checked by hand |

### E7.F4 Unclear lines and the save rule

As a shop owner I am asked when a line is not clear, so that nothing wrong is recorded.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E7.F4.1 | The sample shop is loaded | I send a line with no amount | Nothing is filled and the message reads "Say or type an amount, for example: Sunita 500 udhaar." | `tests/quick.mjs` |
| AC-E7.F4.2 | The sample shop is loaded | I send a line with no customer name | The message reads "Which customer? Say the name, for example: Sunita 500 udhaar." | `tests/quick.mjs` |
| AC-E7.F4.3 | Two customers match the name equally | I send the line | The message reads "More than one customer matches. Pick one:" with a button for each, up to six | checked by hand |
| AC-E7.F4.4 | A line has filled the form on a desktop | I look at the ledger | Nothing is saved until I press "Save entry" | `tests/quick.mjs` |

### E7.F5 A customer it does not know

As a shop owner I am offered to add a customer the line names, so that a new customer does not stop me.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E7.F5.1 | No customer is called Sunita | I send "Sunita 500 udhaar" | Under the question a line reads 'No customer matches "Sunita".' with the button "Add a customer: Sunita" | `tests/assist.mjs` |
| AC-E7.F5.2 | The button is showing | I press it | "Add a customer" opens with the name filled and the cursor in the phone box | `tests/assist.mjs` |
| AC-E7.F5.3 | I have added her | I send the same line again | The form is filled: "Filled: Sunita, gave ₹500. Check it, then press Save entry." | `tests/assist.mjs` |

### E7.F6 Voice input

As a shop owner I say the entry, so that my hands stay free.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E7.F6.1 | The browser has speech recognition | I look at the pill | The microphone button "Speak the entry" shows; in a browser without it the button is hidden | `tests/voice.mjs` |
| AC-E7.F6.2 | I press the microphone | The app listens | The message reads "Listening. Say, for example: Sunita 500 udhaar." | checked by hand |
| AC-E7.F6.3 | The app is listening | "Ravi 500 udhaar" is heard | The form is filled for Ravi Kumar with 500 | `tests/voice.mjs` |
| AC-E7.F6.4 | The language is Telugu and the speech service refuses it | I press the microphone | The app tries once more in Indian English and the entry is heard | `tests/voice.mjs` |
| AC-E7.F6.5 | The microphone is blocked, or the device has none | I press the microphone | The message reads "The microphone is blocked for this site. ..." or "No microphone was found on this device. Type the entry instead.", and the button is released | `tests/voice.mjs` |

### E7.F7 Voice never ends in silence

As a shop owner I am always told what happened after I press the microphone, so that I am not left waiting.

| ID | Given | When | Then | Test |
|---|---|---|---|---|
| AC-E7.F7.1 | The app is listening | The speech service stops without hearing anything | The message reads "Did not catch that. Try again or type it.", not "Listening" | `tests/voice-quiet.mjs` |
| AC-E7.F7.2 | The app is listening | The speech service cannot be reached, or nothing comes back for 9 seconds | The message reads "This browser's speech service did not answer. Voice needs the internet and works best in Chrome. Type the entry instead." | `tests/voice-quiet.mjs` |
| AC-E7.F7.3 | Voice failed on a phone | I read the message | It adds "Tip: tap the box and use the microphone on your keyboard. It works in every browser." | `tests/voice-quiet.mjs` |
| AC-E7.F7.4 | Voice failed on a computer | I read the message | It adds the tip to press the Windows key and H together, or the microphone key on a Mac | `tests/voice-quiet.mjs` |
| AC-E7.F7.5 | The sentence is heard | The form is filled | No tip is shown | `tests/voice-quiet.mjs` |

## E8 Shop data: load, export and the sample shop

Goal: a shop can be brought in from a shop file or a spreadsheet, checked on the way in, taken back with Undo, and taken out again as spreadsheet files and a backup file.

### E8.F1 Load a shop file

As the shop owner, I want to load a shop from a file so that I can move my ledger or start again from a backup.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E8.F1.1 | A shop file (JSON) is pasted into the box under "Load a shop" | The owner presses "Load shop" | The file replaces the current shop, the message reads "Loaded {shop}: {c} customers, {e} entries." and every balance and the total are worked out again from the new entries | `tests/check.mjs` |
| AC-E8.F1.2 | A shop file that has customers and no entries list at all | It is loaded | It loads: every customer is listed with a balance of 0 and the total is 0 | `tests/phone-import.mjs` |
| AC-E8.F1.3 | A shop was loaded | The page is opened again | The loaded shop is still there | `tests/check.mjs` |
| AC-E8.F1.4 | The owner presses "Choose file" | A .json, .csv or .txt file is picked | Its text is loaded in the same way as pasted text | checked by hand |
| AC-E8.F1.5 | Thirty different generated shops, one after another | Each is loaded | Every customer's balance and the total match the figures worked out from the file | `tests/sweep.mjs` |

### E8.F2 A file that cannot be loaded

As the shop owner, I want a broken file refused with a reason so that my ledger is not replaced by nothing.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E8.F2.1 | The box is empty, or the file has no customers list, or its entries are not a list | "Load shop" is pressed | 'The file needs "customers" and "entries" lists.' shows and the shop is unchanged | `tests/phone-import.mjs` |
| AC-E8.F2.2 | Text that starts with { or [ and is not valid JSON | "Load shop" is pressed | "This is not valid JSON: " shows with the browser's reason and the shop is unchanged | checked by hand |
| AC-E8.F2.3 | A file in which no customer or entry can be used | "Load shop" is pressed | The first three problems are shown as the error and the shop is unchanged | checked by hand |

### E8.F3 Rows that are cleaned or skipped

As the shop owner, I want the good rows loaded and the bad ones named so that one wrong row does not stop the whole file.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E8.F3.1 | Some entries have an unknown type, an amount that is empty, not a number or below zero, or a phone that is not 10 digits | The file is loaded | Those rows are left out, the good rows load, and the message adds "Skipped {n}: " with the first three problems | `tests/gaps.mjs` |
| AC-E8.F3.2 | An entry has no date, or a date that cannot be read | The file is loaded | The entry is kept with today's date and the message adds "{n} entries had no date and are dated today." | `tests/gaps.mjs` |
| AC-E8.F3.3 | Amounts written with commas, spaces, Rs, INR, ₹, "/-" or "only"; dates written day first with /, . or - | The file is loaded | The amounts and dates are read correctly | `tests/gaps.mjs` |
| AC-E8.F3.4 | The same phone twice in the customers list; an entry for a phone that is not in the list; a customer with no name | The file is loaded | The phone is kept once; the unlisted phone becomes a customer; the customer with no name is shown by the phone number | checked by hand |
| AC-E8.F3.5 | An entry's amount has paise | The file is loaded | The amount is rounded to whole rupees, halves up, and the message adds "{n} amounts had paise and were rounded to whole rupees." | `tests/rupees.mjs`, `tests/ledger.cjs` |
| AC-E8.F3.6 | An entry carries a picture that is not an image, or one over 400,000 characters long | The file is loaded | The picture is left out, the entry is kept, and the message adds "{n} pictures were left out (not an image, or too large). Their entries were kept." | `tests/guard.mjs` |

### E8.F4 Undo a load

As the shop owner, I want to take a load back so that a wrong file does not cost me my ledger.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E8.F4.1 | A shop has just been loaded | The message at the bottom shows | It reads "Shop loaded." with an "Undo" button, and no pop-up dialog asks anything | `tests/final.mjs` |
| AC-E8.F4.2 | "Shop loaded." is still showing | The owner presses "Undo" | The shop as it was before the load is back, with its total | `tests/final.mjs` |

### E8.F5 Load a spreadsheet saved as CSV

As the shop owner, I want to load a hand-made sheet so that I do not have to type an old ledger in again.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E8.F5.1 | Pasted text that does not start with { or [, with a header row and commas or tabs between cells | "Load shop" is pressed | It is read as a spreadsheet and goes through the same checks as a shop file | `tests/gaps.mjs` |
| AC-E8.F5.2 | A sheet with one column for credit and one for payment (for example "udhaar" and "jama") | It is loaded | Each filled cell becomes an entry of that kind and the balances add up | `tests/gaps.mjs` |
| AC-E8.F5.3 | The file made by "Export entries (CSV)" | It is pasted back and loaded | The shop comes back with the same total | `tests/gaps.mjs` |
| AC-E8.F5.4 | Text with no phone column, or with too little to read | "Load shop" is pressed | "Paste a shop file (JSON) or a spreadsheet saved as CSV with name, phone, type, amount and date columns." shows and the shop is unchanged | `tests/gaps.mjs` |

### E8.F6 Export

As the shop owner, I want my ledger as files so that I have a copy outside the browser.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E8.F6.1 | Any shop | The owner presses "Export entries (CSV)" | A file `hisaab-<shop>-entries.csv` is saved with the columns date, name, phone, type, amount, note, has_screenshot, oldest entry first | `tests/final.mjs`, `tests/check.mjs` |
| AC-E8.F6.2 | Any shop | The owner presses "Export balances (CSV)" | A file `hisaab-<shop>-balances.csv` is saved with name, phone, total_credit, total_payment, balance, last_payment for each customer and a last row TOTAL OUTSTANDING | checked by hand |
| AC-E8.F6.3 | Any shop | The owner presses "Backup file (JSON)" | A file `hisaab-<shop>.json` is saved with the shop name, the UPI ID if one is set, the customers and the entries; it loads back as a shop file. Screenshots are not in it | `tests/profile.mjs` |
| AC-E8.F6.4 | A name or note that starts with =, +, -, @, a tab or a return | It is exported to CSV | The cell gets a leading apostrophe, so a spreadsheet shows it as text and does not run it | checked by hand |

### E8.F7 The sample shop and shop settings

As the shop owner, I want to look around with a sample and then name my own shop so that I can try the app before I trust it.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E8.F7.1 | A first visit | The app opens | The shop is "My shop" with no customers; the Customers card offers "Add your first customer" and "Load sample shop" | checked by hand |
| AC-E8.F7.2 | An empty shop, or the Shop file view | The owner presses "Load sample shop" | "Lakshmi Provision Store (fictional)" loads with 8 customers and 38 entries, with "Undo" offered as for any load | `tests/guard.mjs` |
| AC-E8.F7.3 | Shop settings | The owner types a "Shop name" and presses "Save settings" | "Settings saved." shows and the top bar carries the new name | checked by hand |
| AC-E8.F7.4 | Shop settings, "Shop UPI ID (for pay links)" | A value that is not of the form name@bank is saved | "Enter a UPI ID like shopname@okbank, or leave it empty." shows and the old value stays | `tests/gaps.mjs` |

## E9 Several shops and the shop profile

Goal: one device can hold more than one shop, each with its own ledger, and each shop can carry its owner's name, address and logo.

### E9.F1 Add a shop and switch

As an owner of two shops, I want both ledgers on one phone so that I do not mix them.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E9.F1.1 | One shop only | Shop settings is opened | The "Shops" block lists that shop as "Current" with no "Switch" or "Remove" button; the ledger reads exactly as it does without the block | `tests/shops.mjs` |
| AC-E9.F1.2 | A name typed under "Add a new shop" | The owner presses "Add shop" | An empty shop of that name opens and 'Added "{n}". It is now the current shop.' shows | `tests/shops.mjs` |
| AC-E9.F1.3 | Two shops | The owner presses "Switch" on the other one | That shop opens with its own customers and total, 'Switched to "{n}".' shows, and the first shop is unchanged when switched back | `tests/shops.mjs` |
| AC-E9.F1.4 | Two or more shops | The page is opened again | The same shop is current and the others are still listed and open | `tests/shops.mjs` |
| AC-E9.F1.5 | Two shops at 320 px wide, in Telugu | The Shops block is shown | Nothing scrolls sideways and every control is at least 44 px tall | `tests/shops.mjs`, `tests/addons-width.mjs` |

### E9.F2 Remove a shop

As the owner, I want to remove a shop I no longer keep, without doing it by a slip of the finger.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E9.F2.1 | A shop that is not the current one | The owner presses "Remove" once | The button reads "Press again to remove" and nothing is removed | `tests/shops.mjs` |
| AC-E9.F2.2 | The button reads "Press again to remove" | The owner presses it again | The shop is removed from the list and 'Removed "{n}".' shows | `tests/shops.mjs` |

### E9.F3 Import as a new shop

As the owner, I want to load a file as another shop so that it does not replace the one I have open.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E9.F3.1 | A shop file in the load box | The owner presses "Import as a new shop" | A new shop holding the file is added and becomes current; the shop that was open is untouched | `tests/shops.mjs` |
| AC-E9.F3.2 | A broken file in the load box | The owner presses "Import as a new shop" | No shop is added, the owner is back in the shop that was open, and the reason for the refusal stays on screen | `tests/shops.mjs` |
| AC-E9.F3.3 | Two shops | A file is loaded with the plain "Load shop" button | It replaces only the current shop; the other shop keeps its ledger | `tests/shops.mjs` |

### E9.F4 The shop profile

As the owner, I want my name, address and logo on what I hand to customers so that a statement looks like it comes from my shop.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E9.F4.1 | "Shop profile" in Shop settings | The owner fills "Owner name" and "Shop address" and presses "Save profile" | "Profile saved." shows and the values are still there after the page is opened again | `tests/profile.mjs` |
| AC-E9.F4.2 | "Shop logo" | The owner chooses a picture | The picture is made small, "Logo saved." shows, and the logo appears in the top bar | `tests/profile.mjs` |
| AC-E9.F4.3 | A profile is saved | A customer's "Statement (PDF)" is made | The statement carries the logo, the owner's name and the address | `tests/profile.mjs` |
| AC-E9.F4.4 | A profile is saved | "Backup file (JSON)" is pressed, and the file is loaded back | The backup holds the owner, address and logo, and loading it brings them back | `tests/profile.mjs` |
| AC-E9.F4.5 | A logo is set | The owner presses "Remove logo" | "Logo removed." shows and the top bar has no logo | `tests/profile.mjs` |
| AC-E9.F4.6 | A file that is not a picture is chosen as the logo | The app reads it | "This file is not a picture the browser can read." shows and nothing is saved | checked by hand |

## E10 Languages and names

Goal: the whole app reads in any of twelve languages, with the same figures in each, and a customer's name is shown in the script of the language chosen.

### E10.F1 Twelve languages

As a shop owner, I want the app in my own language so that I and my staff can read it.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E10.F1.1 | The language box in the top bar | It is opened | It lists twelve languages: English, Hindi, Telugu, Bengali, Marathi, Tamil, Gujarati, Kannada, Malayalam, Punjabi, Odia and Urdu | `tests/langs.mjs` |
| AC-E10.F1.2 | Any of the twelve | The owner picks it | Headings, buttons, messages and the note shortcuts change to that language; the total, every balance and every reminder link stay the same | `tests/langs.mjs`, `tests/check.mjs` |
| AC-E10.F1.3 | A language was chosen | The page is opened again | The app opens in that language | `tests/langs.mjs` |
| AC-E10.F1.4 | The twelve language files | They are compared with the English one | Each has the same texts, the same placeholders, nothing empty, and is written in its own script | `tests/i18n-check.cjs` |
| AC-E10.F1.5 | Any language, at five widths | The overview and a customer page are shown | Nothing scrolls sideways, no label is cut off and no text breaks into a column of letters | `tests/layout.mjs` |

### E10.F2 A language loading for the first time

As a shop owner on a slow connection, I want only my language fetched so that the app stays quick.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E10.F2.1 | A visit in English, Hindi or Telugu | The app opens | No language file is fetched; these three are part of the app | `tests/langs.mjs` |
| AC-E10.F2.2 | One of the nine other languages | It is chosen for the first time | Its file is fetched once from the site and the page changes to it | `tests/langs.mjs` |
| AC-E10.F2.3 | A language whose file cannot be fetched | It is chosen | The app stays in English and says "That language could not be loaded. Showing English." | `tests/langs.mjs` |

### E10.F3 Names in the script of the language

As a shop owner, I want each customer's name in my script so that I recognise it at a glance.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E10.F3.1 | Hindi or Telugu is chosen | A customer is listed | The name is followed by its spelling in that script in brackets; a spelling the owner saved under "Customer details" is used first | `tests/names.mjs` |
| AC-E10.F3.2 | One of the nine other languages is chosen | A customer is listed | The name is followed by its spelling in that language's script; Marathi uses the Hindi spelling | `tests/names.mjs` |
| AC-E10.F3.3 | English is chosen | A customer is listed | The name stands alone, with no bracket | `tests/names.mjs` |

### E10.F4 Native digits

As a shop owner, I want figures in Hindi or Telugu digits if I read those more easily.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E10.F4.1 | Hindi or Telugu is chosen | Shop settings is opened | The tick box "Show numbers in Hindi or Telugu digits (१२३, ౧౨౩)" is shown; in English and in the nine other languages it is hidden | checked by hand |
| AC-E10.F4.2 | The box is ticked and saved, in Hindi or Telugu | Amounts, counts and dates are drawn | They use Devanagari or Telugu digits, and the choice is kept with the shop | checked by hand |
| AC-E10.F4.3 | Native digits are on | A tester reads the balance attribute of a customer row | It still holds the plain figure in Latin digits | checked by hand |

### E10.F5 Urdu reads right to left

As an Urdu reader, I want the page laid out from the right so that it reads naturally.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E10.F5.1 | Urdu is chosen | The page is drawn | The whole page runs right to left; in every other language it runs left to right | `tests/desk-ledger.mjs` |
| AC-E10.F5.2 | Urdu on a wide screen | The overview is shown | The right panel sits on the left of the customer list and the "+" menu lines up with the right edge of the quick-entry pill | `tests/desk-ledger.mjs` |
| AC-E10.F5.3 | Urdu | The total and the balances are shown | The figures keep their left-to-right order | checked by hand |
| AC-E10.F5.4 | Urdu on a phone | The overview is shown | Nothing scrolls sideways | `tests/phone-ledger.mjs` |

### E10.F6 Quick-entry words and the "+" menu in each language

As a shop owner, I want to type an entry and open the menu in my language.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E10.F6.1 | One of the nine other languages is chosen | The owner types the example shown in the quick-entry box, in that language | The form fills with the customer, 500 and "You gave (credit)" | `tests/langs.mjs` |
| AC-E10.F6.2 | Any of the twelve languages | The "+" menu is opened | All fifteen items, and the names of the "+" and send buttons, are in that language | `tests/local-assets.mjs` |
| AC-E10.F6.3 | The language is changed back to English | The "+" menu is opened | The labels read as they did at the start | `tests/local-assets.mjs` |

## E11 Layout and themes

Goal: on a phone the customer list has the screen and an entry is one line at the bottom; on a desktop the same list is flanked by what needs attention; both read well in light and dark.

### E11.F1 The phone layout

As a shop owner at the counter, I want my customers on the first screen of my phone so that I can find one with a thumb.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E11.F1.1 | A screen up to 767 px wide, a shop with customers | The overview opens | "Total outstanding" sits in a compact card at the top with one strip under it, and the customer list follows on the first screen | `tests/phone-ledger.mjs` |
| AC-E11.F1.2 | The strip under the total | It is read | It shows three figures, "Customers", "Overdue" and "Collected, last 30 days", and a "…" button named "Show the full overview" that unfolds the four summary tiles | `tests/review-fixes.mjs` |
| AC-E11.F1.3 | Phones of 360, 375, 390 and 412 px, light and dark | Each view is shown | Nothing scrolls sideways | `tests/phone-ledger.mjs`, `tests/phone-import.mjs` |
| AC-E11.F1.4 | A phone | The top bar is shown | It keeps the brand, the language button and the light or dark button; the language box is drawn as an icon button | checked by hand |

### E11.F2 The quick-entry pill

As a shop owner, I want to record a sale in one line so that the queue does not wait.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E11.F2.1 | A phone | The overview is shown | A pill is fixed at the bottom with a "+" button, the box "Quick entry: type or speak it" and a send arrow; the last customer rows are not hidden under it | `tests/phone-ledger.mjs` |
| AC-E11.F2.2 | "Sunita 500 udhaar" is typed in the pill | The send arrow is pressed | The entry is filled and saved in one step and the customer's balance goes up by 500 | `tests/phone-ledger.mjs` |
| AC-E11.F2.3 | The box is empty | The send arrow is pressed | Nothing is saved; the cursor goes to the box | checked by hand |
| AC-E11.F2.4 | A screen 768 px or wider | The overview is shown | The same pill sits at the bottom of the screen, as wide as the customer list and lined up with it | `tests/desk-ledger.mjs` |

### E11.F3 The "+" menu

As a shop owner, I want everything else one tap away so that the first screen can stay simple.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E11.F3.1 | The "+" button of the pill | It is pressed | A menu "All actions" opens with fifteen items in this order: Record an entry, Add a customer, Remind all overdue, Import a shop, Export CSV, Statement, Shops, Shop profile, Language, Light or dark, Customers, Overdue, Recent entries, Shop file, Shop settings | `tests/local-assets.mjs` |
| AC-E11.F3.2 | A phone | The menu is open | It is a sheet across the bottom of the screen, and a tap outside it closes it | `tests/phone-ledger.mjs` |
| AC-E11.F3.3 | A desktop | The menu is open | It is a small panel just above the pill, inside the screen and lined up with the start of the pill | `tests/desk-ledger.mjs` |
| AC-E11.F3.4 | The menu is open | Escape is pressed | It closes and the focus returns to the "+" button; arrow keys, Home and End move between the items | `tests/desk-ledger.mjs` |
| AC-E11.F3.5 | The menu is open | An item is chosen | The menu closes and the matching view or action opens: for example "Add a customer" opens the "Add a customer" view | `tests/phone-ledger.mjs` |

### E11.F4 The desktop layout and the right panel

As a shop owner at the counter computer, I want the list, the overdue customers and the latest entries side by side.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E11.F4.1 | A screen 768 px or wider | The app is shown | The left navigation lists Overview, Record, Customers, Overdue, Recent entries, Add a customer, Shop file and Shop settings, and the strip sits beside the total | `tests/desk-ledger.mjs`, `tests/nav.mjs` |
| AC-E11.F4.2 | A screen 1200 px or wider, a shop with customers | The overview is shown | A right panel about 340 px wide holds "Record an entry" with the full form, then Overdue and Recent entries; below 1200 px there is no panel and the two cards are back in the page | `tests/desk-ledger.mjs` |
| AC-E11.F4.3 | The right panel | The owner types in the filter box of Overdue or of Recent entries | Only the rows holding the typed text stay in that list | `tests/desk-ledger.mjs` |
| AC-E11.F4.4 | The right panel with a long list | The page is scrolled | The panel has no scroll box of its own; it grows with the page | `tests/desk-ledger.mjs` |
| AC-E11.F4.5 | Widths from 375 to 2560 px | The overview, Record and a customer page are shown | Nothing scrolls sideways | `tests/final.mjs`, `tests/layout.mjs` |

### E11.F5 Light and dark

As a shop owner, I want a light screen in a bright shop and a dark one at night.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E11.F5.1 | No theme was ever chosen | The app opens | It follows the device's light or dark setting | checked by hand |
| AC-E11.F5.2 | The light or dark button in the top bar | It is pressed | The theme switches, the button says which theme is on ("Dark theme on" or "Light theme on"), and the choice is kept for the next visit | `tests/assist.mjs` |
| AC-E11.F5.3 | Either theme | Text on the overview and on a customer page is measured | Each text and its background meet a contrast of 4.5 to 1, or 3 to 1 for large text | `tests/check.mjs` |
| AC-E11.F5.4 | Either theme | States reached by acting are measured: messages, the edit mode, the QR card, a pressed button | They meet the same contrast | `tests/contrast.mjs` |

### E11.F6 The brand look and tap sizes

As a first-time visitor, I want a finished, readable product on any screen.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E11.F6.1 | 375 px and 1280 px, light and dark | The app is shown | Headings are set in Fraunces and body text in Inter; the top bar and the total block are Deep Navy and the main buttons are Signal Amber | `tests/brand.mjs` |
| AC-E11.F6.2 | The same four cases | Text sizes are measured | No text is smaller than 12 px | `tests/brand.mjs` |
| AC-E11.F6.3 | The same four cases | Buttons, links and fields are measured | Each is at least 44 px tall | `tests/brand.mjs` |
| AC-E11.F6.4 | A phone, with the profile and several shops in use, in English, Hindi and Telugu, light and dark | Each view is shown | Nothing scrolls sideways | `tests/addons-width.mjs` |

## E12 Offline and install

Goal: after one visit the app opens and works with no signal, and it can be put on the home screen.

### E12.F1 The first visit caches the app

As a shop owner with a weak signal, I want one visit to be enough.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E12.F1.1 | A first visit on the live link | The page has loaded | The app's own files are kept in the browser cache named `hisaab-v22`; the list is under [Offline](../ARCHITECTURE.md#offline) | `tests/offline.mjs` |
| AC-E12.F1.2 | A new version is released | The owner opens the app with a signal | The new files are used at once, because the network is tried first; caches under an older name are deleted | checked by hand |

### E12.F2 What works offline

As a shop owner, I want to record a sale where there is no signal.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E12.F2.1 | One visit with a signal | The signal is lost and the page is opened again | The ledger opens with its total and its customers | `tests/offline.mjs` |
| AC-E12.F2.2 | No signal | The owner opens "UPI QR code" on a customer who owes | The QR code is drawn | `tests/offline.mjs` |
| AC-E12.F2.3 | No signal | The owner chooses a payment screenshot | The picture is attached to the entry; the amount is not read from it, because the reader comes from another site | checked by hand |
| AC-E12.F2.4 | No signal | The owner picks one of the nine other languages that was never used on this device | The app stays in English; a language used once before with a signal still works | checked by hand |

### E12.F3 Install

As a shop owner, I want Hisaab on my home screen like any other app.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E12.F3.1 | A browser that offers install | The owner adds the app to the home screen | It is installed as "Hisaab", with the app icon, and opens in its own window without the browser's bars | checked by hand |

### E12.F4 Fonts and the QR library come from the site

As a shop owner, I want nothing fetched from other sites when I simply open my ledger.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E12.F4.1 | A plain visit | The page loads | Every request goes to the site itself; no other host is asked for anything | `tests/local-assets.mjs` |
| AC-E12.F4.2 | A plain visit | The fonts are loaded | The font files come from the site, under the family names Inter and Fraunces | `tests/local-assets.mjs` |
| AC-E12.F4.3 | A customer who owes | "UPI QR code" is opened | The QR code is drawn by the library served from the site, and no other host is asked | `tests/local-assets.mjs` |
| AC-E12.F4.4 | Any language | The language is changed | No other host is asked for anything | `tests/local-assets.mjs` |

## E13 Checks on what is typed

Goal: the app never looks as if it kept something it did not keep, and it holds back an entry that cannot be right.

### E13.F1 Storage that is full or blocked

As a shop owner, I want to be told when a change was not kept so that I do not walk away believing it was.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E13.F1.1 | The browser's storage is full | The owner saves an entry or loads a shop | A notice stays at the top: "This change was NOT kept: the storage on this phone is full. Export a backup file, then remove some screenshots, and make the change again.", with a "Backup file (JSON)" button; no "Saved." or "Shop loaded." is left on screen | `tests/guard.mjs` |
| AC-E13.F1.2 | The browser blocks storage, as some private windows do | The app opens | A notice stays at the top: "Nothing can be kept on this device in this browser mode: the browser is blocking storage. Open Hisaab in a normal window (not private), or export a backup file before you close this page." The sample shop can still be loaded and looked at | `tests/guard.mjs` |
| AC-E13.F1.3 | Either case | The page is used | No error is logged | `tests/guard.mjs` |

### E13.F2 Whole rupees

As a shop owner, I want amounts in whole rupees so that balances are clean figures.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E13.F2.1 | An amount with paise is typed in the entry form | "Save entry" is pressed | The entry is not saved; "Amounts are in whole rupees, without paise. This was not saved yet." shows with a button "Use Rs {n}" holding the nearest rupee | `tests/rupees.mjs` |
| AC-E13.F2.2 | A shop that already holds amounts with paise | The overview is shown | "{n} stored amounts have paise. They are shown as stored." shows with "Round them to whole rupees"; after a press, "{n} amounts were rounded to whole rupees." shows with "Undo" | `tests/rupees.mjs` |

### E13.F3 Amount, date and note limits

As a shop owner, I want a slip of the finger caught before it lands in the ledger.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E13.F3.1 | An amount above 1,00,00,000 | "Save entry" is pressed | "That amount looks too large. One entry can be up to ₹1,00,00,000." shows and nothing is saved | `tests/final.mjs` |
| AC-E13.F3.2 | The "Date" field of the entry form | The owner opens the date picker | The latest day it offers is today | checked by hand |
| AC-E13.F3.3 | An entry is being edited and its date is cleared | "Update entry" is pressed | The edit stays open with "A date is needed. Choose the date, then save." and the entry keeps its old date | `tests/guard.mjs` |
| AC-E13.F3.4 | The "Note" field | The owner types | It takes at most 80 characters; a note in a loaded file is cut at 200 | checked by hand |
| AC-E13.F3.5 | A payment screenshot of any size is attached | The entry is saved | The picture is stored no larger than 600 px on its longest side | `tests/guard.mjs` |

Known limit: the storage, date and whole-rupee messages of this epic exist in English, Hindi and Telugu; in the nine other languages they show in English.

## E14 Links and robustness

Goal: every view has its own address, a tester can fix the date and read the figures from the page, and the app stays quiet and usable under a big shop, a keyboard and a screen reader.

### E14.F1 An address for every view

As a shop owner, I want Back to work and a link to open the right view.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E14.F1.1 | The address ends in nothing or `#/` | The app opens | The overview shows | `tests/nav.mjs` |
| AC-E14.F1.2 | Any item of the navigation, on a desktop or in the phone's bottom bar and "More" sheet | It is pressed | The address becomes `#/s/record`, `#/s/customers`, `#/s/overdue`, `#/s/recent`, `#/s/add`, `#/s/data` or `#/s/settings`, and that view opens at the top of the page, quickly | `tests/nav.mjs` |
| AC-E14.F1.3 | An address `#/c/` followed by a customer's 10-digit phone | It is opened | That customer's page shows; a number no customer has opens the overview | checked by hand |
| AC-E14.F1.4 | A navigation item is pressed | A screen reader is listening | The name of the view that opened is announced, and "Overview" is marked as the current item on the overview | `tests/assist.mjs` |

### E14.F2 The `?today=` option

As a tester, I want to fix today's date so that figures that depend on the date can be checked.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E14.F2.1 | The address carries `?today=` with a date written YYYY-MM-DD | The app runs | That date is today for the overdue days, the two 30-day figures and the default date of a new entry, and it is shown under the total as the date the figures are for | `tests/check.mjs` |
| AC-E14.F2.2 | `?today=` is missing, or is not a date written that way | The app runs | The device's date is used, with no message | checked by hand |
| AC-E14.F2.3 | Any other option after `?` in the app's address | The app runs | It is ignored; the pay page reads its own details (shop, customer, UPI ID, amount, and `stay=1`) from its own address | checked by hand |

### E14.F3 What a tester reads from the page

As a tester, I want fixed hooks in the page so that a script can read the ledger.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E14.F3.1 | Any shop | The page is read | `#total-outstanding` holds the total; each customer row is `[data-customer="<phone>"]` and holds `[data-balance]` with the plain balance | `tests/check.mjs`, `tests/brief.mjs` |
| AC-E14.F3.2 | Any shop | The page is read | Each customer row has a reminder link `a[data-remind]` to WhatsApp | `tests/check.mjs` |
| AC-E14.F3.3 | A shop file | It is put in `#import-box` and `#import-go` is pressed | The shop loads; a file given to `#shot-input` shows on the saved entry as `img[data-shot]` | `tests/check.mjs` |
| AC-E14.F3.4 | The phone layout, the desktop layout, the profile and several shops | Each is in use | The same hooks are still in place and hold the same figures | `tests/phone-ledger.mjs`, `tests/desk-ledger.mjs`, `tests/brand.mjs` |
| AC-E14.F3.5 | A script calls `window.hisaabParseQuick(text)` | It returns | It gives the amount, the kind, the matching phones and the note, without touching the form | `tests/quick.mjs` |

### E14.F4 A hidden shop and a big shop

As a judge, I want the figures right for a shop the makers never saw.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E14.F4.1 | A shop generated from a seed, with advances and a customer with no entries | It is loaded | Every balance and the total match the figures worked out apart from the app, and the checker gives its score estimate | `tests/check.mjs` |
| AC-E14.F4.2 | A big shop on a desktop | The overview is shown | Nothing scrolls sideways, the pill stays at the bottom and the customer list stays usable | `tests/desk-ledger.mjs` |

### E14.F5 No pop-up dialogs, no errors

As a shop owner, I want an app that never stalls on a pop-up.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E14.F5.1 | A full run of adding, recording, editing, deleting, loading and exporting | Every action is taken | No alert, confirm or prompt dialog opens; a delete or a load is taken back with "Undo" | `tests/check.mjs`, `tests/brief.mjs` |
| AC-E14.F5.2 | The same run | Every action is taken | No page error and no console error is logged | `tests/check.mjs`, `tests/final.mjs`, `tests/gaps.mjs` |
| AC-E14.F5.3 | A name, note or shop name in a loaded file holds HTML | It is shown | It is shown as plain text | checked by hand |

### E14.F6 Keyboard and screen reader basics

As someone who does not use a mouse or cannot see the screen, I want to reach and understand every control.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E14.F6.1 | The page has just opened | Tab is pressed, then Enter on "Skip to the ledger" | The focus moves to the main content | `tests/assist.mjs` |
| AC-E14.F6.2 | The "+" button, the send arrow and the "…" button | A screen reader reads them | They are named "More actions", "Send and save the entry" and "Show the full overview" | `tests/local-assets.mjs` |
| AC-E14.F6.3 | A quick entry is sent from the pill | The result message fades from view | Its text stays available to a screen reader | checked by hand |

### E14.F7 The maker's mark

As a visitor, I want to know who built the app.

| ID | Given | When | Then | Test |
| --- | --- | --- | --- | --- |
| AC-E14.F7.1 | The brand mark in the top bar | It is pressed | `https://sai4b.com` opens in a new tab; the link is named "Smart AI for Business (opens in a new tab)" | checked by hand |
| AC-E14.F7.2 | The name "Hisaab" beside the mark | It is pressed | The overview opens | checked by hand |
| AC-E14.F7.3 | The foot of the left navigation on a desktop | It is read | It shows "Built by Smart AI for Business" as a link to the same site | checked by hand |
