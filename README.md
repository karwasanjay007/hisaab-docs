# Hisaab: khata, made simple

A digital credit ledger for a local shop: the notebook a kirana keeps of who owes what, on the owner's
phone, with a WhatsApp reminder in one tap. Built for the Freedom With AI Batch 10 build project.

This repository holds Hisaab's documentation: the specification check, product requirements, architecture, epics and acceptance criteria, user journeys, data model and schemas, the four pre-release reviews and the latest test results. The application code is kept in a private repository while the hackathon is open; everything here describes the build running at the live link.


| | |
| --- | --- |
| Live | https://hisaab-hazel.vercel.app |
| Documentation | This repository (public) |
| Code | Private repository; the app runs at the live link above |
| Built by | Sanjay Karwa, Smart AI for Business |
| Stack | Plain HTML, CSS and JavaScript; no framework, no build step, no server |
| Data | Stays in the browser (localStorage); nothing is sent anywhere |
| Languages | English, हिंदी, తెలుగు built in; বাংলা, मराठी, தமிழ், ગુજરાતી, ಕನ್ನಡ, മലയാളം, ਪੰਜਾਬੀ, ଓଡ଼ିଆ, اردو since 1.2.0.0 |
| Version | 1.5.1.1 (27 Nov 2026; baseline 1.0.0.0, Sanjay Karwa, 21 Nov 2026) |
| Status | Finished; every line of the specification met and tested on the live site. Version 1.1.0.0 (22 Nov 2026) adds a shop profile, a balance history line and more than one shop on the same phone, as add-on files beside the ledger; every earlier test passes on the live site after it, unedited |

## What Hisaab supports

Every requirement of the brief and every feature beyond it, in one table. The first column names the requirement
the row belongs to: R1 to R9 are the nine "must do" items, B1 to B7 the bonus menu (at most six count), M the
light and dark marks. N/A means the brief does not ask for it. "Pass" means tested on the live site; "Yes" means
the feature is in place. The last column is the version it arrived in.

