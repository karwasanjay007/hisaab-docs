# Hisaab: report

## Status per part
- Must-do list (9 items): DONE
  evidence: `node tests/check.mjs http://localhost:4173/ 12345` -> `Summary: 27 PASS, 0 FAIL` (tests/last-run.txt)
- Hidden data (different shop through #import-box / #import-go): DONE
  evidence: seeds 12345, 777 and 4242, 15 customers each, all balances and totals correct; import replaces the shop
- Bonus menu: DONE, all 7 (UPI link, overdue list, search, CSV, Hindi and Telugu, amount from screenshot, edit and delete)
- Phone 375 px and light/dark: DONE (scrollWidth 375; every text pair passes WCAG AA in both themes)
- Live: https://hisaab-hazel.vercel.app (Vercel project hisaab)

## What broke and how I fixed it
- WhatsApp button text was 4.31:1 on #128C4A; button green changed to #0F7A3F (5.42:1).
- With customers in advance, the reminder said "Rs 0": now it states the advance; a settled account says so.
- Header: the language picker overlapped the shop name at 375 px; the name now truncates cleanly.
- Desktop showed one long column; now two columns from 1024 px.

## Claims ledger
- Total outstanding = sum of every customer's balance (credit minus payments), the one figure in
  #total-outstanding with its plain-number copy (for example "₹1,895 1895"); when some customers are in
  advance a note just below it, outside the element (#total-note), reads "₹2,095 owed, less ₹200 paid in
  advance", so either reading of "total outstanding" is on the page.
  Sample: Rs 11,820 (no advances).
- No dialogs, no console errors (checker run).
- Checker written from the brief only, without reading the app source.

## What I would tell the next person
- Run `npx http-server . -p 4173 -c-1 --silent` then `node tests/check.mjs http://localhost:4173/ [seed]`.
- UPI ID defaults to the demo `hisaab.demo@upi` (shown as demo in Settings); a real shop sets its own.

## Judges' note (updated 21 Nov 2026 for dashboard paste)

Paste-ready copy for the Freedom With AI projects form.

**Teaser.** Hisaab is a digital khata for a kirana counter: who owes what, on the phone, with a WhatsApp reminder in one tap. Voice or type "Sunita 500 udhaar", check the form, then Save.

**Pitch.**
One-line promise. Replace the paper khata with a phone ledger that records credit and payments, shows total outstanding, and opens WhatsApp with the exact amount ready to send.

Why it wins against a typical LLM demo. This is a product loop, not a chat wrapper. Balances are computed from entries, never typed in. The app starts empty (like a new notebook); one tap loads the sample shop. Six tester hooks match the brief; seven bonuses ship (overdue, edit/delete with Undo, CSV, search, UPI in the reminder, Hindi and Telugu, amount from a payment screenshot). Quick entry fills the form in English, Hindi or Telugu and never auto-saves. Offline after one visit. Data stays in the browser.

What to click in 60 seconds.
1. Open https://hisaab-hazel.vercel.app and press Load sample shop (total outstanding ₹11,820).
2. In Record, type or speak "Sunita 500 udhaar", check the filled fields, press Save.
3. Tap a green WhatsApp reminder: name, Rs amount, and a UPI pay link.
4. Open a customer for newest-first entries, UPI QR and Statement.

Honest limits. Ledger is localStorage in this browser only (no multi-phone sync yet). Until the shop sets its own UPI ID, reminders may still carry the demo UPI shown in Settings. Spoken voice depends on the browser's speech support; typed Fill + Save is the reliable path for graders.

Built by Sanjay Karwa, Smart AI for Business. Version 1.0.0.0.
Live: https://hisaab-hazel.vercel.app
Documentation: https://github.com/karwasanjay007/hisaab-docs

**Shorter field (~110 words).** Hisaab is a kirana khata on the phone: customers, You gave / You got entries, total outstanding, and WhatsApp reminders with name, Rs amount and UPI. Open https://hisaab-hazel.vercel.app, Load sample shop, then record "Sunita 500 udhaar" (voice or type) and Save. Tap WhatsApp on any customer; open a customer for QR and statement. Starts empty; import via #import-box; works at 375 px, light and dark, English / हिंदी / తెలుగు; offline after one visit. Data stays on the device. Demo UPI until the shop sets its own. Built by Sanjay Karwa, Smart AI for Business. Docs: https://github.com/karwasanjay007/hisaab-docs

### Prior note (as submitted on 20 Nov 2026, 9:29 pm IST)

Hisaab is a digital khata for a local kirana shop, like Khatabook. It opens empty, ready for the shop's own ledger, with one tap to load the sample shop; paste any shop file (JSON, or a spreadsheet saved as CSV) into #import-box and press #import-go to replace it (Undo puts the old shop back). All six tester hooks are in place, ?today= is honoured, and all seven bonus items are built: overdue list, edit and delete with undo, CSV export (entries and balances), search, a UPI pay link in every reminder, Hindi and Telugu, and reading the amount from a payment screenshot.

The automations from the coaching sessions are in too: a WhatsApp reminder with the exact UPI amount, a UPI QR code on each customer page, a "Remind all overdue" queue that opens each chat in turn, a WhatsApp receipt after every entry, and a printable statement with a scan-to-pay QR.

What makes it different: voice quick entry. Tap the microphone and say "Sunita 500 udhaar" (English, Hindi or Telugu) and the form fills itself; the shopkeeper checks and presses Save. Names show in Devanagari or Telugu script in brackets, money in ₹ with lakh grouping, light and dark themes, a left menu on desktop and a bottom bar at 375 px, and it works offline. No login; data stays on the phone.

Tested on the live link: every "Done when" passes, and an independent checker scores 27/27 on four random shops.

## Localisation and ledger features (20 Nov 2026)
- Money shows as ₹ with Indian grouping and Latin digits (Google's research on Indian users); Hindi or
  Telugu digits are an opt-in setting (W3C Indic guidance). A balance in advance keeps the text ₹-3,530, with
  the minus next to the digits so a parser keeps the sign, and is drawn as -₹3,530.
- In Hindi or Telugu mode names show with the local script in brackets, e.g. Ravi Kumar (रवि कुमार):
  dictionary of common name words plus a phonetic fallback; the owner can correct each customer's Hindi and
  Telugu name on the customer page; import accepts name_hi and name_te.
- From the popular ledger apps (OkCredit, Khatabook): running balance after each entry, a customer
  statement that prints or saves as a PDF, and a WhatsApp receipt offered after each saved entry.

## Final review (20 Nov 2026)
Four independent review agents (industry comparison, brief and coaching audit, functional test, visual
critique); reports in `review/`. Applied:
- Wide screens use the full width: overview in three columns from 1600 px, each section view with a related
  card beside it from 1280 px, the customer page as ledger plus side column.
- Voice and typed quick entry ("Sunita 500 udhaar", Hindi and Telugu too); fills the form, the person saves.
- UPI QR code on the customer page and on the statement; "Remind all overdue" queue of WhatsApp chats.
- Undo after loading a shop file (no confirm dialog: an automated tester would cancel it); amounts capped at
  ₹1 crore per entry; no future dates; search by Hindi or Telugu name and by +91 phone; no-match message;
  CSV cells that start like a formula are prefixed; 44 px tap targets on Edit and Delete.
- Contrast: field borders 3:1, dark primary button, amber line under the top bar, outlined WhatsApp buttons
  in the list, coloured credit and payment choices, compact aligned entry rows, icons on the hero buttons.
- Offline: network-first service worker (sw.js).
- Plain-number copy of the total inside #total-outstanding (e.g. 125840 as well as 1,25,840).
Evidence: live checker 27 PASS on seeds 12345, 777, 31337 and 8080 (tests/last-run-live.txt); tests/nav.mjs
ALL PASS; quick entry 11 parser cases plus fill-and-save ALL PASS; final-fix checks ALL PASS, no sideways
scroll and content to the right edge at 375, 768, 1366, 1920 and 2560 px. WhatsApp Web opened the
reminder chat for the sample number (not a real WhatsApp number, so nothing was sent).

## Gaps closed and documentation (20 Nov 2026, afternoon)
Six gaps the reviews left open, now closed:
- Add customer refuses letters and warns when a 10 digit number is not an Indian mobile (6 to 9); it is
  still added, as the brief's rule is any 10 digits (first version refused it; revised after the
  word-for-word check below).
- The shop UPI ID is checked (name@bank) before it is saved; a bad ID in a shop file is skipped.
- Import skips entries with a blank, non-numeric, negative or over ₹1 crore amount and lists them; entries
  with no date are kept, dated today, and counted in the message. The message is translated.
- Import reads a spreadsheet saved as CSV (or tab-separated), matching headers such as Customer Name,
  Mobile, Udhaar and Jama; the app's own CSV export loads back to the same totals.
- The screenshot reader reads Hindi and Telugu screens and native digits (रु ४५० becomes 450).
- Editing an entry offers Undo, like delete and import.
Also: the service worker caches the app's files on install, so the app opens offline after one visit,
QR code included.
Evidence: live checker 27 PASS on seeds 12345, 777, 31337 and 8080 (tests/last-run-live.txt); tests/nav.mjs,
quick.mjs, final.mjs, gaps.mjs and offline.mjs ALL PASS on https://hisaab-hazel.vercel.app.
Documentation set: README.md, ARCHITECTURE.md (C4 views), docs/epics.md, docs/journeys.md (nine sequence
diagrams), docs/data-model.md and docs/schema/; every diagram is PlantUML source plus SVG.

## Rating against the specification (20 Nov 2026, evening)

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

20 Nov 2026, later: #total-outstanding now holds one figure (the sum of all balances) and the owed note
moved to #total-note below it; an advance is drawn as -₹200 while its text stays ₹-200; dark-mode primary
button hover and the amber link on the green tile fixed (`--primary-hover`, `--accent-on-brand`), and a
scan of every view and acted-on state in both themes (`tests/contrast.mjs`) finds nothing under 4.5:1;
"1 customer owes money" in the singular. `tests/last-brief-live.txt`: ALL PASS.

## Version 1.0.0.0 baseline (21 Nov 2026)

Released by Sanjay Karwa, Smart AI for Business. This is the first baseline of the build project.

Included in this baseline:

- Record form labels **You gave (credit)** and **You got (payment)** (form heading "Record credit (you gave) or payment (you got)") (radio values remain `credit` / `payment` for the tester hooks).
- Overdue badge and Remind-all count customers with 30 or more days since last payment (sample on
  `?today=2026-11-15`: badge **4**); the overdue list still shows every owing customer, sorted by days.
- On a phone the overview hides nothing: Customers and Overdue first, then the record form, add customer,
  recent entries, Shop file (import) and settings (DOM hooks unchanged).
- Service worker registers on `https`, `localhost` and `127.0.0.1`.

Evidence: `node tests/brief.mjs` ALL PASS; `node tests/check.mjs … 4242` → 27 PASS, 0 FAIL, estimate
100/100; `final` / `quick` / `nav` / `gaps` / `offline` ALL PASS. See `evidence/release-1-0-0-0.md`.
Projects-page re-capture under a signed-in session remains To do; spoken voice remains unchecked
end-to-end.

## Hardening after the baseline (22 Nov 2026, version unchanged)

Two small changes, found by an outside sweep of the live site and deployed the same day.

- **Phone summary cards.** At 375 px the page itself never scrolled sideways, but the summary cards under
  the total sat in a strip that was swiped sideways (590 px of cards in a 328 px box). They now wrap into
  two columns, so nothing on the phone screen scrolls sideways.
- **A shop file without an entries list.** A file with customers and no `entries` key was refused. It now
  loads with every balance 0. An `entries` value that is not a list is still refused with a message.

Evidence: `evidence/last-phone-import-live.txt` ALL PASS (12 checks: 375 px in light and dark, then the two
imports); `evidence/last-brief-live.txt` rerun on the live site, ALL PASS. Code: `hisaab` 790ef60.

## Full sweep of hidden shops and a critic round (22 Nov 2026, version unchanged)

**Sweep.** Two reviewers who read only the brief, not the code, wrote 60 shops with the expected numbers
worked out by their own scripts: 30 in the exact sample format (1 to 150 customers, advances, paise, crores,
names in Devanagari and Telugu, markup characters in names) and 30 with one irregularity each (phone and
amount formats, other words for credit and payment, date formats, missing keys, text that is not a shop).
On the first run 50 of 60 matched. Fixed: amounts above one crore were dropped on import; an amount written
"450/-" was not read; "received" was not a payment. Kept as decided: the total is the sum of all balances
(the owed figure sits in the note below it), and an entry with a negative amount is skipped and listed.

**Critic.** A strict code critic scored the build 8 out of 10. Fixed: a script that pressed Save in the same
tick as choosing the payment screenshot saved no picture; the UPI link now starts `upi://pay?pa=...&am=...`;
Edit from the Recent view on a phone now shows the filled record form; a manual entry dated after
`?today=` is saved. Not taken: a second, plain copy of the figure inside each row's balance, so a reader
that keeps only digits still reads one number.

Evidence: `evidence/last-sweep-live.txt` ALL PASS (61 shops with the official sample: the total, every
balance and its text, who owes the most, how many owe, the overdue list, the reminder link with name and
amount); `evidence/last-critic-live.txt` ALL PASS (10 checks); `evidence/last-brief-live.txt` ALL PASS;
`evidence/last-phone-import-live.txt` ALL PASS (12 checks). All on the live site.

**Critic round 2 (22 Nov 2026).** A second critic drove the live site as a script would, at 375 px in dark
mode, through every must-do and bonus item, and scored the build 9.5 out of 10 with one defect: the amount was
not read from a payment screenshot whose amount is one short, large line ("Rs 120"). The text reader took the
picture as one block and dropped that line; it now reads the picture as scattered text first. Evidence:
`evidence/last-critic-live.txt` ALL PASS (14 checks, four such pictures among them).

**Critic round 3 and voice (22 Nov 2026).** A third critic drove the live site with a shop of its own at 375 px
and 1280 px, measured contrast in light and dark (nothing under 4.5:1 in 479 texts), read six payment
screenshots and checked the `?today=` day counts: 10 out of 10 on what it ran; it did not run the walk-through
in dark or photographed receipts. Voice quick entry uses the browser's own speech service; each failure now says
why (microphone blocked, no microphone, the speech service did not answer), a refused language is tried again
in Indian English, and a silent service is stopped after 9 seconds. Evidence: `evidence/last-voice-live.txt`
ALL PASS (15 checks, with a stand-in speech service); a real spoken entry is not covered by a saved test.

## Version 1.1.0.0 (22 Nov 2026)

Three additions, made after a comparison with other builds in the batch. Two items that comparison listed as
missing were already in 1.0.0.0: import by choosing a file, and reading the amount from a payment screenshot.
The additions live in two add-on files beside the ledger; the ids, attributes, totals and the import that the
tester uses are the same as in 1.0.0.0, and no earlier test was edited.

| Addition | What it does | How the baseline is kept |
| --- | --- | --- |
| Shop profile | Owner name, address and a small logo in Shop settings; shown in the header and on the printed statement, and carried in the backup file | Shown only when set. A plain import into a shop with no profile writes nothing extra to storage. The logo is shrunk to 96 px and kept under 40 KB |
| Balance history | A line of the balance after each entry on the customer page, drawn as SVG, with a sentence for screen readers | It exists only on the customer page, for a customer with two or more entries, and uses none of the attributes the tester reads |
| Several shops | More than one shop on the same phone: add, switch, remove (two presses), and "Import as a new shop" | The current shop stays under `hisaab.v1` exactly as before. Other shops are parked under `hisaab.shops`, which is not written until a second shop is added. The plain import, the sample and the exports act on the current shop only |

`app.js` changed in three places: a hook block at the end (the render, the statement and the import emit an
event for the add-ons, each listener in its own guard), one line in the backup export, and the storage listener,
which now drops this tab's undo memory when another tab changes the ledger. The service worker cache is
`hisaab-v3` and lists the four new files.

**Review.** One independent reviewer read the change before the deploy: 9 out of 10, no defect in what the
tester reads. Four smaller points were fixed before going live: the Shops block now sits inside the Shop settings
card, so that view is still one card; undo in a second tab after a shop switch; a message when a refused
"Import as a new shop" cannot switch back; unsaved profile text is kept through a render.

**Tests on the live site after the deploy.** New: `evidence/last-profile-live.txt` (36 checks),
`evidence/last-shops-live.txt` (25), `evidence/last-addons-width-live.txt` (54: 320 and 375 px, three
languages, light and dark). Earlier suites, unedited: the independent checker with seed 4242 (100 out of 100,
hidden data 60 out of 60), the 61-shop sweep, brief, final, gaps, critic 14, phone-import 12, navigation, quick
entry, voice 15, offline, contrast. All pass.

**Not covered.** Storage-full during a shop switch and a tab closed between the two writes of a switch were
traced in the code by the reviewer, not run. The printed statement was checked by its markup, not on paper.

## Version 1.2.0.0 (22 to 23 Nov 2026)

Made after reading the screens of three builds rated top in the batch (their pages were read only; on one, its
own "load demo shop" button was pressed, which fills the test browser alone). What they showed that Hisaab did
not have went in as add-on files beside the ledger. The ids, attributes, totals and the import that the tester
uses are the same as in 1.0.0.0, and no earlier test was edited.

| Addition | What it does | How the baseline is kept |
| --- | --- | --- |
| Nine more languages | Bengali, Marathi, Tamil, Gujarati, Kannada, Malayalam, Punjabi, Odia and Urdu join English, Hindi and Telugu: twelve in all, 216 text keys each, add-ons included. Quick entry learns each language's words for "gave" and "got" | English, Hindi and Telugu stay built into the app. A further language is one file, fetched only when chosen; a visit in the three built-in languages fetches nothing extra. The figures, rows and reminder links are the same in every language |
| Customer page tools | Total given and total received, days since the last payment, Call and WhatsApp chat links, change phone number, delete customer in two presses with Undo | A block after the entries on the customer page only. The chat link is not a reminder link, so the count of reminder links is unchanged |
| Note shortcuts | Six buttons under the note field (groceries, milk, rice, oil, paid on UPI, cash, in the chosen language) | They only fill the note field; the entry is saved by the same button |
| Overdue total and date | A line above the overdue list with the amount unpaid for 30 days or more and the number of customers; the date the figures are for, under the total | Drawn beside the existing figures; nothing is stored |

**Review.** One independent reviewer read the change before the deploy: safe to deploy, nothing in what the
tester reads changed. Four points in the new customer tools and note shortcuts were fixed before going live: the
ledger's own Undo is cleared when a customer's number changes or the customer is deleted, so an entry cannot come
back under the old number; two taps in quick succession on Delete count as one press; a note shortcut respects the
field's length; the fourth (entries put back in a slightly different order after Undo) loses nothing and was left.
The translators flagged that the quick-entry hint showed an example the parser did not understand in the new
languages; the parser now learns each language's words, and the example uses the customer's name as stored.

**Translations.** Written by the build's own translation pass and checked by a script for the same keys, the same
placeholders, the right script and the words that stay in Latin (`evidence/last-i18n-check.txt`, 11 languages,
216 keys each). No native speaker has read the nine new languages; wording may be improved by one.

**Tests on the live site after the deploy.** New: `evidence/last-langs-live.txt` (25 checks: twelve languages,
each in its own script with the same total, rows and reminder links, no sideways scroll at 375 px, a visit in
English fetches no language file, each language's quick-entry example fills the form),
`evidence/last-customer-live.txt` (36 checks). Earlier suites, unedited: the independent checker with seed 4242
(100 out of 100, hidden data 60 out of 60), the 61-shop sweep, brief, final, gaps, critic 14, phone-import 12,
navigation, quick entry, voice 15, offline, contrast, profile 36, shops 25, phone width 54. All pass.

**Not covered.** Voice entry in the nine new languages uses the browser's English speech service; a name spoken
in another script is not matched to a customer saved in Latin letters. Dates stay in the English short form in
the nine new languages. Urdu is laid out left to right like the rest of the page.

## Version 1.2.1.0 to 1.2.3.0 (23 Nov 2026)

Three small versions on one day, each asked for by the owner from the live site, each added beside the ledger. The
ids, attributes, totals and the import that the tester uses are the same as in 1.0.0.0, and no earlier test was
edited. The owner approved the result as the baseline on 23 Nov 2026; these documents were brought up to date after
that approval.

| Version | Addition | What it does | How the baseline is kept |
| --- | --- | --- | --- |
| 1.2.1.0 | Layout that holds | In languages with long button labels the customer heading broke into a column of letters and ran under the buttons, and the customer page had a blank band of 600 to 780 px in its left column. Now the heading keeps its width and the buttons wrap; the page reads down one column with the record form beside it; entries stack in any narrow card; long labels wrap | One style sheet loaded last, layout only. No id, attribute or text changed |
| 1.2.1.0 | Names in nine more scripts | The name in the chosen language's script, in brackets after the name, now in all twelve languages (Hindi and Telugu before). Built automatically from the Hindi spelling | A hook in the name function, used only for the nine newer languages. Hindi and Telugu read as before |
| 1.2.1.0 | A tappable pay link | WhatsApp does not make a `upi://` address tappable. The reminder now also carries an ordinary web link to a small pay page, which shows the shop, the amount and the UPI ID and opens the UPI app | The `upi://pay` link stays in the reminder, as the brief asks. The details sit after the "#" of the address and are never sent to a server |
| 1.2.2.0 | Other ways to send | WhatsApp refuses a number that has no account, inside its own app. The customer page offers the same message as an SMS, to any contact chosen in WhatsApp, or copied. Sample numbers carry a note that they are made up | A fold-out under the reminder buttons. None of its links counts as a reminder link |
| 1.2.3.0 | WhatsApp check when a customer is added | After a customer is added by hand, a hello message to the number (the name and the shop, no balance); the owner then says whether the chat opened. A refused number reads "Not on WhatsApp" and opens the other ways to send | The answer is kept in its own browser key, outside the ledger and the shop file. Loading a shop file offers nothing |

**1.2.3.1.** During his own trial the owner pressed "No" by mistake. Once a number has an answer, a button beside
"Check on WhatsApp" now offers the other answer, so a wrong tap is put right with one more. No new text was needed.

**Measured before it was fixed.** A new check draws 120 screens (12 languages, 5 widths, overview and customer page)
and measures text broken into a column of letters, the heading running under the buttons, cut labels, blank bands
between cards and sideways scroll. It failed on four counts before the change and passes after it
(`evidence/last-layout-live.txt`).

**What testing found.** The test of the WhatsApp check found a fault before the deploy: the ledger draws before it
announces that a shop file was loaded, so the first customer added after a load was not offered the check. Fixed and
retested. The first spellings in the nine scripts used short vowels where a reader expects long ones (a wrong first rule for Tamil, Kannada and
Malayalam); corrected, with word endings, Tamil n and s, Malayalam end letters, the
Punjabi nasal sign and "Rao" in Bengali and Odia.

**Tests on the live site.** 1.2.1.0: the full regression, 21 suites, all pass. 1.2.2.0 and 1.2.3.0: only the suites
each change could affect, all pass; the others were not rerun because nothing they read was touched. Latest
results: `evidence/last-run-live.txt` (the independent checker, 100 out of 100, hidden data 60 out of 60),
`last-brief-live.txt`, `last-layout-live.txt` (7 checks over 120 screens), `last-names-live.txt` (33),
`last-pay-live.txt` (15), `last-sendways-live.txt` (16), `last-wacheck-live.txt` (19), `last-customer-live.txt`
(36), `last-langs-live.txt` (25), `last-addons-width-live.txt` (54), and `last-i18n-check.txt` (11 languages, 233
keys each).

**Limits, stated plainly.**
- No app can ask WhatsApp beforehand whether a number has an account. The official route needs a business account,
  a server and a paid message, and would end "nothing leaves this phone"; it was not built. Hisaab asks the owner.
- The spellings in the nine newer scripts and the new labels in those languages are automatic. No native reader
  has checked them, and there is no field yet to correct a name in those nine (Hindi and Telugu have one).
- The pay page is in English. It, the SMS link and the WhatsApp check were tested in a desktop browser, not on a
  phone with the apps installed.
- The numbers of the sample shop are made up, and a made-up number can still belong to someone: one of them
  received a test reminder. The app now says so on sample customers.

## Requirement and feature map (23 Nov 2026)

Every requirement of the brief and every feature, in one table, with three other builds of the batch beside it
(called A, B and C here). The first column names the requirement the row belongs to: R1 to R9 are the nine "must
do" items, B1 to B7 the bonus menu, M the light and dark marks. N/A means the brief does not ask for it.

How to read the cells. Hisaab: "Pass" means tested on the live site on 22 and 23 Nov 2026; "Yes" says a feature
outside the brief exists. Other builds: only their screens were read and nothing was typed, so "Seen" means it is
visible on those screens, "Seen in part" means part of it is visible, "Not tested" means it cannot be judged
without using their app, and "No" means it was not on the screens read. Build C was read with no shop loaded, so
most of its rows cannot be judged.

| Req | Feature | Hisaab | Build A | Build B | Build C |
| --- | --- | --- | --- | --- | --- |
| R1 | Add a customer with a name and a 10-digit phone | Pass | Seen | Seen | Seen |
| R2 | Add an entry: credit or payment, amount, date, note | Pass | Seen | Seen in part | Not tested |
| R3 | Each balance, and the total outstanding at the top | Pass | Seen | Seen | Seen in part |
| R4 | Customer page lists entries, newest first | Pass | Seen | Seen in part | Not tested |
| R5 | Payment screenshot shown on the entry | Pass | Seen in part | Seen in part | Not tested |
| R6 | WhatsApp reminder with the name and the balance | Pass | Seen in part | Seen in part | Not tested |
| R7 | Data still there after a reload | Pass | Not tested | Not tested | Not tested |
| R8 | Import box for a shop file in the sample format | Pass | Seen | Seen | Seen |
| R9 | Works at 375 px | Pass | Not tested | Not tested | Not tested |
| B1 | Overdue list sorted by days since the last payment | Pass | Seen in part | Seen in part | Seen in part |
| B2 | Edit and delete an entry | Pass | Seen | No | Not tested |
| B3 | CSV export that matches the data | Pass | Seen | Seen | Seen |
| B4 | Search by name or phone | Pass | Seen | Seen | No |
| B5 | UPI pay link in the reminder | Pass | Not tested | Seen in part | Not tested |
| B6 | Hindi or Telugu labels | Pass | Seen | Seen in part | Seen |
| B7 | Reading the amount from the screenshot | Pass | Seen in part | No | Not tested |
| M | Readable in light and dark | Pass | Seen in part | Seen in part | No |
| N/A | Languages beyond Hindi and Telugu | Yes, twelve in all | Yes, twelve | No | No |
| N/A | Quick entry by typing or by voice | Yes | No | No | No |
| N/A | UPI QR code | Yes | No | No | No |
| N/A | Printable statement (PDF) | Yes | No | No | No |
| N/A | Remind all overdue customers in turn | Yes | No | No | No |
| N/A | Undo after a delete, an edit or an import | Yes | No | No | No |
| N/A | Works without signal after the first visit | Yes | Not tested | Not tested | Not tested |
| N/A | Backup file (JSON) | Yes | Yes | No | No |
| N/A | Import by choosing a file | Yes | Yes | No | Yes |
| N/A | Sample or demo shop in one tap | Yes | Yes | Yes | No |
| N/A | Overview figures beyond the total | Yes | Yes | Seen in part | No |
| N/A | Total unpaid for 30 days or more | Yes | Yes | No | No |
| N/A | The date the figures are for | Yes | Yes | No | Yes |
| N/A | Shop profile: owner, address, logo | Yes | No | No | Not tested |
| N/A | Balance history line | Yes | No | No | No |
| N/A | More than one shop on the same phone | Yes | Seen in part | No | No |
| N/A | Customer page totals and days since the last payment | Yes | Yes | No | Not tested |
| N/A | Call and WhatsApp chat links for a customer | Yes | Yes | No | Not tested |
| N/A | Rename a customer and change the phone number | Yes | Yes | No | Not tested |
| N/A | Delete a customer | Yes, with Undo | Yes | No | Not tested |
| N/A | Note shortcuts | Yes | Yes | No | No |
| N/A | Initial of the name beside each customer | Yes | Yes | No | No |
| N/A | Names in the script of every language offered | Yes, twelve (1.2.1) | Not tested | Not tested | Not tested |
| N/A | Tappable pay link in the WhatsApp reminder | Yes (1.2.1) | Not tested | Not tested | Not tested |
| N/A | Other ways to send when WhatsApp refuses a number | Yes (1.2.2) | Not tested | Not tested | Not tested |
| N/A | WhatsApp check when a customer is added | Yes (1.2.3) | Not tested | Not tested | Not tested |

Hisaab: all 17 rows of the brief pass, and all 26 rows outside the brief are in place (the last four were added in
1.2.1.0 to 1.2.3.0 and were not looked for on the other builds). None of the features seen on
the three other builds is missing.
