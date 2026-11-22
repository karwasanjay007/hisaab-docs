# Hisaab: brief and coaching audit

Independent audit, 20 Nov 2026. Sources: `brief/brief.md`, `brief/data/`, `app.js`, `index.html`, the
live site https://hisaab-hazel.vercel.app, `tests/last-run-live.txt`, `REPORT.md`, and the three 15 Nov
2026 Level 5 transcripts plus `coaching-guide.md`. No project file was changed.

Own live run: a Playwright script (scratchpad `audit.cjs`, output `audit-out.txt`, `audit2-out.txt`) loaded
a shop of my own through `#import-box` and `#import-go` at 375 px with `?today=2026-12-01`: six customers
with a decimal balance (800.25), a lakh-sized balance (125000), an advance (-200), no entries (0), a
settled account (0) and credit only (40), with entries out of date order and two on the same date.

## 1. Requirements, one by one

| # | Requirement | Verdict | Evidence |
| --- | --- | --- | --- |
| M1 | Add a customer with name and 10 digit phone | Met | `phone10`/`validPhone` in app.js:285 onwards; checker add-customer flow; form in index.html |
| M2 | Entry: credit or payment, amount, date, short note | Met | entry form index.html ~140-160; save at app.js:724; checker 5-shot flow added a payment |
| M3 | Each customer's balance and shop total at the top | Met | `balances()` app.js:383, `totalOutstanding()` app.js:391; own run 6/6 balances correct |
| M4 | Customer page, entries newest first | Met | `newestFirst` app.js:398; own run: Bharat Big shows 10 Nov payment above 1 Sept credit |
| M5 | Payment screenshot, small picture on the entry | Met | `#shot-input` index.html:152; `img[data-shot]` app.js:556; live checker PASS incl. after reload |
| M6 | WhatsApp reminder per customer with name and rupee balance | Met | `waLink` app.js:434; own run: every link `https://wa.me/91<phone>?text=`, text holds name and "Rs 1,25,000" |
| M7 | Data survives reload | Met | localStorage app.js:339-345; own run: imported shop and total unchanged after reload |
| M8 | Import box loads a different shop | Met | `doImport` app.js:855; own run: "Loaded Audit Kirana Test: 6 customers, 9 entries", no sample customer left; bad JSON gives a message and keeps data |
| M9 | Works on a phone, 375 px | Met | own run scrollWidth 375 after import; all hooks visible at 375 px without navigating |
| H1 | `#total-outstanding` contains the number | Met, with a risk | sample "₹11,820"; own shop "₹1,25,840.25 (net ₹1,25,640.25 after advances)". See risks R1, R2 |
| H2 | `[data-customer="<phone>"]` with `[data-balance]` inside | Met | app.js:490, 495; attribute holds the raw number (800.25, -200), text "₹800.25" |
| H3 | `a[data-remind="<phone>"]`, link `https://wa.me/91<phone>?text=<message>` | Met | app.js:496; own run 6/6 links in that exact form |
| H4 | `#import-box` text area and `#import-go` button | Met | index.html:243, 246; visible on first load on phone and desktop |
| H5 | `#shot-input` on the payment form, then `img[data-shot]` | Met | index.html:152, app.js:556; checker PASS |
| H6 | Date logic honours `?today=` | Met | `todayIso` app.js:304; own run with 2026-12-01: credit-only customer shows 61 days (1 Oct to 1 Dec) |
| D1 | Sample data shape `{shop, customers, entries}` | Met | `fromShopFile` app.js:351; also tolerates `shop_name`, `debit`/`paid`, "Rs" prefixes, dd/mm/yyyy |
| D2 | Sample expected: total 11820, 8 balances | Met | live checker PASS; desktop fresh load shows "₹11,820" |
| D3 | `top_defaulter_phone` 9000000102 | Met (not a listed hook) | first row is the top defaulter and labelled "Owes the most"; own shop: Bharat Big first and labelled |
| D4 | `customers_with_balance` 8 | Met (not a listed hook) | text "8 customers owe money"; own shop "3 customers owe money" (positive balances only). See R3 |
| B1 | Overdue list sorted by days since last payment | Met | `overdueRows` app.js:417; checker PASS with two `today` values |
| B2 | Edit and delete an entry, balances recalculated | Met | checker PASS (4420 then 3920) |
| B3 | CSV export matching the data | Met | checker PASS: 38 rows, sums match |
| B4 | Search by name or phone | Met | checker PASS |
| B5 | UPI pay link in the reminder | Met | own run: `upi://pay?pa=hisaab.demo%40upi&...&am=125000.00` in every owed reminder. See R5 |
| B6 | Hindi or Telugu labels | Met | both; checker PASS |
| B7 | Amount read from the payment screenshot | Met | checker PASS (750 filled after 2 s) |
| B- | At most 6 bonus items count | Met | 7 built, so one can fail and 30/30 still holds |
| P1 | No sideways scroll at 375 px (5 marks) | Met | scrollWidth 375 on home and customer page, also with a lakh-sized shop |
| P2 | Readable in light and dark (5 marks) | Met | checker contrast PASS both themes; `prefers-color-scheme: dark` alone gives the dark background |
| S1 | Live link required and public | Met | site returns the app with no login |
| S2 | One submission per member, editable | Open | not yet submitted. See R6 |
| S3 | Details for the judges (optional) | Ready | judges' note in REPORT.md |
| S4 | Tie-break: earlier submission wins | Open | argues for submitting now, then editing |
| S5 | Deadline | Open | page: Sat 21 Nov 2:00 pm IST; class: Sun 22 Nov 10:00 am IST. Work to the earlier |
| S6 | Video | Not asked | coach "thinking to add a place" for a video; no field in the brief |
| C1 | Console and dialogs | Met | own run: no page or console errors |

