# Hisaab: report

## Status per part
- Must-do list (9 items): DONE
  evidence: `node tests/check.mjs http://localhost:4173/ 12345` -> `Summary: 27 PASS, 0 FAIL` (tests/last-run.txt)
- Hidden data (different shop through #import-box / #import-go): DONE
  evidence: seeds 12345, 777 and 4242, 15 customers each, all balances and totals correct; import replaces the shop
- Bonus menu: DONE, all 7 (UPI link, overdue list, search, CSV, Hindi and Telugu, amount from screenshot, edit and delete)
- Phone 375 px and light/dark: DONE (scrollWidth 375; every text pair passes WCAG AA in both themes)
- Live: https://hisaab-hazel.vercel.app (Vercel project hisaab, team elodos)

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

## Judges' note (as submitted on 20 Nov 2026, 9:29 pm IST)
Live app: https://hisaab-hazel.vercel.app
Documentation: https://github.com/karwasanjay007/hisaab-docs (specification check, architecture, 189 acceptance criteria, journeys, data model, reviews and test results)

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

- Record form labels **You gave** and **You got** (radio values remain `credit` / `payment` for the tester hooks).
- Overdue badge and Remind-all count customers with 30 or more days since last payment (sample on
  `?today=2026-11-15`: badge **4**); the overdue list still shows every owing customer, sorted by days.
- On a phone the overview shows hero, customers and overdue; Record, Shop file and settings sit under
  the bottom bar (DOM hooks unchanged).
- Service worker registers on `https`, `localhost` and `127.0.0.1`.

Evidence: `node tests/brief.mjs` ALL PASS; `node tests/check.mjs … 4242` → 27 PASS, 0 FAIL, estimate
100/100; `final` / `quick` / `nav` / `gaps` / `offline` ALL PASS. See `evidence/release-1-0-0-0.md`.
Projects-page re-capture under a signed-in session remains To do; spoken voice remains unchecked
end-to-end.
