# Hisaab: architecture

| | |
| --- | --- |
| Product | Hisaab, a shop credit ledger (khata) for a local kirana shop |
| Live | https://hisaab-hazel.vercel.app (Vercel project `hisaab`) |
| Code | private repository github.com/karwasanjay007/hisaab |
| Last revised | 27 Nov 2026 (version 1.5.1.0) |

Diagrams are C4 (PlantUML with the bundled C4 library), kept as `.puml` sources in `docs/diagrams/` and
rendered to SVG.

## Contents

1. [Overview and goals](#1-overview-and-goals)
2. [Constraints and decisions](#2-constraints-and-decisions)
3. [Context](#3-context)
4. [Containers](#4-containers)
5. [Components](#5-components)
6. [Runtime views](#6-runtime-views)
7. [Data](#7-data)
8. [Deployment](#8-deployment)
9. [Cross-cutting concerns](#9-cross-cutting-concerns)
10. [Quality and testing](#10-quality-and-testing)
11. [Risks and known limits](#11-risks-and-known-limits)
12. [Glossary](#12-glossary)

## 1. Overview and goals

Hisaab replaces the notebook a kirana keeps of who owes what. The owner records udhaar (goods given on
credit) and jama (payments received), sees every balance and the total owed to the shop, and sends a
WhatsApp reminder with the exact amount and a UPI pay link in one tap.

The whole product is a static single-page app. There is no server, no account and no database: the shop
lives in the browser's localStorage on the owner's phone.

Goals the architecture serves (from PRD.md and TECH-STACK.md):

- Correct figures: balance = all credit minus all payments per customer (F-1); every figure is computed
  from stored entries (F-3); total outstanding is the sum of every customer's balance (D-3).
- Data stays on the phone and survives reload (D-1, F-5).
- Fast on a cheap phone, usable at 375 px, light and dark.
- Testable by an automated browser that reads fixed ids and data attributes (`#total-outstanding`,
  `[data-customer]`, `[data-balance]`, `a[data-remind]`, `#import-box`, `#import-go`, `#shot-input`,
  `img[data-shot]`) and honours `?today=YYYY-MM-DD` (F-4).
- Cost: $0 a month.

## 2. Constraints and decisions

Constraints: no server data (D-1); loading a shop file replaces the current shop after it is checked (D-2);
deleting can be undone straight away (D-4); graded by a scripted browser; one owner on one phone.

| ID | Decision | Reason | Alternative rejected |
| --- | --- | --- | --- |
| AD-1 | Plain HTML, CSS and JavaScript; no framework, no build step. `app.js` is one IIFE in strict mode | Smallest and fastest; nothing to compile or break; every id the grader reads is under direct control | React with Vite: more weight, no benefit for one screen of forms and lists |
| AD-2 | No server and no account; Vercel serves static files only | The brief allows browser storage; nothing to secure or pay for; no personal data leaves the phone | A backend with login: not needed for one owner on one phone |
| AD-3 | One JSON document in localStorage under `hisaab.v1`, written in full after every change; preferences under `hisaab.lang` and `hisaab.theme` | Synchronous, simple, survives reload (F-5) | IndexedDB: only needed for many large photos |
| AD-4 | Screenshots resized in the browser to at most 600 px (640 px until 1.2.6.0) and stored as JPEG (quality 0.72) data URLs on the entry | Keeps the stored document well under the browser's storage limit | Storing the original picture: fills storage fast |
| AD-5 | Hash routing: `#/` overview, `#/s/<section>` for each nav item, `#/c/<10-digit phone>` for a customer page | Works on any static host with no rewrite rules; reload and back button keep the view | History API paths: need server rewrites to `index.html` |
| AD-6 | No `alert`, `confirm` or `prompt`. Delete, edit and import act at once and show a toast with Undo for 7 seconds | An automated browser stalls on, or cancels, a dialog; Undo is safer for a person too (D-4) | Confirm dialogs: block the grader and annoy the owner |
| AD-7 | Network-first service worker (`sw.js`), cache `hisaab-v18` (version 1.5.1.0): the app shell, with the fonts (`fonts/fonts.css`) and the QR library (`vendor/qrcode.min.js`), is precached on install, older caches are deleted on activate, and GET requests for the app's own files are cached as they load | A new release shows at once; after one online visit the app, the QR code included, opens with no signal | Cache-first: owners would keep running an old release |
| AD-8 | Libraries loaded only when needed by adding a script tag: qrcode-generator 1.4.4 from the site itself (`vendor/qrcode.min.js`, MIT, since 1.4.1.0; cdnjs before), Tesseract.js 5.1.1 from jsDelivr | First load carries no library weight; most visits never need OCR | Bundling them: a much heavier first load |
| AD-9 | Amount from a screenshot read on the phone with Tesseract.js, in English or English plus Hindi or Telugu by the app language; the owner checks it before saving | Free, no key, no server | A vision model API: needs a key and a server |
| AD-10 | All stored and imported text passes through `esc()` before it goes into HTML; imported screenshots are kept only if they start with `data:image/` | Imported files are untrusted; stops markup or script injection | Trusting the file: an XSS hole |
| AD-11 | Money as ₹ with Indian grouping (`en-IN`) and Latin digits by default; Hindi or Telugu digits are an opt-in setting (`state.nativeDigits`) | Latin digits are what most Indian users and the grader read; the minus sits next to the digits in the text (₹-3,530) and is drawn before the rupee sign (-₹3,530) | Native digits whenever the language changes |
| AD-12 | `?today=` in the address overrides today's date everywhere (`todayIso()`) | Lets tests fix the date for overdue days (F-4) | The system clock only: untestable |
| AD-13 | A first visit starts with an empty shop (`blank()`: "My shop", no customers, no entries). The overview's empty state offers Add your first customer (`#add-first`) and Load sample shop (`#load-sample-home`, which loads `sample-data.json`, with `sample.js` as the fallback) | The projects page's "Done when" lines add Ravi Kumar 9000000101 to a fresh app and expect Rs 250 after a Rs 250 credit; with the sample preloaded he was a duplicate and read Rs 920. The sample stays one tap away | The sample shop preloaded on a first visit (the earlier design): breaks those lines |
| AD-14 | Fonts: Baloo 2 for headings, Noto Sans with Devanagari and Telugu (and Fraunces and Inter since 1.3.0.0), served from the site itself since 1.4.1.0: `fonts/fonts.css` and its woff2 files, with the OFL notes (Google Fonts before) | Indian scripts render properly | System fonts: Telugu can fall back badly |
| AD-15 | Add customer accepts any 10 digit number (`validPhone`), as the brief says. Input with anything other than digits, spaces and + ( ) - . is refused (`errPhone`); a number that does not start with 6, 7, 8 or 9 is added with a warning (`warnMobile`, from `validMobile`). Import accepts any 10 digits with no warning | Follows the brief's rule and its "Done when" line (9 digits or letters refused with a message), while still telling the owner that WhatsApp may not reach a number that is not a mobile | Refusing numbers that are not mobiles (the earlier rule, `errMobile`): stricter than the brief |

## 3. Context

![C4 context](docs/diagrams/c4-context.svg)

- The **shop owner** uses Hisaab in a phone or desktop browser.
- The **customer** never opens Hisaab. They receive a WhatsApp message the owner sends, and pay through
  their own UPI app from the `upi://pay` link or the QR code.
- The **automated grader** is a scripted browser that opens the live site, loads hidden shops through
  `#import-box` and `#import-go`, and reads the fixed ids.
- **WhatsApp** is reached only through `https://wa.me/91<phone>?text=<message>` links that Hisaab builds
  locally. Hisaab never sends anything itself; the owner presses Send.
- The **SMS app** (version 1.2.2.0) opens an `sms:` link with the same reminder text, for a customer who
  has no WhatsApp. The **phone dialer** (version 1.2.0.0) opens a `tel:` link from the customer page.
- The **pay page** `pay.html` (version 1.2.1.0) is part of Hisaab. The reminder carries an https link to it,
  because WhatsApp may not make a `upi://` link tappable. It opens the customer's UPI app.
- **Vercel** serves the static files, the fonts (`fonts/fonts.css`) and the QR library (`vendor/qrcode.min.js`)
  among them since 1.4.1.0, so a plain visit asks no other host. **jsDelivr** serves the screenshot reader,
  loaded only when a screenshot is chosen.

## 4. Containers

![C4 containers](docs/diagrams/c4-container.svg)

| Container | Technology | What it does |
| --- | --- | --- |
| Single-page app | `index.html`, `styles.css`, `sample.js`, `app.js` | All views, forms and figures. `index.html` holds every section; an inline script applies the saved theme before first paint |
| localStorage | Web Storage | `hisaab.v1` (the shop), `hisaab.lang`, `hisaab.theme`; `hisaab.shops` (1.1.0.0); `hisaab.wa` (1.2.3.0, the owner's WhatsApp answer per number, outside the shop). A `storage` event listener reloads the shop when another tab changes it |
| Service worker and cache | `sw.js`, Cache API | Registered only on HTTPS or localhost. Precaches the app shell in `hisaab-v18` on install (the add-on files, `pay.html`, the fonts and the QR library included); network first for the app's own files; on failure the cached copy, or the cached `index.html` |
| Add-ons | `profile.js`, `shops.js`, `langs.js`, `extras.js`, `customer.js`, `names.js`, `sendways.js`, `wacheck.js`, `assist.js`, `guard.js`, `compact.js` and their style files, with `polish.css` and `brand.css` | Loaded after `app.js`; they reach the ledger only through `window.Hisaab` (see the version sections below) |
| Language files | `i18n/<code>.json` | Nine languages fetched when chosen; 254 text keys per language (235 until 1.4.0.0; the 19 keys of the + sheet added in 1.4.1.0) |
| Pay page | `pay.html` | A separate small page. Reads shop, UPI ID and amount from the part after `#`, shows them and opens the `upi://pay` link |
| Lazy-loaded libraries | qrcode-generator 1.4.4 (`vendor/qrcode.min.js`, on the site), Tesseract.js 5.1.1 (jsDelivr) | QR codes as inline SVG; text recognition of payment screenshots |
| Web Speech API | `SpeechRecognition` or `webkitSpeechRecognition` | Voice quick entry in `en-IN`, `hi-IN` or `te-IN`. The microphone button stays hidden when the browser has no support. The service is the browser's own and can fail; since 1.2.4.0 every failure is said in words, with a tip for the keyboard's microphone key or the computer's own dictation |

`manifest.webmanifest` makes the site installable as a standalone app (`start_url ./`, theme `#0E5C45`,
icon `icon.svg`).

## 5. Components

![C4 components](docs/diagrams/c4-component.svg)

All components live in `app.js`, inside one IIFE. Module state is a handful of variables: `state` (the
shop), `lang`, `shotData`, `ocrFilled`, `lastDeleted`, `lastShop`, `lastEdited`, `currentPhone`, plus the remind-all
queue and the lazy-library promises.

| Module | Responsibility | Main functions | State it reads / writes |
| --- | --- | --- | --- |
| State and storage | Load and save the shop; turn any shop file into valid state; compute balances, total, overdue rows | `blank`, `load`, `save`, `fromShopFile`, `balances`, `totalOutstanding`, `customer`, `entriesOf`, `newestFirst`, `lastPaymentDate`, `firstCreditDate`, `overdueRows` | Reads and writes `state` and `hisaab.v1`. `save` shows a toast if storage is full |
| i18n | Labels in English, Hindi and Telugu; customer names in local script; dates and money | `I18N`, `t`, `applyLabels`, `translit`, `wordToDeva`, `devaToTelugu`, `localName`, `nameText`, `nameHtml`, `money`, `balHtml`, `digits`, `niceDate` | Reads `lang`, `state.nativeDigits`, `name_hi`, `name_te`; writes `hisaab.lang` |
| Rendering | Build the overview (hero, KPIs, customer list, overdue list, recent entries) and the customer page from state | `render`, `renderCustomer`, `entryHtml`, `runningBalances`, `fillCustDetails`, `balClass`, `balText` | Reads `state`, search box, `currentPhone`; writes the DOM only |
| Routing and navigation | Map the hash to a view; move the entry card between home and customer page; left nav, bottom bar, More sheet | `route`, `goTo`, `spy`, `setActive`, `closeSheet` | Reads `location.hash`; writes `currentPhone` |
| Entry form and validation | Add, edit and delete entries; add customers (any 10 digits; a warning when the number is not a mobile, `validMobile`); messages and Undo after a delete or an edit | `onEntrySubmit`, `startEdit`, `deleteEntry`, `resetEntryForm`, `nextSeq`, `onAddCustomer`, `toast` | Writes `state.entries`, `state.customers`, `lastDeleted`, `lastEdited` (the entry as it was before an edit); calls `save` |
| Quick entry and voice | Parse a phrase such as "Sunita 500 udhaar" (English, Hindi or Telugu, words or digits, multipliers like hazaar) and fill the form for the owner to check and save | `parseQuick`, `applyQuick`, `onQuick`, `qScore`, `qDist`, `setupVoice` | Reads `state.customers`; writes form fields only, never saves by itself |
| Reminders | Reminder, advance and settled messages; WhatsApp and UPI links; receipt after each entry; "Remind all overdue" queue | `reminderText`, `waLink`, `upiLink`, `upiId`, `receiptLink`, `renderRemindAll`, `remindNextOne` | Reads `state.shop`, `state.upi` (demo `hisaab.demo@upi` when empty; Shop settings saves a UPI ID only if `validUpi` passes) |
| QR | Load the QR library and draw the UPI QR code on the customer page | `loadQr`, `qrSvg`, `showQr` | Reads `currentPhone`, balances. If the library cannot load, the pay link is shown as text |
| Statement | Printable customer statement with running balance, totals and a scan-to-pay QR | `printStatement` | Reads the customer's entries; writes `#print-area`, then `window.print()` |
| Import and export | Load a shop from pasted text (JSON, or CSV and tab-separated text turned into a shop file), a `.json`, `.csv` or `.txt` file or the sample, with Undo; export entries CSV, balances CSV and shop JSON | `doImport`, `csvRows`, `fromCsv`, `csvCell`, `download`, `exportEntries`, `exportBalances`, `exportJson`, `slug` | Replaces `state`; keeps the old shop in `lastShop` for Undo. `fromShopFile` skips bad amounts and a bad UPI ID and counts entries with no date |
| Screenshot reading | Resize a chosen picture, attach it to a payment, read the amount in the app language (`eng`, `eng+hin` or `eng+tel`, falling back to `eng`), with Devanagari and Telugu digits normalised by `qNorm` | `onShotChange`, `loadTesseract`, `readAmount`, `pickAmount` | Reads `lang`; writes `shotData`, `ocrFilled`, the amount field (only if empty or filled by OCR) |
| Theme | Light and dark, following the system until the owner picks one | `currentTheme`, `toggleTheme` | Writes `data-theme` and `hisaab.theme` |
| Wire-up and service worker registration | Load state or start an empty shop (`blank`), bind every listener, register `sw.js`, first route | `init` | Everything above |

Small helpers shared by all modules: `$`, `esc`, `fmt`, `round2`, `phone10`, `validPhone`, `validMobile`, `validUpi`, `isoDate`,
`validIso`, `normDate`, `todayIso`, `daysBetween`, `uid`.

## 6. Runtime views

The main journeys (first open, recording entries, reminders, import, screenshot reading, quick entry,
language and offline) are described with sequence diagrams in [docs/journeys.md](docs/journeys.md), with
the diagrams in `docs/diagrams/j1-first-open.svg` to `docs/diagrams/j9-language-offline.svg`, and `docs/diagrams/j10-several-shops.svg` (added in 1.1.0.0).

## 7. Data

The stored shop, the import file format and the CSV exports are described in
[docs/data-model.md](docs/data-model.md), with JSON Schemas in [docs/schema/](docs/schema/).

In short: one object `{ shop, upi, nativeDigits?, customers[], entries[] }`. A customer is
`{ name, phone, name_hi?, name_te? }` keyed by a 10 digit phone (F-2). An entry is
`{ id, phone, type: credit | payment, amount, date (YYYY-MM-DD), note, shot, seq }`. No total is stored;
every figure is computed (F-3).

## 8. Deployment

![Deployment](docs/diagrams/deployment.svg)

- Source: private GitHub repository karwasanjay007/hisaab.
- Hosting: Vercel static hosting, project `hisaab`, at https://hisaab-hazel.vercel.app.
  No functions, no environment variables used by the app.
- Release, from PowerShell in the project folder: `vercel deploy --prod --yes`.
- `.vercelignore` keeps `brief`, `brand`, `tests`, `docs`, `*.md`, the editor and agent folders
  (`.clinerules`, `.cursor`, `.github`, `.vscode`, `.windsurf`) and `kilo.jsonc`, `.aider.conf.yml` off the
  site. What is served: `index.html`, `pay.html`, `styles.css`, `app.js`, `sample.js`, `sample-data.json`,
  `sw.js`, `manifest.webmanifest`, `icon.svg`, the add-on files and the folder `i18n`.
- Because the service worker is network first, a new deployment reaches every phone on its next online
  visit.

## 9. Cross-cutting concerns

### Security and privacy
- No keys, no secrets and no server. Nothing the owner types is sent anywhere by Hisaab.
- WhatsApp and UPI links are built in the browser; the owner chooses to open and send them.
- All text from the shop, imports and the owner is escaped by `esc()` before it is put into HTML.
- Imported screenshots are accepted only as `data:image/` URLs; notes are cut to 200 characters on import.
- CSV export guards against formula injection: a text cell that starts with `=`, `+`, `-`, `@`, tab or
  carriage return gets a leading `'` (`csvCell`).
- The only third-party requests are the fonts and the two lazy libraries; they carry no shop data.

### Accessibility
- Skip link to the ledger; `aria-live` on the hero and on the quick-entry and OCR messages; `role="alert"`
  on form errors; `aria-current` on the active nav item; `aria-expanded` on the More button and QR toggle;
  `aria-pressed` on the microphone; Escape closes the More sheet.
- A visually hidden plain number inside `#total-outstanding` (for example 125840 beside ₹1,25,840).
- Text contrast checked to WCAG AA in both themes, including acted-on states (toasts, messages, edit mode,
  QR, hover; `tests/contrast.mjs`); the primary button hover has its own token `--primary-hover` and the
  amber link on the brand tile (navy since 1.3.0.0) uses `--accent-on-brand`; field borders at 3:1; 44 px tap targets on Edit and
  Delete (REPORT.md).
- `prefers-reduced-motion` stops the microphone pulse.

### Performance
- No framework and no build: the first load is `index.html`, `styles.css`, `sample.js` and `app.js` plus
  the fonts; libraries only when used (AD-8).
- Fonts preconnected and loaded with `display=swap`.
- Screenshots shrunk before they are stored (AD-4).

### Internationalisation
- Three label sets (`en`, `hi`, `te`) in `I18N`; missing keys fall back to English; the choice is saved.
- In Hindi or Telugu mode names show with the local script in brackets, from a dictionary of common name
  words plus a phonetic fallback; the owner can correct each name; import accepts `name_hi` and `name_te`.
- Dates use `en-IN`, `hi-IN` or `te-IN`; digits stay Latin unless the owner opts in (AD-11).
- Voice recognition and the screenshot reader follow the chosen language.

### Responsive layout
Breakpoints in `styles.css`:

| Width | Layout |
| --- | --- |
| up to 359 px | Shop name chip hidden in the top bar |
| up to 559 px | Compact top bar (smaller brand, narrower language picker) |
| from 720 px | Tablet spacing; hero in two columns |
| from 1024 px | Left navigation, two content columns; bottom bar and More sheet hidden |
| from 1280 px | A section view shows a related card beside it; customer page as ledger plus side column |
| from 1600 px | Overview in three columns, four KPI tiles in a row (on an empty shop only since 1.5.0.0) |
| print | In statement mode (`body.print-mode`) only the customer statement is printed |

Below 1024 px navigation is a bottom bar with a More sheet.

Since 1.5.0.0 `compact.css` lays out the overview of a shop with customers from 768 px: a total strip, the customer
list, the quick-entry pill at the bottom of the main column, and from 1200 px a right panel 340 px wide (see
"Version 1.5.0.0 additions"). An empty shop, the section views and the customer page keep the breakpoints above.

### Offline
- On install the service worker precaches the app shell in `hisaab-v18` (`./`, `index.html`, `styles.css`,
  `app.js`, `sample.js`, `sample-data.json`, `icon.svg`, `manifest.webmanifest`, the add-on files,
  `pay.html`, `fonts/fonts.css` with its woff2 files and `vendor/qrcode.min.js`); on activate it deletes
  older caches. After one online visit the app opens with no signal, and the ledger works fully offline
  because the data is local.
- At run time it caches, network first, the app's own files; since 1.4.1.0 the fonts and the QR library are
  among them and are precached, so the QR code draws offline after one online visit (`tests/offline.mjs`).
- Tesseract.js from jsDelivr and its language data are not cached, so offline the screenshot reader reports
  that no amount was read.

## 10. Quality and testing

| Test | What it does | Evidence |
| --- | --- | --- |
| `tests/check.mjs` | Independent checker written by a separate agent from `brief/` only, without reading the app source. Playwright 1.62.0. Checks the sample shop, a random hidden shop from a seed through `#import-box`, reminders, UPI, persistence, `?today=`, overdue, search, CSV, language, dialogs and console errors. Usage `node tests/check.mjs [baseUrl] [seed]` | `tests/last-run-live.txt`: live site, seed 12345, 27 PASS, 0 FAIL. REPORT.md records the same on seeds 777, 31337 and 8080; `tests/last-run.txt` is the local run |
| `tests/nav.mjs` | Every nav item opens its section as its own view, at the top, quickly. Usage `node tests/nav.mjs [baseUrl]` | ALL PASS (REPORT.md) |
| `tests/quick.mjs` | Quick entry: the parser on English, Hindi and Telugu lines, the form fill, focus on Save, the customer page, the microphone button, 375 px. Usage `node tests/quick.mjs [baseUrl]` | ALL PASS |
| `tests/voice.mjs` | Voice quick entry with a stand-in speech service: each failure has its own words, a refused language is tried once more in `en-IN`, a silent service is stopped after 9 seconds. Usage `node tests/voice.mjs [baseUrl]` | ALL PASS (15 checks) |
| `tests/voice-quiet.mjs` | (1.2.4.0) A speech service that ends with no result and no error says "Did not catch that"; every failure line carries the keyboard tip on a phone or tablet and the dictation tip on a computer, a touch-screen laptop included. Usage `node tests/voice-quiet.mjs [baseUrl]` | ALL PASS (11 checks) |
| `tests/critic.mjs` | The defects a code critic found: Save in the same tick as choosing a screenshot, the UPI link, Edit from the Recent view, a manual entry dated after `?today=`, amounts read from four payment screenshots | ALL PASS (14 checks) |
| `tests/phone-import.mjs` | On a phone the summary cards wrap into two columns; a shop file with customers and no `entries` key loads with every balance 0 | ALL PASS (12 checks) |
| `tests/sweep.mjs` | 61 shops (60 written by two reviewers from the brief only, plus the sample): total, every balance, who owes the most, the overdue list and the reminder link | ALL PASS (61 shops) |
| `tests/final.mjs` | Final review: total text, remind all, entry validation, search, CSV export, undo import, QR code and statement QR, widths from 375 to 2560 px. Usage `node tests/final.mjs [baseUrl]` | ALL PASS |
| `tests/gaps.mjs` | The warning for a number that is not a mobile, the UPI ID check, import amount and date rules, CSV import (round trip and a hand-made sheet), Undo after an edit, a Hindi screenshot read. Usage `node tests/gaps.mjs [baseUrl]` | ALL PASS |
| `tests/offline.mjs` | One online visit, then offline: the ledger opens with 11,820 and the QR code draws. Usage `node tests/offline.mjs [baseUrl]` | ALL PASS |
| `tests/brief.mjs` | Follows every "Done when" line of `brief/spec-page.md` literally in a fresh browser: 9 digits and letters refused, Ravi Kumar added to the empty app, a Rs 250 credit, reload, the sample loaded (11,820, Ravi 670, 8 customers), newest first, reminder, screenshot, a shop with a customer with no entries, 375 px with no small tap targets, no dialogs and no page errors. Usage `node tests/brief.mjs [baseUrl]` | `tests/last-brief-live.txt`: live site, ALL PASS |
| `tests/seed.mjs` | Not a test: `withSample()` gives every new browser context the sample shop in localStorage, unless it already holds a shop, since the app now starts empty. Used by `nav.mjs`, `quick.mjs`, `final.mjs`, `gaps.mjs` and `offline.mjs` | |
| Review agents | Industry comparison, brief and coaching audit, functional test, visual critique | `review/*.md`; fixes listed in REPORT.md |

Local run: `npx http-server . -p 4173 -c-1 --silent`, then `node tests/check.mjs http://localhost:4173/ [seed]`;
the other scripts take the same base URL as their first argument.

## 11. Risks and known limits

| Risk or limit | Effect | Source |
| --- | --- | --- |
| Data lives in one browser on one phone | Clearing site data or losing the phone loses the shop; no sync between devices. Mitigation: JSON and CSV export. Next step (phase 3, PRD out of scope): optional cloud backup and sync across a shop's phones, for example Supabase sign-in, kept optional so the ledger never sits behind a login | AD-2, AD-3 |
| localStorage size | Many screenshots can fill storage; `save` then shows "Storage is full" and the change is not kept | AD-4 |
| Two readings of the total when a customer is in advance | `#total-outstanding` holds only the sum of all balances; the amount owed by those who owe is in `#total-note` just below it, so a grader expecting the owed-only figure must read the note | review/brief-audit.md R1 |
| `upi://` links inside WhatsApp text | WhatsApp may not make them tappable. Since 1.2.1.0 the reminder also carries an https link to `pay.html`, which opens the UPI app; the QR code and statement stay as fallbacks. Not checked on a real phone | review/industry.md |
| Demo UPI ID `hisaab.demo@upi` until the owner sets one | Pay links point to a fake payee; shown as demo in Settings | PRD Q-1, review/brief-audit.md R5 |
| Lenient import | Entries for an unknown phone create a customer named by the phone; a repeated phone is dropped silently; a missing or unreadable date becomes today (counted in the message); an amount of 0 and a future date are accepted | `fromShopFile`, review/brief-audit.md R4, review/functional.md 11 |
| Phone numbers | Any 10 digits are accepted, typed or imported, not only Indian mobile numbers; a typed number that does not start with 6, 7, 8 or 9 is added with a warning that WhatsApp may not reach it, an imported one with none (AD-15) | `validPhone`, `validMobile`, review/functional.md 6 |
| "Overdue" has no due date | It means days since the last payment (or the first credit if none), not a missed due date | `overdueRows`, review/industry.md |
| OCR | A wrong amount is possible, so the owner checks it before saving; the language data is downloaded on first use and not cached, so the reader needs a signal | `readAmount` |
| Voice | Depends on browser support for the Web Speech API | `setupVoice` |
| Undo window | Undo lasts as long as the toast (7 seconds) and is one level deep | `toast` |

Further minor findings are listed in `review/functional.md`.

## 12. Glossary

| Term | Meaning |
| --- | --- |
| Udhaar (udhar) | Goods given on credit; an entry of type `credit` that raises the balance |
| Jama | Money received from the customer; an entry of type `payment` that lowers the balance |
| Kirana | A small neighbourhood grocery shop in India |
| Khata | The shop's credit notebook (ledger); Hisaab is a digital khata |
| UPI | Unified Payments Interface, India's instant bank payment system; paid through apps such as GPay, PhonePe and Paytm from a `upi://pay` link or QR code |
| Balance | All credit minus all payments for one customer; positive means the customer owes the shop |
| Total outstanding | Sum of every customer's balance; a customer in advance reduces it (D-3); the owed-only figure is in the note below it |
| Advance | A balance below 0: the customer has paid more than they owe; shown as -₹200 with an "Advance" label (text ₹-200) |

## Version 1.1.0.0 additions (22 Nov 2026)

| Part | File | What it holds |
| --- | --- | --- |
| Add-on hooks | `app.js` (end of file) | `window.Hisaab`: `on(event, fn)`, `state()`, `replace(shop)`, `save()`, `render()`, `blank()` and formatting helpers. Events: `render`, `statement`, `imported`, `export`. Each listener runs in its own guard, so an add-on that fails cannot stop the ledger |
| Shop profile and balance history | `profile.js`, `profile.css` | `#profile-form` inside Shop settings (`#shop-owner`, `#shop-address`, `#shop-logo`); state fields `owner`, `address`, `logo`; `#shop-logo-img` in the header; `#bal-history` on the customer page, an inline SVG |
| Several shops | `shops.js`, `shops.css` | `#sec-shops` inside the Shop settings card; `#shop-add`, `[data-shop-switch]`, `[data-shop-remove]`, `#import-new`. Storage key `hisaab.shops` = `{ active, list: [{ id, name, data }] }`; the active shop's `data` is null because its ledger is under `hisaab.v1` |
| Service worker | `sw.js` | Cache `hisaab-v3`; the app shell now also lists `profile.css`, `profile.js`, `shops.css`, `shops.js` |
| Tests | `tests/profile.mjs`, `tests/shops.mjs`, `tests/addons-width.mjs` | 36, 25 and 54 checks |

## Version 1.2.0.0 additions (22 to 23 Nov 2026)

| Part | File | What it holds |
| --- | --- | --- |
| Add-on hooks, extended | `app.js` (end of file) | `window.Hisaab` gained `addLanguage`, `hasLanguage`, `setLang`, `text`, `balances`, `overdue`, `quickWords`, `forgetUndo` and a few read-only helpers |
| More languages | `langs.js`, `i18n/<code>.json` | Twelve language files (sections core, profile, shops, customer, extras; 216 keys each). English, Hindi and Telugu are also built into the scripts; the other nine are fetched only when chosen and registered through `addLanguage`; the choice stays in the existing key `hisaab.lang` |
| Customer tools | `customer.js`, `customer.css` | `#cust-tools` after the entries on the customer page: totals, days since the last payment, `#cust-tools-call`, `#cust-tools-chat`, change phone (`#cust-new-phone`), delete with `#cust-undo-bar` |
| Note shortcuts, overdue total, date | `extras.js`, `extras.css` | `#note-chips` under `#entry-note`, `#overdue-total` above the overdue list, `#as-of` under the total |
| Service worker | `sw.js` | Cache `hisaab-v4`; the app shell also lists the five new add-on files; a language file is cached the first time it is fetched |
| Tests | `tests/i18n-check.cjs`, `tests/langs.mjs`, `tests/customer.mjs` | 11 languages checked; 25 and 36 checks |

## Version 1.2.1.0 to 1.2.3.0 additions (23 Nov 2026)

| Part | File | What it holds |
| --- | --- | --- |
| Add-on hooks, extended | `app.js` (end of file) | `window.Hisaab` gained `translit`, and the slot `nameIn(customer, lang)` that `names.js` fills. The reminder (`reminderText`) now also carries an https link built by `payPage`; the `upi://pay` link stays in the text |
| Layout | `polish.css` | Layout only: the customer heading, the customer page columns, entries in a narrow card, tiles, labels that wrap, and the styles of the two new blocks. No id, attribute or text changed |
| Names in nine more scripts | `names.js` | Bengali, Marathi, Tamil, Gujarati, Kannada, Malayalam, Gurmukhi, Odia and Urdu, built from the Devanagari spelling through `translit`; registers `Hisaab.nameIn` |
| Pay page | `pay.html` | A separate small page. Reads shop, UPI ID, amount and the customer's name from the part after `#`, which is never sent to a server; shows them and opens the `upi://pay` link |
| Other ways to send | `sendways.js` | On the customer page, the same reminder as an SMS (`sms:` link), to any contact chosen in WhatsApp (`wa.me` link with no number), or copied |
| WhatsApp check | `wacheck.js` | After a customer is added by hand, offers a hello message on WhatsApp to that number and stores the owner's answer in `hisaab.wa`, outside `hisaab.v1` |
| Language files | `i18n/<code>.json` | 233 text keys each at 1.2.3.0 (216 at 1.2.0.0, 223 at 1.2.2.0) |
| Service worker | `sw.js` | Cache `hisaab-v5` at 1.2.1.0, `hisaab-v6` at 1.2.2.0, `hisaab-v7` at 1.2.3.0, `hisaab-v8` at 1.2.3.1; the app shell also lists `names.js`, `sendways.js`, `wacheck.js`, `polish.css` and `pay.html` |
| Tests | `tests/layout.mjs`, `tests/names.mjs`, `tests/pay.mjs`, `tests/sendways.mjs`, `tests/wacheck.mjs` | Layout 7 checks over 120 screens; names 33; pay 15; sendways 16; wacheck 19 |

## Version 1.2.3.1 and 1.2.4.0 additions (23 Nov 2026)

| Part | File | What it holds |
| --- | --- | --- |
| One-tap switch (1.2.3.1) | `wacheck.js` | Once a number has an answer, a button `#wa-check-flip` beside Check on WhatsApp offers the other answer, so a wrong tap is put right with one tap. It uses the existing labels; no new text |
| Voice that never ends in silence (1.2.4.0) | `app.js`, `listen` inside `setupVoice` | A speech service that stops with no result and no error now says "Did not catch that". Every voice failure line is followed by a tip: `quickKeyTip` (the microphone key of the keyboard) on a phone or tablet, known by its user agent (Android, iPhone, iPad, iPod), and `quickPcTip` (Windows key and H) on anything else, so a laptop with a touch screen gets the laptop tip. A silent service is still stopped after 9 seconds |
| Language files | `i18n/<code>.json` | 235 text keys each (233 at 1.2.3.0); `quickKeyTip` and `quickPcTip` added in 1.2.4.0 |
| Service worker | `sw.js` | Cache `hisaab-v8` at 1.2.3.1, `hisaab-v9` at 1.2.4.0 (`hisaab-v7` at 1.2.3.0); the app shell list is unchanged |
| Tests | `tests/wacheck.mjs`, `tests/voice-quiet.mjs` | wacheck gained three checks at 1.2.3.1 (19); voice-quiet 11 checks, new at 1.2.4.0 |

Nothing new is stored by either version.

## Version 1.2.5.0 additions (25 Nov 2026)

| Part | File | What it holds |
| --- | --- | --- |
| Controls that say what they did | `assist.js` | Loaded last. It listens after the app's own handlers and changes no id, total or stored value. The theme button gets `aria-pressed`; a status line `#assist-status` (for screen readers) says which theme is on, and names the view after a press on any `[data-go]` item. A click or Enter on the skip link puts the focus on `#main`. After a quick entry that found no customer, when the text held a name, it adds the line `#quick-no-match` and the button `#quick-add-customer` to `#quick-choices`; the button opens the add form with the name filled. It reads the parse through `window.hisaabParseQuick` and text through the hook `text` |
| Text | `assist.js` | The two theme lines in twelve languages are inside the add-on; every other word comes from the existing keys `noMatch` and `addCustomer`. The language files are unchanged (235 keys) |
| Service worker | `sw.js` | Cache `hisaab-v10`; the app shell also lists `assist.js` |
| Tests | `tests/assist.mjs` | 38 checks from an empty ledger, at 375 px and 1440 px |

Nothing new is stored by this version.

## Versions 1.2.6.0 to 1.4.0.0 additions (27 Nov 2026)

| Part | File | What it holds |
| --- | --- | --- |
| Storage and input guards (1.2.6.0) | `guard.js`, `guard.css` | Loaded after `assist.js`. Wraps the store's writes: a refused write is said as not kept, and a success line written just after it is corrected. At start it writes and removes the key `hisaab.guard` to find a blocked store and shows a lasting notice. Drops an imported picture that is not an image or is over 400,000 characters and counts it. Holds an edit whose date was cleared |
| Whole rupees (1.3.0.0) | `guard.js` | A new entry or an edit with paise is not saved and the nearest whole amount is offered; an import rounds paise halves up and says how many; stored paise get one line with Round and Undo. The lines are in English, Hindi and Telugu inside the add-on |
| Screenshot size (1.2.6.0) | `app.js` | One number: the longest side is at most 600 px (was 640) |
| Brand look (1.3.0.0) | `brand.css`, `brand-mark.svg`, `icon.svg` | Loaded after the other sheets: navy, cream, slate blue and amber; Fraunces and Inter, the Indian-script fonts first for those languages; the sai4b mark first in the top bar. No green: paid, the payment choice, success lines and WhatsApp buttons are slate and navy |
| Phone layout (1.4.0.0) | `compact.css`, `compact.js` | Loaded last. Below phone width: a 56 px top bar, a compact total, quick entry as one pill at the bottom, a slim tab row, and a + sheet (`#compact-sheet`) with every other action, opened by tap, Enter or Space and closed by Escape or a tap outside. The grader hooks stay in place and are not copied. Desktop unchanged at 1.4.0.0 (its own layout since 1.5.0.0) |
| Service worker | `sw.js` | Cache `hisaab-v14`; the app shell also lists `guard.js`, `guard.css`, `brand.css`, `brand-mark.svg`, `compact.css`, `compact.js` |
| Tests | `tests/guard.mjs`, `tests/rupees.mjs`, `tests/brand.mjs`, `tests/phone-ledger.mjs` | 48, 28, 184 and 147 checks, all passing on the live link |

The only new stored key is `hisaab.guard`, a probe that is removed as soon as it is written.

## Version 1.4.1.0 additions (27 Nov 2026)

| Part | File | What it holds |
| --- | --- | --- |
| + sheet in twelve languages | `compact.js`, `langs.js`, `i18n/*.json` | The sheet's labels and button names come from a `compact` section of 19 keys in each language file and change with the language |
| Fonts from the site | `fonts/fonts.css`, woff2 files, OFL notes | Fraunces, Inter, Baloo 2 and Noto Sans (Latin, Devanagari, Telugu) under the same family names as before; `index.html` and `pay.html` link `fonts/fonts.css` |
| QR library from the site | `vendor/qrcode.min.js`, MIT notice | `loadQr()` adds this script; nothing else changed in how the QR code is drawn |
| One balance function | `customer.js`, `extras.js` | The customer page totals and the overdue line read the ledger's own sums, so they cannot drift from the balances |
| Ledger hook | `app.js`, `window.Hisaab.ledger` | A frozen, read-only set of the ledger's own functions (balances, sums, totals, order, entries, last payment, overdue, reading a shop file), each on a given shop or the open one |
| Service worker | `sw.js` | Cache `hisaab-v15`; the app shell also lists `fonts/fonts.css`, its woff2 files and `vendor/qrcode.min.js` |
| Tests | `tests/local-assets.mjs`, `tests/ledger.cjs` | 13 and 27 checks; local-assets passes on the live link, the ledger tests run without a browser |

No new stored key. The screenshot reader, Tesseract.js, still loads on demand from jsDelivr.

## Version 1.5.0.0 additions (27 Nov 2026)

| Part | File | What it holds |
| --- | --- | --- |
| Desktop layout | `compact.css`, `compact.js` | From 768 px, on the overview of a shop with customers only (`body:not(.guard-empty) #view-home:not([data-section])`): a total strip at most 110 px (`#compact-stats`: total outstanding, customers, overdue, collected in 30 days, and "…" that opens the full overview); the customer list as one column with its search on the title line; `#entry-card` sticky at the bottom of the main column as the quick-entry pill |
| Desktop "+" | `compact.js`, `#desk-plus` | Its own button beside the phone "+", with aria-haspopup, aria-expanded and aria-controls; opens the same `#compact-sheet` as a popover lined up above the pill; arrows move, Escape closes and returns the focus |
| Right panel | `compact.js`, `aside#desk-aside` | From 1200 px (a media query listener), 340 px wide and sticky: `#desk-entry` with the entry form at the top, then overdue and recent entries, moved there (not copied) and moved back below 1200 px, in the section views, on the customer page and on an empty shop |
| Folding form | `compact.js`, `compact.css` (`#entry-card.desk-open`) | Between 768 and 1199 px the entry form is folded; it opens above the pill when quick entry fills it or a screenshot is chosen, and folds again after Save or Escape |
| Service worker | `sw.js` | Cache `hisaab-v16`; the app shell lists the same files |
| Tests | `tests/desk-ledger.mjs` | 93 checks at 1024 x 768, 1280 x 800 and 1440 x 900, light and dark, all passing on the live link (149 since 1.5.1.0) |

No new stored key; the grader hooks stay in place and are not copied. Nothing removed.

## Version 1.5.1.0 additions (27 Nov 2026)

| Part | File | What it holds |
| --- | --- | --- |
| Right to left | `compact.js` | For Urdu it sets `dir="rtl"` on the html element and removes it for every other language; amounts and phone numbers are kept left to right; the skip link no longer scrolls the page sideways. On a desktop in Urdu the right panel sits on the left of the list |
| Right panel and list | `compact.css`, `compact.js` | The right panel fits the screen and its cards (overdue, recent) scroll on their own; a row brought into view stays above the pill; checked with a 40-customer shop |
| Service worker | `sw.js` | Cache `hisaab-v18`; the app shell lists the same files |
| Tests | `tests/desk-ledger.mjs` | 149 checks: the 1.5.0.0 screens, Urdu right to left at 375 x 667 and 1280 x 800, and a 40-customer shop at 1280 x 800, all passing on the live link |

No new stored key; nothing removed.