Nothing in the brief is unmet. Our checker's 27 PASS reproduces with data it did not generate.

## 2. What the coaches said (15 Nov 2026, Level 5 part 3)

Quotes are short and verbatim from the transcript (the transcript renders Hisaab as "hab").

- The idea: "how many of you know about katab book" (Khatabook); local shops keep no ledger and
  "cannot send customer invoices or payment links".
- Range of ambition: "you can be making a basic application also or you can go super creative".
- What creative means here: "automatically follows up with the customer, sends them the bill, sends
  them the payment link, UPI QR code". Then "Go absolutely wild".
- Purpose: "you can actually go to a local business and sell it to them".
- Standard: "build something that you would be proud of to showcase".
- Who judges: "our team will be reviewing all the projects". The written brief says the bonus marks are
  "decided by a script or a screenshot check, not taste".
- Both projects: "I recommend you build both so you can maximize your chances".
- Submission: "upload the link here" plus optional details "like this is the ID and password".
- Video: "I'm also thinking to add a place if you can record a video".
- Visibility: after the event "we'll public all the projects so that everyone can see" (no code shown).
- Speed: "Amar is ahead of every one of you. So you guys should catch up."
- Deadline in class: "Sunday 22nd 10:00 a.m. in the morning"; winners named at the next session.
- Tries: nothing said in class; the brief says one submission per member, editable later.

Summary: marks are mechanical (60 hidden data, 30 bonus, 10 phone and theme), so creativity earns no
marks directly. It counts where people choose: a team reviews, the projects go public, and the coach
asked for something "proud to showcase" and sellable to a shop. Ties go to the earlier submission.

### What this submission should add or say

1. Submit now, then edit. The earlier submission wins a tie, and the 60/30/10 marks are probably level
   for many entrants.
2. Lead the judges' note with the coach's own words: it is a Khatabook for a kirana that a local shop
   could use tomorrow. Name the automations he listed and say which are in: payment link (UPI in every
   reminder), bill (WhatsApp receipt after each entry, statement as PDF). The note should also tell the
   reviewer they can load their own shop through the import box.
