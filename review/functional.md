# Hisaab functional test report

Tested 20 November 2026 against the live app at https://hisaab-hazel.vercel.app/?today=2026-11-15 with Playwright (Chromium), at 1440x900 and 375x812, in light and dark colour schemes. Every result below was observed in the browser by script, not inferred from the code. Scripts and raw output are in `C:\Users\sskar\AppData\Local\Temp\claude\a153659dba92c228\3568ccd8-320a-4379-8379-a3641d808a21\scratchpad\final\qa\` (`qa1.mjs`, `qa2.mjs`, `qa1.txt`, `qa2.txt`, plus JSON results).

Summary: no blockers. 3 major and 14 minor bugs. The core ledger (balances, totals, KPIs, overdue, running balance, receipt and reminder links, print, export, JSON import, navigation, persistence) works.

## What passed

| Area | Check | Result |
|---|---|---|
| Sample data | Total outstanding 11820, 8 customers owing, top defaulter Sunita (9000000102), every balance matches `brief/data/sample-expected.json` | Pass |
| Date override | `?today=2026-11-15` pins the entry date field and KPI window | Pass |
| Entry validation | No customer, empty, zero and negative amounts rejected with a message; nothing saved | Pass |
| Decimals | 12.345 stored as 12.35 | Pass |
| Record credit | 700 to Kavitha: total 11820 to 12520, her balance 1000, top of recent list, toast with "Send receipt" link `https://wa.me/919000000108?text=...` | Pass |
| Record payment | Payment of 1000 clears Kavitha: customer page shows 0, running balance column correct (0, 1000, 300, 180, 960, 780), UPI button hidden, she leaves the overdue list, owing count 8 to 7 | Pass |
| Advance | Overpayment gives advance text in reminder and "net after advances" note on the total | Pass |
| WhatsApp links | Owing, zero and advance balances all give `https://wa.me/91<phone>?text=<encoded>` with the right message and amount | Pass |
| Edit and delete | Edit loads the entry into the form and updates it; delete then Undo restores the entry | Pass |
| Statement | Print button calls `window.print` (stubbed) with the customer's statement rows and totals in `#print-area` | Pass |
| Add customer | Empty and spaces-only name, duplicate phone, 9 and 11 digit phones rejected; 40-character name, Hindi name saved; HTML in a name is escaped; no overflow at 375 px with the long name; new customer appears in the picker | Pass |
| Edit customer | Empty name rejected; name and Hindi name saved and shown in the header | Pass |
| Search | By name, case-insensitive, by part of a phone, by a Hindi-script name | Pass |
| Export | Entries CSV (header and row count match), balances CSV (Sunita 3470, TOTAL OUTSTANDING 11820), JSON backup; JSON backup re-imports to the same totals | Pass |
| Import rejects | Malformed JSON, wrong shape, empty box, a bare array, CSV text, all-invalid rows: each rejected with a message, stored data unchanged | Pass |
| Import file | JSON chosen through the file input loads | Pass |
| Screenshot | Button opens a file chooser; a non-image file gives "That file is not a picture." | Pass |
| Settings | Shop name saved and shown in header; blank shop name keeps the old one | Pass |
| Language | हिंदी and తెలుగు translate the visible labels, `html lang` follows, customer names show local script in brackets, reminder text is translated, native digits toggle appears outside English and converts amounts and dates | Pass |
| Persistence | Theme, language and shop name survive a reload | Pass |
| Navigation | Every left-nav item (desktop) and bottom bar plus More sheet item (phone) routes to its own hash; Back and Forward move through the views and the active item follows; customer page then Back returns to Customers; unknown hash and unknown customer deep link fall back to home | Pass |
| Layout | No horizontal overflow on any view at 1440 or 375 px, light or dark; last button and the toast clear the phone bottom bar | Pass |
| Console | No console errors or page errors on any view or flow | Pass |
| Keyboard | Skip link first; Tab reaches the customer picker in 16 stops with a visible focus ring on each; picker, type radios, amount and Enter save an entry; toast is `role=status`, form message `role=alert` | Pass |

## Bugs

### 1. Major: Load sample and Import wipe the whole ledger with no confirmation and no undo

- Repro: add a customer (Add customer, "Temp", 9876500011). Open Shop file, press "Load sample" (or paste any valid file and press Load).
- Expected: a confirmation that all current customers and entries will be replaced, or an undo, or an automatic backup download first.
- Actual: no dialog; the ledger is replaced at once (customers back to the sample 8) and Temp is gone for good.
- Likely location: `app.js` `doImport()` (around line 852, `state = res.state; save();`) and the `load-sample` handler in `init()` (around line 958).