| Req | Feature | Supported | Since |
| --- | --- | --- | --- |
| R1 | Add a customer with a name and a 10-digit phone number | Pass | 1.0 |
| R2 | Add an entry: credit or payment, with amount, date and a short note | Pass | 1.0 |
| R3 | Each customer's balance, and the shop's total outstanding at the top | Pass | 1.0 |
| R4 | A customer page that lists the entries, newest first | Pass | 1.0 |
| R5 | A payment screenshot, shown as a small picture on the entry | Pass | 1.0 |
| R6 | A WhatsApp reminder per customer with the name and the balance in rupees | Pass | 1.0 |
| R7 | Data still there after a reload | Pass | 1.0 |
| R8 | An import box that loads a shop file in the sample format | Pass | 1.0 |
| R9 | Works on a phone at 375 px | Pass | 1.0 |
| B1 | Overdue list, sorted by days since the last payment | Pass | 1.0 |
| B2 | Edit and delete an entry, with balances recalculated | Pass | 1.0 |
| B3 | CSV export that matches the data | Pass | 1.0 |
| B4 | Search by name or phone | Pass | 1.0 |
| B5 | A UPI pay link in the reminder | Pass | 1.0 |
| B6 | Hindi or Telugu labels | Pass | 1.0 |
| B7 | Reading the amount from the payment screenshot | Pass | 1.0 |
| M | Readable in light and dark | Pass | 1.0 |
| N/A | Quick entry by typing or by voice ("Sunita 500 udhaar") | Yes | 1.0 |
| N/A | UPI QR code for the exact balance | Yes | 1.0 |
| N/A | Printable statement (PDF) for one customer | Yes | 1.0 |
| N/A | Receipt message after an entry | Yes | 1.0 |
| N/A | Remind all overdue customers, one after another | Yes | 1.0 |
| N/A | Undo after a delete, an edit or an import | Yes | 1.0 |
| N/A | Works without signal after the first visit | Yes | 1.0 |
| N/A | Backup file (JSON), and import by pasting, by choosing a file or from a spreadsheet CSV | Yes | 1.0 |
| N/A | Sample shop in one tap | Yes | 1.0 |
| N/A | Customer names shown in Hindi and Telugu script | Yes | 1.0 |
| N/A | Overview figures: owes the most, collected and given in the last 30 days, longest without paying | Yes | 1.0 |
| N/A | Shop profile: owner, address and logo, on the header and the statement | Yes | 1.1 |
| N/A | Balance history line on the customer page | Yes | 1.1 |
| N/A | More than one shop on the same phone | Yes | 1.1 |
| N/A | Twelve languages (nine more: Bengali, Marathi, Tamil, Gujarati, Kannada, Malayalam, Punjabi, Odia, Urdu) | Yes | 1.2 |
| N/A | Urdu reads right to left (amounts and phone numbers stay left to right) | Yes | 1.5.1 |
| N/A | Urdu reads right to left (amounts and phone numbers stay left to right) | Yes | 1.5.1 |
| N/A | Customer page totals and days since the last payment | Yes | 1.2 |
| N/A | Call and WhatsApp chat links for a customer | Yes | 1.2 |
| N/A | Change a customer's phone number (rename since 1.0) | Yes | 1.2 |
| N/A | Delete a customer, with Undo | Yes | 1.2 |
| N/A | Note shortcuts under the note field | Yes | 1.2 |
| N/A | Total unpaid for 30 days or more, and the date the figures are for | Yes | 1.2 |
| N/A | Customer names in the script of all twelve languages (Hindi and Telugu since 1.0) | Yes | 1.2.1 |
| N/A | A tappable pay link in the WhatsApp reminder, opening a pay page that opens the UPI app | Yes | 1.2.1 |
| N/A | Layout checked in 12 languages at 5 widths: no heading broken into letters, no blank band, no cut label | Yes | 1.2.1 |
| N/A | Other ways to send a reminder when WhatsApp refuses a number: SMS, a chosen contact, or copied | Yes | 1.2.2 |
| N/A | WhatsApp check when a customer is added: a hello message, and the answer remembered per number (switched with one tap) | Yes | 1.2.3 |
| N/A | Voice entry never ends in silence: a failure says why, and offers the keyboard microphone or the computer's dictation | Yes | 1.2.4 |
| N/A | Controls that say what they did: the light and dark button names the theme, a navigation press names the view, and quick entry offers to add a customer whose name it does not know | Yes | 1.2.5 |
| N/A | A change the browser store refused, or a store that is blocked, is said plainly; an oversized imported picture is left out and counted; an edit cannot lose its date; screenshots at most 600 px | Yes | 1.2.6 |
| N/A | Money in whole rupees: typed paise are held with the nearest whole amount offered, imports are rounded and counted, stored paise are offered a round with Undo | Yes | 1.3 |
| N/A | Brand look: navy, cream, slate blue and amber, Fraunces and Inter, the sai4b mark first in the top bar; no green anywhere | Yes | 1.3 |
| N/A | Phone layout: a 56 px top bar, a compact total, quick entry as one pill at the bottom, a slim tab row and a + sheet with every other action | Yes | 1.4 |
| N/A | The + sheet in all twelve languages: its labels and button names follow the chosen language | Pass | 1.4.1 |
| N/A | Fonts and the QR library served from the site itself: a plain visit, a QR code and a language change ask no other host (the screenshot reader still loads on demand from its host) | Pass | 1.4.1 |
| N/A | The ledger maths tested on their own (balances, totals, order, imports, overdue days, the side sums), without a browser | Yes | 1.4.1 |
| N/A | Desktop layout like the best chat apps (768 px and up, on the overview of a shop with customers): a total strip at most 110 px, the customer list gets the height with its search on the title line, quick entry as a sticky pill with a "+" popover; from 1200 px a right panel with the entry form, overdue and recent entries | Pass | 1.5 |

All 17 rows of the brief pass, and all 36 rows beyond it are in place. The same table with three other builds of
the batch beside it is in [REPORT.md](REPORT.md), section "Requirement and feature map".

## For the judges: Hisaab in two minutes