3. Cheap gaps against his list. He named a **UPI QR code** and **automatic follow-up**, and neither is
   in the code (no "qr" anywhere in app.js or index.html; no scheduled or "remind all" flow). A QR on the
   customer page or statement from the same `upi://` link is small. "Remind everyone overdue" (a queue
   of wa.me links opened one after another) would cover follow-up without a server.
4. Keep a 60 to 90 second phone-width screen recording ready even though no field exists yet: import a
   shop, record a credit, attach a screenshot and read its amount, send the WhatsApp reminder with UPI,
   then switch to Telugu and to dark. A video helps if a field is added, and helps for the public showcase.
5. If FrontDesk AI is also built, settle first how the dashboard treats two submissions (see R6).

## 3. Risks: where a grader could read the brief differently

| # | Risk | Likelihood | Note |
| --- | --- | --- | --- |
| R1 | **Two figures in `#total-outstanding` when someone is in advance.** Text reads "₹1,25,840.25 (net ₹1,25,640.25 after advances)". A grader that takes the first number gets the sum of positive balances. If its expected value is the sum of all balances, it fails, and a "contains" check could also match the wrong figure by chance. | Medium; hits only if the hidden shop has an overpaid customer, which our checker's generator assumes | Sample expected (11820) cannot tell the two readings apart. Choose one reading and show one number in the element, or put the net figure first. "All credit minus all payments" summed over the shop gives the net figure, which argues for net. |
| R2 | **Indian digit grouping at a lakh or above.** "₹1,25,840" does not contain "125,840" or "125840" unless commas are stripped. The brief's example "Rs 11,820" does not tell the two apart. | Low to medium; only if a hidden balance or total reaches 1,00,000 | A plain-number copy inside the element (for example a `data-value` or a visually hidden span) would cover both readings without changing the look. |
| R3 | `customers_with_balance`: the app counts positive balances only ("3 customers owe money"). A grader may count non-zero (4 in my shop). | Low; not a listed hook | Leave it, or label it "owe money" (already done) so a screenshot reader takes it literally. |
| R4 | Entries for a phone missing from `customers` create a new customer named by the phone; entries with no date take today. Either could change row count or overdue days. | Low | Reasonable, but note it in the judges' note. |
| R5 | The UPI ID is a demo (`hisaab.demo@upi`). A reviewer might see a fake payee. | Low | Already labelled as demo in Settings; say so in the note. |
| R6 | **One submission per member** (brief) against "build both to maximize your chances" (coach). If the dashboard keeps one entry per member, submitting FrontDesk AI later could replace the Hisaab entry. CHECKPOINT.md says "Then FrontDesk AI". | Medium, high cost | Check the dashboard form before submitting the second project; ask in the group if unclear. |
| R7 | **Two deadlines**: page Sat 21 Nov 2:00 pm IST, class Sun 22 Nov 10:00 am IST. | Low, high cost | Work to Saturday 2:00 pm IST. |
| R8 | The tester opens the link in a browser that already holds an imported shop. Import replaces it, so tests still pass; only a visual reviewer reusing a browser would see someone else's shop. | Very low | Nothing to do. |
| R9 | Bonus "decided by a script or a screenshot check". A screenshot checker may look for words: "Overdue", "Export CSV", "Search", a UPI link. | Low | Labels are visible in English; keep English as the default language. |

## Evidence files

- Own live runs: `the session scratch folder\audit-out.txt` and `audit2-out.txt` in the same folder (scripts `audit.cjs`, `audit2.cjs`). The scratchpad may be cleared; the key results are copied into the table above.
- Project checker: `tests/last-run-live.txt` (27 PASS, seed 6060).
- Transcript: `resources/coaching/transcripts/2026-11-15-batch10-level5-part3.md`. Parts 1 and 2 are on
  outreach and sales and say nothing about project judging.
