# Hisaab: khata, made simple

A digital credit ledger for a local shop: the notebook a kirana keeps of who owes what, on the owner's
phone, with a WhatsApp reminder in one tap. Built for the Freedom With AI Batch 10 build project.

This repository holds Hisaab's documentation: requirements, epics and acceptance criteria, data model,
architecture, journeys, the report and the latest test results. The application code is kept in a private
repository. The text documents here describe version 2.0.1.0, the build running at the live link.

| | |
| --- | --- |
| Live | https://hisaab-hazel.vercel.app |
| Documentation | This repository (public) |
| Code | Private repository |
| Built by | Sanjay Karwa, Smart AI for Business |
| Stack | Vite, React, TypeScript, Tailwind CSS, Zustand; static files, no server, no AI |
| Data | Stays in the browser (`localStorage`); nothing is sent anywhere |
| Languages | English, हिन्दी, తెలుగు |
| Version | 2.0.1.0 (live since 27 Nov 2026). Versions 1.0.0.0 to 1.2.5.0 (20 to 25 Nov 2026) were plain HTML and JavaScript |
| Status | Checker on the live link: 25 PASS, 1 FAIL (the screenshot amount reader, not built), estimate 100/100 |

## What Hisaab supports

What version 2.0.1.0 does. R1 to R9 are the nine "must do" items of the brief, B1 to B7 the bonus menu (at most
six count), M the light and dark marks. N/A means the brief does not ask for it. "Pass" means the checker
passed it on the live link on 27 Nov 2026. "Local" means it passes in the local tests only. "Yes"
means it is in the code and no test of 2.0 covers it. "No" means it is not in 2.0.1.0.

| Req | Feature | Supported |
| --- | --- | --- |
| R1 | Add a customer with a name and a 10-digit phone number | Yes |
| R2 | Add an entry: credit or payment, with amount, date and a short note | Pass |
| R3 | Each customer's balance, and the shop's total outstanding at the top | Pass |
| R4 | A customer page that lists the entries, newest first | Local |
| R5 | A payment screenshot, shown as a small picture on the entry | Pass |
| R6 | A WhatsApp reminder per customer with the name and the balance in rupees | Pass |
| R7 | Data still there after a reload | Pass |
| R8 | An import box that loads a shop file in the sample format | Pass |
| R9 | Works on a phone at 375 px | Pass |
| B1 | Overdue list, sorted by days since the last payment | Pass |
| B2 | Edit and delete an entry, with balances recalculated | Pass |
| B3 | CSV export that matches the data | Pass |
| B4 | Search by name or phone | Pass |
| B5 | A UPI pay link in the reminder (a demo UPI id until the shop sets its own) | Pass |
| B6 | Hindi and Telugu labels | Pass |
| B7 | Reading the amount from the payment screenshot | No |
| M | Readable in light and dark | Pass |
| N/A | Undo after deleting an entry | Local |
| N/A | Reminder text in the chosen language | Local |
| N/A | A ledger, language and theme saved by 1.x open in 2.0 | Local |
| N/A | A full browser store refuses the action and loses nothing | Local |
| N/A | Opens with no network after one visit; asks no other site for anything | Local |
| N/A | The theme button and the navigation say what they did | Local |
| N/A | A searched name with no match offers to add that customer | Local |
| N/A | Days since the last payment on the customer page | Local |

The checker's full output is in [evidence/last-check-live-2.0.1.0.txt](evidence/last-check-live-2.0.1.0.txt).

## In 1.x, not in 2.0

Version 2.0 is a rebuild. These existed in 1.2.5.0 and are not in 2.0.1.0:

- Nine more languages (Bengali, Marathi, Tamil, Gujarati, Kannada, Malayalam, Punjabi, Odia, Urdu)
- Voice quick entry and typed quick entry
- The screenshot amount reader
- Several shops on one phone
- Shop profile and the balance history line
- Other ways to send a reminder (SMS, a chosen contact, copy)
- The WhatsApp check of a number
- The pay page and the UPI QR code
- The printable statement
- CSV import, the JSON backup file, Undo after an import
- Deep links
- The wide, multi-column layout
- Smaller items: the sample shop button, the receipt message, remind all, names in another script, call and chat links, change phone, delete customer, note shortcuts, the overview figures

The results measured on 1.x are kept in [REPORT.md](REPORT.md) and in the older files of `evidence/`.

## Try it

Open https://hisaab-hazel.vercel.app. The ledger starts empty.

1. Add a customer (name and 10-digit phone), or paste a shop file into the import box and press Import.
2. Open the customer and add a credit or a payment; attach a payment screenshot if there is one.
3. Press Remind on a row: WhatsApp opens with the name, the balance and a `upi://pay` link.
4. Open Overdue for who has gone longest without paying. Open Settings to set the shop's UPI id and to export CSV.
5. Switch the language to हिन्दी or తెలుగు, and the theme to dark.

## Limits

- The ledger lives in this browser only. There is no backup file and no sync in 2.0.
- Until the shop sets its own UPI id, reminders carry the demo id `hisaab.demo@upi`. Set the real id before sending a real reminder.
- Tested in Chromium. Other browsers and a real phone: not tested.

## Documentation

| Document | What it covers |
| --- | --- |
| [PRD.md](PRD.md) | What is built and why; the list of what 1.x had and 2.0 does not |
| [docs/epics.md](docs/epics.md) | Epics, features and acceptance criteria, each traced to a test file |
| [docs/data-model.md](docs/data-model.md) | The stored shape, the storage keys, the carry-over from 1.x, formulas |
| [TECH-STACK.md](TECH-STACK.md) | Each technology choice with its reason |
| [IMPLEMENTATION-PLAN.md](IMPLEMENTATION-PLAN.md) | The rebuild phases and the fix pass |
| [SPEC-CHECK.md](SPEC-CHECK.md) | The brief, line by line |
| [REPORT.md](REPORT.md) | Measured results per version |
| [ARCHITECTURE.md](ARCHITECTURE.md) | C4 views, decisions, deployment |
| [docs/journeys.md](docs/journeys.md) | User journeys with sequence diagrams (PlantUML source and SVG in `docs/diagrams/`) |
| [docs/schema/](docs/schema/) | Schemas and link formats |

To redraw a diagram after editing its `.puml` source:

```bash
java -jar plantuml.jar -tsvg -charset UTF-8 docs/diagrams/<name>.puml
```

Diagrams are kept as SVG only.

## Privacy

No account, no server and no keys. Customer names, phone numbers and amounts stay in the browser. The page
asks no other site for anything. A WhatsApp link is built on the phone, and nothing is sent until the owner
presses Send in WhatsApp. The reminder carries the customer's name, the balance and the UPI id to the customer
who receives it. Text from an imported file is shown as text, never as markup.
