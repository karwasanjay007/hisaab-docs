# Hisaab against the market: final industry critique

Reviewed 20 Nov 2026 on the live site https://hisaab-hazel.vercel.app/?today=2026-11-15 (desktop 1366 px and
phone 375 px, Playwright, no console errors, no dialogs) and the source (index.html, app.js). Screenshots
(JPG): `the session scratch folder\final\industry\`
(desk-home, desk-s-customers, desk-s-overdue, desk-c-9000000102, and the same four as phone-*).

Nothing in this report asks to change the tester hooks (`#total-outstanding`, `[data-customer]`,
`[data-balance]`, `a[data-remind]` with `https://wa.me/91...`, `#import-box`, `#import-go`, `#shot-input`,
`img[data-shot]`, `?today=`). Every proposal below keeps them as they are, and keeps `upi://pay?...` in the
reminder because the bonus list asks for it.

## The strongest objection

Hisaab is a form and a dashboard, not a khata. The leaders are built around one loop: open a customer, press
a big red "You gave" or green "You got", type the amount, done. Ok Khata's store listing sells exactly that
("debit (you gave) and credit (you got)" plus a "smart calculator" that saves to the ledger). Hisaab instead
leads with a generic "Record credit or payment" form whose first field is a drop-down of every customer, and
whose two choices are accounting words: "Credit (gave goods)" and "Payment (received)". "Credit" means the
opposite thing to anyone who has seen a bank statement, which is why the market says "gave" and "got".

The second objection: the Overdue list has no signal. With the sample shop it lists all 8 customers who owe,
and the nav badge reads 8, the same as the Customers badge. "Overdue" here means "owes anything, sorted by
days since last payment". Khatabook has a collection date per customer with Pending, Today and Upcoming views
and an automatic reminder the day before; myBillBook reminds three days before a due date and on it. Hisaab
has no due date at all, so "overdue" cannot be true or false.

## 1. Gaps worth closing in a few hours, ranked by what a judge notices in a few minutes

### 1.1 "You gave / You got" on the customer page (about 1.5 hours)
- Where: customer page (`#/c/<phone>`), directly under the balance block, replacing the segmented
  Credit/Payment control as the first thing in the entry card.
- What: two large buttons side by side, full card width on a phone:
  - red: "You gave ₹" (Hindi "आपने दिया ₹", Telugu "మీరు ఇచ్చారు ₹")
  - green: "You got ₹" (Hindi "आपको मिला ₹", Telugu "మీకు వచ్చింది ₹")
  Pressing one sets the existing radio to credit or payment (keep the radios, hidden or small, so nothing the
  checker uses moves), focuses the amount and changes the Save button text to "Save: gave ₹1,500" or
  "Save: got ₹950" as the amount is typed.
- Relabel everywhere else to match: the home form options become "Gave goods (udhaar)" and "Got payment";
  the entry pills "CREDIT" and "PAYMENT" become "Gave" and "Got".
- Amount box accepts a sum: "120+45+2*30" shows "= ₹225" under the field and saves 225 (a 20-line parser
  for digits, + - * and brackets; never `eval`). This is the "calculator" the market apps advertise and the way
  a shopkeeper reads a bill aloud.