### 2. Major: no upper limit on an amount; huge values corrupt totals and lose precision

- Repro: Record, customer Ravi Kumar, Credit, amount `99999999999999`, Save. Then amount `1e21`, Save.
- Expected: a sensible maximum (for example ₹1,00,00,000) with an error message.
- Actual: both saved. Ravi's balance shows `₹10,00,00,00,00,00,681.84` (paise wrong because of floating point), and after `1e21` the total outstanding reads `₹1,00,00,00,09,99,99,99,99,00,000`. One typo makes every KPI and export meaningless.
- Likely location: `app.js` `onEntrySubmit()` (around line 723, `if (!isFinite(amount) || amount <= 0)`); same in `fromShopFile()` for imports.

### 3. Major: future dates are accepted, and then counted in balances but not in the 30-day KPIs

- Repro: Record, customer Sunita Devi, Credit, amount 500, date 2027-03-01, Save.
- Expected: dates after today are refused (or at least warned), as the date input has no `max`.
- Actual: saved silently. Sunita's balance and the total go up by 500, but "Given in 30 days" ignores it (`daysBetween` is negative), so the KPI tiles and the total disagree. The overdue day count is clamped to 0 for such entries.
- Likely location: `app.js` `onEntrySubmit()` (no check against `todayIso()`), `resetEntryForm()` (no `max` set on `#entry-date`), KPI loop in `render()` (`d >= 0 && d < 30`).

### 4. Minor: amounts below ₹1 are saved although the field says `min="1"`

- Repro: Record, Ravi Kumar, amount 0.5, Save.
- Expected: consistent rule; either refused (field minimum is 1) or the field allows it.
- Actual: saved as ₹0.5 while the field reports itself invalid.
- Likely location: `index.html` line 139 (`min="1"`) versus `onEntrySubmit()` (`amount <= 0`).

### 5. Minor: phone field `maxlength="14"` truncates the common "+91 98765 43210" format

- Repro: Add customer, name "Dup2", type `+91 90000 00101` (15 characters), Add.
- Expected: recognised as 9000000101 and refused as a duplicate (the code strips a leading 91).
- Actual: the last digit is cut off by `maxlength`, giving "Enter a 10 digit phone number." The 12-digit stripping in `phone10()` can only work if the user leaves out the spaces.
- Likely location: `index.html` line 177 (`maxlength="14"`).

### 6. Minor: phone numbers that are not Indian mobiles are accepted

- Repro: Add customer "Starts with 1", phone 1234567890.
- Expected: Indian mobile numbers start with 6, 7, 8 or 9; others refused, since the number is used for WhatsApp.
- Actual: "Customer added."; the reminder link goes to `wa.me/911234567890`.
- Likely location: `app.js` `validPhone()` (line 291, `/^\d{10}$/`).

### 7. Minor: search with no match shows an empty list and no message

- Repro: Customers, search `zzzz`.
- Expected: a "No customer matches" line.
- Actual: blank list; only the count changes to "0 / 13". `#customer-empty` stays hidden because it only checks whether any customers exist.
- Likely location: `app.js` `render()` (around line 500, `$('customer-empty').hidden = state.customers.length > 0`).

### 8. Minor: search by phone fails when the number includes +91 or spaces copied from the customer page

- Repro: Customers, search `+91 90000 00101` (the format shown on the customer page).
- Expected: Ravi Kumar.
- Actual: no result; the digits become `919000000101` which is not found. Mixed text such as "Ravi 9000" matches every customer through the digit part.
- Likely location: `app.js` `render()` search block (`qd = q.replace(/\D/g, '')`); strip a leading 91 or 0 with `phone10()` and require both parts to match.

### 9. Minor: search ignores the local-script names shown in brackets

- Repro: switch to हिंदी, Customers, search `रवि`.
- Expected: Ravi Kumar, whose row reads "Ravi Kumar (रवि कुमार)".
- Actual: no result; search only looks at `c.name`.
- Likely location: `app.js` `render()` (`c.name.toLowerCase().indexOf(q)`); include `localName(c)`.

### 10. Minor: balances CSV and entries CSV are open to spreadsheet formula injection

