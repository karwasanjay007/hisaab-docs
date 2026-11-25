# Hisaab data model

Hisaab keeps one shop's ledger in the browser's `localStorage`. There is no server and no database. This
page describes what is stored, how the pieces relate, and how every figure on screen is worked out. The
source of truth is `app.js`; the schemas in [schema/](schema/) give the exact field rules.

![Data model](diagrams/data-model.svg)

## Entities

| Entity | Where | Key | What it holds |
| --- | --- | --- | --- |
| Shop | the root of the `hisaab.v1` document | none (one per browser) | `shop` (name), `upi` (UPI ID, `""` when not set), `nativeDigits` (optional), and the two arrays below |
| Customer | `customers[]` | `phone`, 10 digits | `name`, optional `name_hi` (Devanagari) and `name_te` (Telugu script) |
| Entry | `entries[]` | `id` (made by `uid()`) | `phone`, `type` (`credit` or `payment`), `amount` (rupees, 2 decimals), `date` (`YYYY-MM-DD`), `note`, `shot` (JPEG data URL or `null`), `seq` |
| Settings | the Shop fields `upi` and `nativeDigits`, plus the keys `hisaab.lang` and `hisaab.theme` | | UPI ID, digit style, language, theme |

Field-by-field rules: [schema/stored-state.schema.json](schema/stored-state.schema.json).

### Keys and relationships

- A customer is identified by its phone. There is no separate customer id. The edit form changes only
  the names. Since version 1.2.0.0 the customer page can change the phone, which rewrites `phone` on the
  customer and on every entry of that customer.
- An entry belongs to one customer through `entry.phone`. A customer has zero or more entries.
- Phones are unique within a shop: the add-customer form refuses a phone that exists, and import keeps
  the first customer with a given phone. Both accept any 10 digits; the add-customer form refuses input
  with anything other than digits, spaces and + ( ) - ., and adds a number that does not start with 6, 7,
  8 or 9 with a warning that WhatsApp may not reach it.
- Since version 1.2.0.0 a customer can be deleted. The customer and all their entries go together, so
  no entry is left without its customer through the app. If a stored
  document were edited by hand to hold such an entry, its amount would still count in the totals
  (`balances()` adds a zero balance for any phone it meets) but it would not be listed as a customer.
- The app stores no timestamps, no created or updated dates, and no version number inside the document.
  The version lives only in the key name (`hisaab.v1`).

## Derived values

None of these is stored. All are recomputed from the entries on every render.

| Value | Formula | Shown in |
| --- | --- | --- |
| Customer balance | sum of the customer's `credit` amounts minus sum of their `payment` amounts, rounded to 2 decimals after each step | `[data-customer="<phone>"] [data-balance]`, customer page |
| Owes / in advance / clear | balance above 0 / below 0 / exactly 0 | colour of the balance, customer page line; in advance the `[data-balance]` text is "₹-200" (`data-balance="-200"`), drawn as "-₹200" with an "Advance" label (`balHtml`) |
| Total outstanding | sum of **all** balances, including negative ones, rounded to 2 decimals (`totalOutstanding`). A customer in advance (balance below 0) reduces it | `#total-outstanding`: the one figure, with `data-value` and a screen-reader plain-number copy (for example "₹1,895 1895"); the balances CSV TOTAL OUTSTANDING row |
| Owed total | sum of the balances above 0 only (`owedTotal`) | only when it differs from the total: in `#total-note` just below `#total-outstanding`, outside it, as "₹2,095 owed, less ₹200 paid in advance" (i18n `owedNote`) |
| Customers with balance | count of customers whose balance is above 0 | `#customers-with-balance` |
| Top defaulter | customer with the largest balance above 0 (on a tie, the one stored first) | `#top-defaulter`, with `data-phone` |
| Days overdue | for each customer with a balance above 0: `max(0, today - ref)` in whole days, where `ref` is the date of the latest payment, or the earliest credit when there has never been a payment | overdue list (`[data-overdue]` with `data-days`), sorted by most days, then larger balance |
| Running balance | the customer's entries sorted by `date` then `seq`, oldest first; the balance after each entry | customer page ("balance after"), printed statement. Shown newest first |
| Last 30 days | sums of payments and of credits whose date is 0 to 29 days before today | the two KPI tiles |

### Today and the `?today=` override

Every date-dependent figure uses `todayIso()`: when the page address has `?today=YYYY-MM-DD` and the value
passes the date check, that date is today; otherwise it is the device's local date. This affects days
overdue, the 30-day tiles, the default date on a new entry, the "no future dates" check on the entry
form, the statement date and the date given to an imported entry that has none. Day counts are made in
UTC from the two `YYYY-MM-DD` strings, so they are whole days whatever the time zone.

Sample check (with `?today=2026-11-15`): Sunita Devi (`9000000102`) has credits 1500 + 1200 + 340 + 1500
= 4540 and payments 120 + 950 = 1070, so her balance is 3470. Her last payment was 4 Oct 2026, so she is
42 days overdue.

### Display order

- Customer page and recent entries: newest first, by `date` descending, then `seq` descending.
- Customer list: customers who owe first, largest balance first; then the rest, by balance and then by
  name.