### 1.2 Collection date, and an Overdue list that means something (about 2 hours)
- Where: customer page, a line under "owes · Last paid: 4 Oct 2026": "Collect by: not set" with three chips
  "Next week", "Next month", "Pick a date" (Khatabook's own presets).
- Store `collectBy` per customer (import ignores it if absent, export includes it).
- Overdue view: keep the current list and its sort by days since last payment (that is the bonus test), and
  add a strip above it with three counts: "Due today 1", "Past collect-by date 2", "Upcoming 3". When no
  dates are set, a customer counts as overdue only after 30 days without paying; say so in the subtitle:
  "Owing and no payment for 30 days or more, or past the collect-by date".
- Nav badge on Overdue counts only those, so it stops reading 8 of 8. With the sample shop on 15 Nov this
  gives 4 (Prakash 61, Anjali 44, Kavitha 43, Sunita 42 days), which is a figure a judge believes.
- Customer rows past their date get a small red "Due 12 Nov" tag beside the name.

### 1.3 "Remind all overdue" (about 45 minutes)
- Vyapar offers a bulk reminder over WhatsApp and SMS; Khatabook has "Select all" for collection reminders.
- Where: top of the Overdue view, a green button "Send reminders (4)".
- Browsers block several pop-ups at once, so make it a stepper: a panel shows "1 of 4: Prakash Rao, ₹230"
  with "Open WhatsApp" (the normal `wa.me` link) and "Next". Mark each as "Reminded today" on the row, and
  store the date so the customer page shows "Last reminded: 15 Nov".

### 1.4 Phone home order (about 30 minutes)
- The phone home is 5,231 px tall: hero, four KPI tiles, the full record form, customers, overdue, recent
  entries, add customer, the JSON box and settings, all stacked. The leaders' phone home is the customer list.
- At 375 px: hero, then the KPIs as one row that scrolls sideways, then Customers with search. The record
  form stays on the floating "Record" button and its own view (`#/s/record`), and is reached by tapping a
  customer. Shop file and settings belong only to "More". Keep the DOM ids where they are if the checker
  needs them on the first view; reordering with CSS `order` is enough.

### 1.5 Opening balance when adding a customer (about 30 minutes)
- Every shop that switches has a paper notebook. Add-customer form, third field: "Already owes from your
  notebook (optional) ₹". If filled, save a credit entry dated today with the note "Opening balance (from
  notebook)". Then go straight to that customer's page, not back to the form.

### 1.6 Works offline and can be installed (about 30 minutes)
- `manifest.webmanifest` is linked, but there is no service worker (no `serviceWorker` in app.js), so the app
  is not installable as an app on Android and fails without a network. OkCredit's home page sells "Offline
  bhi kaam karta hai". A cache-first `sw.js` for the five static files closes it; add "Install Hisaab" under
  More when `beforeinstallprompt` fires.

### 1.7 Backup nudge (about 20 minutes)
- localStorage is the only copy; the leaders back up to their cloud. In Shop file and as a chip in the
  sidebar footer: "Last backup: never" (amber) or "Last backup: 12 Nov" (grey). After every 20 new entries
  since the last backup, a toast: "20 entries since your last backup. Save a backup file." with the button.

### 1.8 Credit limit per customer (optional, about 30 minutes)
- myBillBook and Vyapar set a credit limit per party. Customer details: "Udhaar limit ₹". When a "You gave"
  entry would pass it, the form shows in amber: "This takes Sunita Devi to ₹4,970, over her ₹4,000 limit."
  It warns, it does not block.

## 2. Creative differentiators (no backend, no API key)

### 2.1 A customer pay page that the reminder opens (the one to build)
- Problem it solves: WhatsApp turns `https://` text into a link but, as far as I know, not `upi://`, so the
  "Pay by UPI" link inside the reminder very likely arrives as plain text the customer cannot tap. Check this
  on a real phone before the deadline. OkCredit's reminders carry a tappable https payment link.
- Build `pay.html` on the same Vercel site. The reminder keeps the `upi://pay?...` link (bonus test) and adds
  a second line: "See your khata and pay: https://hisaab-hazel.vercel.app/pay.html#d=<data>".
- `<data>` is the shop name, UPI ID, customer name, balance and the last five entries, JSON then base64url, in
  the URL fragment (the fragment never reaches the server, so the privacy line stays true).
- The page shows: shop name, "Namaste Sunita Devi", "You owe ₹3,470", the last five entries with running
  balance, a large "Pay ₹3,470 by UPI" button (the `upi://` link, which works when tapped from a browser on
  a phone), and a UPI QR code of the same link for someone looking at it on a laptop (qrcodejs from cdnjs, or a
  small inline encoder). Same languages as the reminder.
- Why it stands out: it gives the customer the "both parties see the same record" that OkCredit sells as its
  main trust feature, with no server, and a judge can open it from the customer page with one click ("Preview
  what Sunita sees").

### 2.2 "Today's collection round"
- A card at the top of the home view: "Today's round: 3 calls, about ₹2,400 likely".
- Picks three customers by a simple score: balance times days since last payment, raised for customers past
  their collect-by date, lowered for anyone reminded in the last three days.
- Each line uses their own history, worked out from the entries: "Sunita Devi, ₹3,470. Usually pays about
  ₹500 every 9 days; last paid 42 days ago, which is unusual." The buttons are "Remind" and "Call"
  (`tel:+91...`).
- A line at the bottom: "Expected this week: ₹X", the sum of each customer's average payment if they keep to
  their usual gap. No ledger app in this group gives a payment habit per customer; it reads as insight, not
  as one more form, and it is plain arithmetic on data the app already has.

(Not recommended in the time left: voice entry through the Web Speech API. Hindi works in Chrome without a
key, but it fails in Firefox and in some embedded browsers, and a judge without a microphone sees a dead
button.)

## 3. Broken, or weaker than the leaders

1. Overdue counts every customer who owes (8 of 8; the badge equals the Customers badge). See 1.2.
2. The UPI link in the WhatsApp text is very likely not tappable (see 2.1). The receipt message after an entry
   has no pay link at all.
3. Destructive links everywhere: every row in Recent entries on the home view and on the customer page carries
   underlined "Edit" and "Delete". The leaders open an entry detail on tap, with edit and delete inside it.
   Make the whole row tappable and open a small sheet holding the details, the screenshot, Edit and Delete.
   Keep the undo.
4. Phone numbers are formatted in one place and not another: "+91 90000 00102" on the customer page,
   "9000000102" in the customer list and the record drop-down. Use the spaced form everywhere it is displayed
   (leave `data-customer` as the plain 10 digits).
5. The shop name in the phone header truncates to "Lakshm...", so a judge on a phone never sees whose ledger
   it is. On narrow screens put the shop name on its own line under the top bar, or drop the "Hisaab" word
   mark at 375 px and keep the icon.
6. The home record form starts with a drop-down of all customers. With 50 customers that is unusable. Make
   it a type-ahead (the existing search logic) with the five most recent customers as chips above it.
7. The reminder text is plain: "Namaste {name}, this is a reminder from {shop}. Your balance is Rs {amt}." It
   uses "Rs" while the app shows ₹. Add the last payment ("Last payment ₹950 on 4 Oct") and the pay-page
   link; keep "Rs" only if the checker needs it, and since the brief accepts either, ₹ is fine in the text.
8. No app lock. Khatabook and OkCredit protect the ledger on a shared shop phone. A 4-digit PIN stored as a
   hash in localStorage, off by default, is 30 minutes of work; it is lower in the ranking because a judge
   will not look for it.
9. Look and feel: the dark green hero, amber accent and type are cleaner than Khatabook's and OkCredit's dense
   screens, and that is an advantage. The weaker parts are the long stacked phone home (1.4) and the
   uppercase CREDIT and PAYMENT pills, which read as a bank statement rather than a khata.

## Order to do it in, if there are about four hours
1. 1.1 You gave / You got with the calculator (the biggest change to first impressions).
2. 1.2 collect-by date and the 30-day overdue rule, with the honest badge.
3. 2.1 pay page (fixes item 3.2 and is the standout feature).
4. 1.4 phone home order, 3.4 phone format, 3.5 shop name.
5. 1.6 service worker, 1.7 backup nudge, 1.5 opening balance.
6. If time is left: 2.2 collection round, 1.3 bulk reminders, 3.3 entry sheet.

Rerun `tests/check.mjs` against the live site after each release; none of the above needs a hook to move.

## Sources
- Khatabook collection date, Pending/Today/Upcoming, reminder SMS a day before, Select all, QR:
  https://khatabook.com/blog/use-khatabook-for-faster-returns
- Khatabook feature listings (reminders over SMS and WhatsApp, reports): https://www.capterra.in/software/197475/khatabook ,
  https://www.techjockey.com/detail/khatabook
- Khatabook cashbook: https://khatabook.com/blog/cash-register-by-khatabook
- OkCredit reminders with a one-tap payment link, any UPI app, automatic recording, free and premium plans:
  https://okcredit.in/payment-reminder-app ; offline claim and user count: https://okcredit.in ;
  shared record for both parties: https://okcredit.in/blog/how-is-okcredit-helping-msmes-with-online-payment-collection
- OkCredit record, remind, pay model: https://medium.com/okcredit/record-remind-and-payments-a-full-stack-model-that-ticks-all-the-right-boxes-for-merchants-7a465b01f418
- myBillBook reminders three days before and on the due date, one-click WhatsApp from the party list, daily
  summary: https://mybillbook.in/s/features/payment-reminders/
- Vyapar bulk reminders, editable template, party statement, credit limit and due dates:
  https://api1.vyaparapp.in/guides/how-to-send-payment-reminders-in-vyapar ,
  https://vyaparapp.in/free/small-business-accounting-software/accounts-receivable ,
  https://vyaparapp.in/blog/vyapar-app-features/
- Ok Khata "you gave / you got" and smart calculator: https://apps.apple.com/in/app/id1475938213
- Udhaar analogue (Pakistan): https://oscar.pk/blog/get-paid-on-time-with-oscar-udhaar

Not verified: that WhatsApp leaves `upi://` untappable (test on a phone), and Khatabook's paid bulk SMS and
IVR (third-party source only).