- Repro: Add customer named `=HYPERLINK("http://x","y")`, phone 9876500009. Shop file, Export balances.
- Expected: cells starting with `=`, `+`, `-` or `@` are prefixed with `'` so Excel treats them as text.
- Actual: row is `"=HYPERLINK(""http://x"",""y"")",9876500009,0,0,0,` which Excel runs as a formula.
- Likely location: `app.js` `csvCell()` (line 872).

### 11. Minor: import accepts blank and zero amounts, impossible dates and unreadable dates

- Repro: Shop file, paste `{"shop":"T2","customers":[{"name":"A","phone":"9876543210"}],"entries":[{"phone":"9876543210","type":"credit","amount":"","date":"2026-11-01"},{"phone":"9876543210","type":"credit","amount":0,"date":"31/02/2026"}]}`, Load. Also try `"date":"yesterday"`.
- Expected: blank and zero amounts skipped like negative ones; 31/02/2026 and "yesterday" reported as bad dates.
- Actual: "Loaded T2: 1 customers, 2 entries." with amounts 0 and 0; 31/02/2026 stored as `2026-02-31`; "yesterday" silently becomes today. Entry form rejects zero, so import and form disagree.
- Likely location: `app.js` `fromShopFile()` (`amount < 0` should be `amount <= 0`; `Number('')` is 0) and `validIso()` (`Date.parse` rolls 31 Feb over to 3 March; compare the round trip).

### 12. Minor: no CSV import, and the file chooser only offers .json

- Repro: Shop file, Choose file. Or paste `date,name,phone,type,amount` rows and Load.
- Expected (if CSV import is in scope, as the entries export is CSV): CSV rows imported.
- Actual: picker filters to `.json,application/json`; pasted CSV gives "This is not valid JSON". A shopkeeper cannot re-import the entries CSV the app itself exports.
- Likely location: `index.html` line 247 (`accept`), `app.js` `doImport()` (JSON only).

### 13. Minor: UPI ID is saved without any format check

- Repro: Settings, UPI ID `not a upi`, Save.
- Expected: refused unless it looks like `name@bank`.
- Actual: "Settings saved."; every reminder then carries a broken `upi://pay?pa=not%20a%20upi` link.
- Likely location: `app.js` settings submit handler in `init()` (around line 967).

### 14. Minor: some text stays English after switching language

- Repro: Settings, Save (shows "Settings saved."), then switch to हिंदी or తెలుగు.
- Expected: all labels translated.
- Actual: "Settings saved." stays English until saved again (message is not re-rendered); footer "Built by" and "Smart AI for Business"; aria-labels "Hisaab home", "Language", "Switch light or dark", "Sections" are English for screen readers; import "Skipped:" prefix and the "Storage is full..." toast are hard-coded English. (UPI, CSV, JSON, PDF and WhatsApp left as is are fine.)
- Likely location: `app.js` `doImport()` (`' Skipped: '`), `save()` (line 346), `applyLabels()`; `index.html` lines 56, 61 and the footer.

### 15. Minor: native digits do not apply to counts

- Repro: हिंदी, Settings, tick native digits. Look at the Customers count and the left-nav badges.
- Expected: ८ in every count, as amounts and dates are converted.
- Actual: amounts show ₹११,८२० but `#customer-count` and `#nav-count-cust`/`#nav-count-over` show 8; phone numbers stay Latin (arguably correct).
- Likely location: `app.js` `render()` (customer-count and nav-count lines do not call `digits()`).

### 16. Minor: tap targets under 44 px on the phone

- Repro: 375x812, Overview or Recent, or any customer page.
- Expected: every tap target at least 44x44 px.
- Actual: entry Edit and Delete buttons 47x40 and 68x40; customer name links in the recent list 22 px high; header brand link 108x34; footer link 147x19.
- Likely location: `styles.css` `.link-btn`, `.entry .cust-name`, `.brand`, footer link.

### 17. Minor: closing the More sheet with Escape drops focus

- Repro: 375x812, press More, then Escape.
- Expected: focus returns to the More button.
- Actual: sheet closes but focus goes to the page body.
- Likely location: `app.js` `closeSheet()` (line 666); focus `#more-btn` when it was open.

### Notes, not bugs

- Advance balances read `₹-50`; "−₹50" or "₹50 advance" would read better.
- There is no way to delete a customer or change a customer's phone number. If that is intended, a mistyped phone can only be fixed by exporting, editing the JSON and importing.
- Two deletes in a row then Undo restores only the second; the toast makes that clear enough.
- Theme toggle has no `aria-pressed` state, so a screen reader cannot tell which theme is on.