**Promise.** Replace the paper khata with a phone ledger that records credit and payments, shows total
outstanding, and opens WhatsApp with the exact amount ready to send.

**Why not a typical LLM demo.** This is a product loop, not a chat wrapper. Balances are computed from
entries. The app starts empty; one tap loads the sample. Quick entry fills the form in English, Hindi or
Telugu and never auto-saves. Offline after one visit. Data stays in the browser.

**Try it in 60 seconds.** Open https://hisaab-hazel.vercel.app. Press **Load sample shop** (₹11,820
outstanding). Then:

1. In **Record**, type or speak "Sunita 500 udhaar", check the fields, press Save.
2. Tap the WhatsApp button on any customer: name, Rs amount and a `upi://pay` link.
3. Open a customer: entries newest first, UPI QR, printable statement.
4. Switch language to हिंदी or తెలుగు, and the theme to dark.

**Honest limits.** localStorage in this browser only. Demo UPI may appear in reminders until the shop sets
its own ID in Settings. Spoken voice depends on the browser; typed Fill + Save is the reliable grader path.
Paste-ready dashboard copy lives in [REPORT.md](REPORT.md) (Judges' note).

**Against the specification.** Every requirement on the projects page, copied word for word into
[brief/spec-page.md](https://dashboard.freedomwithai.com/redesign/v3/projects), is rated line by line in [SPEC-CHECK.md](SPEC-CHECK.md):

| Part | Marks | Status |
| --- | --- | --- |
| Nine must-do items, each "Done when" followed literally in a fresh browser (tests/brief.mjs) | 60 (hidden data) | All met; every number computed from the data loaded, including empty customers and advances |
| The six tester hooks (`#total-outstanding`, `[data-customer]` with `[data-balance]`, `a[data-remind]`, `#import-box` and `#import-go`, `#shot-input` and `img[data-shot]`, `?today=`) | | All in place, copied exactly |
| Bonus menu | 30 | All seven built: overdue list, edit and delete with Undo, CSV export, search, UPI pay link, Hindi and Telugu, amount read from the payment screenshot |
| Phone and light/dark | 10 | No sideways scroll at 375 px, 44 px tap targets, every text 4.5:1 or better in both themes, including hover and after every action |

**What we added, and why.** The coach asked for automations a shopkeeper would pay for, so they are in: a
UPI QR on every customer page, "Remind all overdue" (opens each WhatsApp chat in turn, longest overdue
first), a WhatsApp receipt after each entry, a statement with a scan-to-pay QR, and CSV import from the
spreadsheet a shop already keeps. The difference no other entry will have is **voice quick entry** in
English, Hindi and Telugu: the shopkeeper speaks, the form fills, a person always presses Save. It works
offline after one visit and nothing ever leaves the phone.

**How it was built.** Specification first, then tests, then code:
- The projects page was captured word for word and every "Done when" became an automated check before it
  was built. Eight Playwright scripts in tests/ run against the live site; the latest results
  are in [tests/last-brief-live.txt](evidence/last-brief-live.txt) and
  [tests/last-run-live.txt](evidence/last-run-live.txt). The independent checker, written from the brief
  alone, scores 27 of 27 on four randomly generated shops.
- Four separate reviews before release, kept in [review/](review/): a comparison with the leading ledger
  apps, an audit against the brief and the coaching, a functional test and a design critique. Every
  finding was fixed or recorded.
- A full documentation set (below): 15 epics, 70 features and 341 acceptance criteria traced to the tests;
  nine user journeys as sequence diagrams; C4 architecture views; JSON Schemas for every file the app reads
  or writes. Every diagram is PlantUML source plus SVG.

**Deliberately left out.** No login and no server: the brief says browser storage is enough, a sign-in
screen would stand between the grader and the ledger, and keeping data on the phone is a privacy promise
to the shopkeeper. Each judge's browser already holds its own ledger. Cloud backup and sync across a
shop's phones (for example with Supabase sign-in) is the natural next step, listed in
[ARCHITECTURE.md](ARCHITECTURE.md) under risks and limits.

## What it does

- **Customers and entries.** Add a customer, record **You gave (credit)** (udhaar) or **You got (payment)** with an
  amount, date, note and an optional payment screenshot; the amount is read from the screenshot, in
  English, Hindi or Telugu. Edit or delete with Undo. Each balance and the
  total outstanding are computed from the entries, never typed in.
- **Overview.** Total outstanding, who owes the most, money collected and given on credit in the last 30
  days, and an overdue list sorted by days since the last payment. The Overdue badge and Remind-all count
  only 30+ days without paying; the list still shows everyone who owes. Search by name (English, Hindi or
  Telugu script) or phone. On a phone nothing is hidden: Customers and Overdue come first, then the
  record form, add customer, recent entries, Shop file (import) and settings.
- **Reminders and getting paid.** One tap opens WhatsApp with the customer's name, balance and a UPI pay
  link already in the message. "Remind all overdue" walks the overdue list, longest first. Each customer
  page shows a UPI QR code to scan at the counter.
- **Customer page and statement.** Every entry newest first with a running balance; a printable statement
  (save as PDF) with a scan-to-pay QR.
- **Quick entry, typed or spoken.** Say or type "Sunita 500 udhaar" (or "सुनीता 500 उधार", "సునీత 300
  అప్పు") and the form fills itself. The owner checks it and presses Save; it never saves by itself.
- **Shop data.** Load a shop file (JSON) or a spreadsheet saved as CSV, with checks and Undo; export
  entries or balances as CSV, or a full backup as JSON.
- **Reads on any screen.** One column on a phone; on a desktop the customer list gets the screen, with quick
  entry as a pill at the bottom and, from 1200 px, a right panel; light and dark,
  and it opens offline after one visit.

How the pieces fit is drawn in SVG: the C4 views in [ARCHITECTURE.md](ARCHITECTURE.md) and one sequence
diagram per journey in [docs/journeys.md](docs/journeys.md). To see the screens, open the live link.

## Documentation

| Document | What it covers |
| --- | --- |
| [PRD.md](PRD.md) | What is built and why: decisions, rules, scenarios |
| [docs/epics.md](docs/epics.md) | Epics, features and acceptance criteria, traced to the PRD and the tests |
| [docs/journeys.md](docs/journeys.md) | User journeys with sequence diagrams (PlantUML source and SVG in `docs/diagrams/`) |
| [ARCHITECTURE.md](ARCHITECTURE.md) | C4 context, container and component views, decisions, deployment, cross-cutting concerns |
| [docs/data-model.md](docs/data-model.md) | Entities, derived values and their formulas, storage keys |
| [docs/schema/](docs/schema/) | JSON Schemas (shop file, stored state, quick-entry result), CSV columns, link formats |
| [TECH-STACK.md](TECH-STACK.md) | Each technology choice with its reason and cost |
| [IMPLEMENTATION-PLAN.md](IMPLEMENTATION-PLAN.md) | The build slices and release steps |
| [REPORT.md](REPORT.md) | Build report, final review and the note for judges |
| WORKLOG.md | One line per slice: what was done, the command, the result |

To redraw a diagram after editing its `.puml` source (Java and the PlantUML jar needed):

```bash
java -jar plantuml.jar -tsvg -charset UTF-8 docs/diagrams/<name>.puml
```

Diagrams are kept as SVG only.

## Privacy

No account, no server and no keys. Customer names, phone numbers and amounts stay in the browser.
WhatsApp, SMS and UPI links are built on the phone, and nothing is sent until the owner presses Send in
WhatsApp or the messages app.
The pay link in a reminder carries the shop name, the UPI ID, the amount and the customer's name to the
customer who receives it. On the pay page those details sit after the "#" of the address, which a browser
never sends to a server, and the page stores nothing.
The answer to the WhatsApp check is kept in the same browser, outside the shop file. No app can ask WhatsApp
whether a number has an account, so Hisaab asks the owner; no number is sent to any checking service. Text from imported files is escaped before it is shown, and exported CSV cells that start with
`=`, `+`, `-` or `@` are prefixed so a spreadsheet does not run them as formulas.