- `seq` is the tie-breaker for entries on the same date: the index in the file for imported entries, the
  highest `seq` plus 1 for a new entry, unchanged when an entry is edited.

## localStorage keys

| Key | Value | Written when | Read when |
| --- | --- | --- | --- |
| `hisaab.v1` | the whole shop as JSON ([stored-state.schema.json](schema/stored-state.schema.json)) | after every change: add or edit customer, add, edit or delete entry, undo, import, settings, digit style | on start, and when another tab changes it (the `storage` event reloads the shop in this tab) |
| `hisaab.lang` | one of twelve language codes: `en`, `hi`, `te`, `bn`, `mr`, `ta`, `gu`, `kn`, `ml`, `pa`, `or` or `ur` | the language menu (`#lang`) is changed | on start; an unknown value falls back to `en` |
| `hisaab.theme` | `light` or `dark` | the theme button is pressed | by a small script in the `<head>` of `index.html` before the page paints; when absent the device setting decides |
| `hisaab.wa` | (1.2.3.0) a map from the 10-digit number to `{ s: "ok" or "no", d: date }`, the owner's answer to the WhatsApp check | the owner answers after a hello message to a customer added by hand | when a customer is added; not part of a shop file or a backup |

Outside `localStorage`, the service worker (`sw.js`) keeps copies of the app's files in the Cache Storage
cache `hisaab-v10` (version 1.2.5.0) so the app opens without signal. On install it precaches the app shell (`./`,
`index.html`, `styles.css`, `app.js`, `sample.js`, `sample-data.json`, `icon.svg`,
`manifest.webmanifest`, and since versions 1.1.0.0 and 1.2.0.0 the add-on files `profile.js`,
`profile.css`, `shops.js`, `shops.css`, `langs.js`, `customer.js`, `customer.css`, `extras.js` and
`extras.css`, and since 1.2.1.0 to 1.2.3.0 `names.js`, `sendways.js`, `wacheck.js`, `polish.css` and
`pay.html`), so one online visit is enough; on activate it deletes older caches such as
`hisaab-v1`. At run time it fetches from the network first and keeps a copy of the app's own files
(a language file `i18n/<code>.json` among them, the first time it is fetched), the
`qrcode-generator` script from cdnjs and Google Fonts. Tesseract.js and its language data (jsDelivr) are
not cached. It never caches the shop data, which lives only in `localStorage`.

