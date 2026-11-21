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
| Languages | English, हिंदी, తెలుగు |
| Status | Finished; every line of the specification met and tested on the live site |

## For the judges: Hisaab in two minutes

Hisaab replaces the khata notebook on a kirana counter. The shopkeeper sees at a glance how much the shop
is owed and by whom, records a sale on credit in seconds (by voice if the hands are full), and asks for
payment with one tap, the exact amount and a UPI pay link already in the WhatsApp message.

**Try it.** Open https://hisaab-hazel.vercel.app. It starts empty, like a new notebook. Press **Load sample
shop** to see the brief's sample (₹11,820 outstanding), or add your own customer. Then:

1. Tap the microphone in **Record** and say "Sunita 500 udhaar" (or type it). The form fills itself; you
   check it and press Save.
2. Tap the green WhatsApp button on any customer: the reminder opens with the name, the balance and a
   `upi://pay` link.
3. Open a customer: entries newest first with a running balance, a UPI QR code to scan, and a printable
   statement.
4. Switch the language to हिंदी or తెలుగు, and the theme to dark.

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
- A full documentation set (below): 8 epics, 42 features and 189 acceptance criteria traced to the tests;
  nine user journeys as sequence diagrams; C4 architecture views; JSON Schemas for every file the app reads
  or writes. Every diagram is PlantUML source plus SVG.

**Deliberately left out.** No login and no server: the brief says browser storage is enough, a sign-in
screen would stand between the grader and the ledger, and keeping data on the phone is a privacy promise
to the shopkeeper. Each judge's browser already holds its own ledger. Cloud backup and sync across a
shop's phones (for example with Supabase sign-in) is the natural next step, listed in
[ARCHITECTURE.md](ARCHITECTURE.md) under risks and limits.

## What it does

- **Customers and entries.** Add a customer, record **You gave** (udhaar) or **You got** (payment) with an
  amount, date, note and an optional payment screenshot; the amount is read from the screenshot, in
  English, Hindi or Telugu. Edit or delete with Undo. Each balance and the
  total outstanding are computed from the entries, never typed in.
- **Overview.** Total outstanding, who owes the most, money collected and given on credit in the last 30
  days, and an overdue list sorted by days since the last payment. The Overdue badge and Remind-all count
  only 30+ days without paying; the list still shows everyone who owes. Search by name (English, Hindi or
  Telugu script) or phone. On a phone the overview is money + customers; Record and Shop file sit under
  the bottom bar.
- **Reminders and getting paid.** One tap opens WhatsApp with the customer's name, balance and a UPI pay
  link already in the message. "Remind all overdue" walks the overdue list, longest first. Each customer
  page shows a UPI QR code to scan at the counter.
- **Customer page and statement.** Every entry newest first with a running balance; a printable statement
  (save as PDF) with a scan-to-pay QR.
- **Quick entry, typed or spoken.** Say or type "Sunita 500 udhaar" (or "सुनीता 500 उधार", "సునీత 300
  అప్పు") and the form fills itself. The owner checks it and presses Save; it never saves by itself.
- **Shop data.** Load a shop file (JSON) or a spreadsheet saved as CSV, with checks and Undo; export
  entries or balances as CSV, or a full backup as JSON.
- **Reads on any screen.** One column on a phone, up to three columns on a wide screen, light and dark,
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
WhatsApp and UPI links are built on the phone, and nothing is sent until the owner presses Send in
WhatsApp. Text from imported files is escaped before it is shown, and exported CSV cells that start with
`=`, `+`, `-` or `@` are prefixed so a spreadsheet does not run them as formulas.