On first start, or when `hisaab.v1` is missing, cannot be parsed, or lacks the `customers` and
`entries` arrays, the app starts an empty shop (`blank()`: shop "My shop", `upi` empty, no customers,
no entries) and saves it under `hisaab.v1`. The sample shop is loaded only when the owner presses Load
sample shop (on the overview's empty state or the Shop file card), like any imported file.
Import replaces the whole shop; the toast offers one undo, which puts the previous document back. Editing
an entry also offers one undo for 7 seconds, which puts the old entry back; it is held in memory only.

## Storage limits and screenshots

- Browsers give each site about 5 MB of `localStorage` (the exact amount depends on the browser). The
  whole shop, screenshots included, must fit in that one string.
- A screenshot chosen with `#shot-input` is never stored as the original file. It is drawn on a white
  canvas with its longer side scaled down to at most 640 px (smaller pictures are not enlarged) and saved
  as a JPEG data URL at quality 0.72. Choosing a screenshot also
  switches the entry type to payment.
- The amount reader (Tesseract OCR, loaded only when needed) reads the original picture, not the
  resized copy, and its result is not stored; it only fills the amount field.
- When a save fails because storage is full, the app shows "Storage is full. Export a backup, then remove
  some screenshots." The change stays on screen but is not saved, so it is lost on reload.
- Screenshots that arrive through import are kept as they are, with no resizing and no size check.
- The JSON backup does not include screenshots (see
  [schema/export-entries.csv.md](schema/export-entries.csv.md)).

## Import validation, in short

The import box accepts the shop file described by [schema/shop-file.schema.json](schema/shop-file.schema.json),
or a spreadsheet saved as CSV or tab-separated text, whose headers are matched by meaning and turned into
the same shape ([Importing a CSV](schema/export-entries.csv.md#importing-a-csv)).
Phones are normalised to 10 digits (`+91`, a leading `0`, spaces and dashes are removed), `type` aliases
such as `udhar` and `jama` are mapped to `credit` and `payment`, amounts may be text such as `Rs 1,200`,
and day-first dates such as `6/10/2026` are converted. An amount that is missing, blank, not a number,
negative or above 10,000,000 skips its entry; amount 0 and future dates are accepted. An entry with a
missing or unreadable date is kept, dated today, and counted in the message ("{n} entries had no date
and are dated today."). A `upi` or `upi_id` that fails the UPI ID check ([schema/links.md](schema/links.md))
is skipped and the saved UPI ID is kept. A bad record is skipped and named in the message ("Skipped {n}: ",
translated) rather than failing the whole import.

### Validation result

`sample-data.json` (identical to `brief/data/sample-data.json`) was checked against
`schema/shop-file.schema.json` on 20 Nov 2026 with a small hand-written Node checker of the keywords the
schema uses (ajv is not installed). Result:

- Both copies of the sample file are **valid** against the shop-file schema. Rechecked the same day after
  the schema gained the import amount limit and the UPI ID pattern: still valid. The UPI ID pattern was
  also run against `validUpi()` on 13 test values and agrees on all of them.
- The schema's phone, type, amount and date patterns were run against the app's own `phone10`,
  `validPhone` and `normDate` functions, taken from `app.js` at run time, on 50 test values: they agree on
  all of them, except two amount strings (`"1."` and `".5"`) that the app accepts and the pattern does
  not; this is stated in the schema.
- A stored state built from the sample the way the import does is valid against
  `schema/stored-state.schema.json`.
- The balances computed by the formula above match `brief/data/sample-expected.json`: total outstanding
  11820, all 8 balances equal, 8 customers with a balance.

Other schema files: [schema/quick-entry-result.schema.json](schema/quick-entry-result.schema.json)
(the result of `window.hisaabParseQuick`) and [schema/links.md](schema/links.md) (WhatsApp and UPI links).

## Added in version 1.1.0.0 (22 Nov 2026)

- **Shop state under `hisaab.v1`, optional profile fields:** `owner` (text, at most 60 characters), `address` (at
  most 160) and `logo` (a `data:image/` URL, shrunk to 96 px and kept under 40 KB). The backup file carries them
  when they are set.
- **A second key, `hisaab.shops`:** `{ active, list: [ { id, name, data } ] }` holds the other shops on the same
  phone. The active shop's `data` is null, because its ledger is the one under `hisaab.v1`. The key is written only
  once a second shop is added and is removed again when one shop is left.

The diagram `diagrams/data-model.svg` shows both.

## Added in version 1.2.0.0 (22 to 23 Nov 2026)

Nothing new is stored. No key and no field was added.

- **`hisaab.lang`, more values:** the existing key can now hold one of twelve codes. English, Hindi and Telugu
  are built into the app. Each of the other nine is one file, `i18n/<code>.json`, fetched only when that language
  is chosen.
- **Language files and the service worker:** a language file is kept by the service worker after its first fetch,
  so the chosen language also opens without signal. It is text for the screen only and holds no shop data.
- **Changing a customer's phone number:** the customer page can now do this. It rewrites `phone` on the customer
  and on every entry of that customer, so the entries stay with their customer. The ledger's own Undo is cleared
  at that moment, so an entry cannot come back under the old number.
- **Deleting a customer:** this removes the customer and all their entries. It takes two presses. Undo restores
  both the customer and the entries, and is offered for eight seconds.
- **Figures that are drawn, not stored:** the customer page totals, the days since the last payment, the total
  unpaid for 30 days or more and the date the figures are for are worked out from the entries each time.

## Added in version 1.2.1.0 to 1.2.3.0 (23 Nov 2026)

One new key. Nothing in the shop under `hisaab.v1` changed.

- **A new key, `hisaab.wa` (1.2.3.0):** a map from the 10-digit number to `{ s, d }`. `s` is `"ok"` or `"no"`,
  the owner's answer to the question whether the number is on WhatsApp; `d` is the date of the answer
  (`YYYY-MM-DD`). It is written after the owner answers, once a hello message was offered to a customer added
  by hand.
- **Per browser, keyed by number:** `hisaab.wa` is keyed by the number, not by the shop, so one answer serves
  every shop on the phone. It is not part of a shop file, an export or a backup.
- **Names in the nine newer scripts are computed, not stored:** `names.js` builds them each time from the
  Devanagari spelling (`name_hi`, or the built-in transliteration). Only `name_hi` and `name_te` are stored.
- **The pay link:** the reminder carries `pay.html#...` with the shop name, the UPI ID, the amount and the
  customer's name after `#`. That part of an address is never sent to a server. The page reads it, shows it
  and opens the `upi://pay` link.
- **Cache:** `hisaab-v5` at 1.2.1.0, `hisaab-v6` at 1.2.2.0, `hisaab-v7` at 1.2.3.0, `hisaab-v8` at 1.2.3.1. The precache list also
  names `names.js`, `sendways.js`, `wacheck.js`, `polish.css` and `pay.html`. Each language file had 233 text
  keys.

## Version 1.2.3.1 and 1.2.4.0 (23 Nov 2026)

Nothing new is stored. Version 1.2.3.1 lets the owner switch an answer in `hisaab.wa` with one tap; the
shape of the key is the same. Version 1.2.4.0 changes only what voice quick entry says when it fails.

- **Cache:** `hisaab-v8` at 1.2.3.1, `hisaab-v9` at 1.2.4.0.
- **Text keys:** 235 per language at 1.2.4.0 (233 at 1.2.3.0); `quickKeyTip` and `quickPcTip` were added.

## Version 1.2.5.0 (25 Nov 2026)

Nothing new is stored. The add-on `assist.js` reads `hisaab.theme` only through the page (the attribute
`data-theme`) and writes no key.

- **Cache:** `hisaab-v10` at 1.2.5.0; the app shell also lists `assist.js`.
- **Text keys:** 235 per language, unchanged.
