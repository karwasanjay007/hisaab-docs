# Exports: entries CSV, balances CSV and JSON backup

Hisaab has three export buttons. All three are built in the browser from the stored state (see
[stored-state.schema.json](stored-state.schema.json)) and downloaded through a temporary Blob link. Nothing
is sent anywhere. Source: `exportEntries()`, `exportBalances()`, `exportJson()` and `csvCell()` in `app.js`.

## File names

Every file name uses a slug of the shop name: the name in lower case, each run of characters other than
`a-z` and `0-9` replaced by `-`, and a leading or trailing `-` removed. A name with no Latin letters or
digits (for example a shop name typed only in Devanagari) gives the slug `shop`.

| Button | Element | File name | MIME type |
| --- | --- | --- | --- |
| Entries CSV | `#export-csv` | `hisaab-<slug>-entries.csv` | `text/csv;charset=utf-8` |
| Balances CSV | `#export-balances` | `hisaab-<slug>-balances.csv` | `text/csv;charset=utf-8` |
| JSON backup | `#export-json` | `hisaab-<slug>.json` | `application/json` |

Example: the sample shop "Lakshmi Provision Store (fictional)" gives
`hisaab-lakshmi-provision-store-fictional-entries.csv`.

## Entries CSV

One header row, then one row per entry, in this column order:

| # | Column | Value | Format |
| --- | --- | --- | --- |
| 1 | `date` | `entry.date` | `YYYY-MM-DD` |
| 2 | `name` | the customer's `name` (English name, not `name_hi` or `name_te`) | text; empty if no customer has the entry's phone |
| 3 | `phone` | `entry.phone` | 10 digits, no country code |
| 4 | `type` | `entry.type` | `credit` or `payment` |
| 5 | `amount` | `entry.amount` | the stored number as JavaScript prints it: no rupee sign, no thousands separator, a decimal point only when needed (`250`, `12.5`) |
| 6 | `note` | `entry.note` | text, may be empty |
| 7 | `has_screenshot` | whether `entry.shot` is set | `yes` or `no` (the picture itself is not exported) |

Row order: by `date` ascending, then by `seq` ascending for entries on the same date (oldest first, the
reverse of the customer page).

The entries export round-trips: loaded back through `#import-box` (see [Importing a CSV](#importing-a-csv))
it gives the same totals (tested on the sample: total outstanding 11,820). The `has_screenshot` column is
not read back, so screenshots do not return.

## Balances CSV

One header row, one row per customer in stored order (the order they were added or imported), then a
total row:

| # | Column | Value | Format |
| --- | --- | --- | --- |
| 1 | `name` | customer `name` | text |
| 2 | `phone` | customer `phone` | 10 digits |
| 3 | `total_credit` | sum of the customer's credit amounts, rounded to 2 decimals | number |
| 4 | `total_payment` | sum of the customer's payment amounts, rounded to 2 decimals | number |
| 5 | `balance` | `total_credit - total_payment` as computed by `balances()` | number; negative when the customer is in advance |
| 6 | `last_payment` | date of the customer's latest payment | `YYYY-MM-DD`, or empty when there is none |

Last row: `TOTAL OUTSTANDING,,,,<total>,` where `<total>` is the total outstanding, the sum of every
customer's balance (a customer in advance reduces it). See [../data-model.md](../data-model.md).

## Common CSV rules (both files)

- Separator `,`; line ending CRLF (`\r\n`) after every row, including the last one.
- Encoding UTF-8 with no byte order mark. Hindi or Telugu text in a name or note is written as UTF-8.
- Every cell goes through `csvCell(v)`:
  1. `null` or `undefined` becomes the empty string; anything else is converted with `String(v)`.
  2. **Formula guard:** when the value is not a JavaScript number and its text starts with `=`, `+`, `-`,
     `@`, a tab or a carriage return, a single quote `'` is put in front (`=SUM(A1)` becomes
     `'=SUM(A1)`, `-5 off` becomes `'-5 off`, `+91` becomes `'+91`). Numbers are never guarded, so a
     negative balance stays `-180`.
  3. **Quoting:** when the text (after the guard) contains `"`, `,`, a line feed or a carriage return, it
     is wrapped in double quotes and each `"` inside is doubled (`say "hi"` becomes `"say ""hi"""`).
     Other cells are written bare.
- Phone numbers are written as bare 10-digit cells with no quote or guard. A spreadsheet may read them
  as numbers; that would drop a leading 0, and import does accept a 10-digit phone that starts with 0
  (the Add customer form does not).

## Importing a CSV

`#import-box` and `#import-file` (which accepts `.json`, `.csv` and `.txt`) also take a spreadsheet saved
as CSV or as tab-separated text. Text that starts with `{` or `[` is read as JSON; anything else is read
as CSV by `csvRows()` and `fromCsv()` in `app.js`.

**Reading the text.** A byte order mark at the start is dropped. The separator is a tab when the first
line has more tabs than commas, otherwise a comma. Quoted cells, doubled quotes inside them and line
breaks inside quotes are handled. Empty rows are ignored. The leading `'` that the export's formula guard
adds before `=`, `+`, `-` or `@` is removed.

**Headers.** The first row is the header row. Each header is lower-cased and reduced to its letters
`a-z` only (so `Customer Name`, `customer_name` and `CUSTOMER-NAME` all read as `customername`), then
matched against these names; the first matching column is used:

| Field | Header names accepted |
| --- | --- |
| name | `name`, `customer`, `customername`, `naam` |
| phone (required) | `phone`, `mobile`, `phonenumber`, `mobilenumber`, `number`, `contact` |
| type | `type`, `entrytype`, `kind`; when there is no type column, entries are credit |
| amount | `amount`, `amt`, `rs`, `inr`, `rupees` |
| date | `date`, `day`, `entrydate`; `YYYY-MM-DD` or `DD/MM/YYYY`, as in the shop file |
| note | `note`, `notes`, `item`, `items`, `details`, `remark` |
| separate credit column | `credit`, `udhaar`, `udhar`, `gave`, `given`, `debit` |
| separate payment column | `payment`, `paid`, `jama`, `received`, `got` |

A phone column and at least one of amount, credit, payment or name are required, and there must be at
least one row under the header. Otherwise the message is "Paste a shop file (JSON) or a spreadsheet saved
as CSV with name, phone, type, amount and date columns." and nothing is replaced.

**Rows.** Each row with a phone adds a customer (name and phone). A row can then give one typed entry from
the amount column, and a credit entry and a payment entry from the separate columns; a blank or zero
credit or payment cell gives no entry. A row with only a name and a phone adds just the customer. Every
entry carries the row's date and note.

**Checks.** The result is a shop object with the current shop name, which then goes through the same
`fromShopFile()` checks as a JSON file ([shop-file.schema.json](shop-file.schema.json)): type aliases,
amounts from 0 to 10,000,000 (others skipped and listed), a missing or unreadable date set to today and
counted, repeated phones dropped, bad rows skipped and listed, and one Undo in the toast.

## JSON backup

`JSON.stringify(out, null, 1)` (one-space indent), where `out` is:

```json
{
 "shop": "<state.shop>",
 "upi": "<state.upi>",
 "customers": [ { "name": "...", "phone": "..........", "name_hi": "...", "name_te": "..." } ],
 "entries": [ { "phone": "..........", "type": "credit", "amount": 250, "date": "YYYY-MM-DD", "note": "..." } ]
}
```

- `upi` is left out when no UPI ID is saved (the value is `undefined`, which JSON drops).
- `customers` is the stored array as it is, so `name_hi` and `name_te` appear only on customers that have
  them (possibly as `""`).
- Each entry keeps only `phone`, `type`, `amount`, `date` and `note`. The `id`, `seq` and **`shot`
  (screenshot) are not exported**, so a backup restored through `#import-box` comes back without its
  screenshots, with new ids, and with `seq` reset to the position in the file.
- `nativeDigits` is not exported.
- The file is a valid shop file ([shop-file.schema.json](shop-file.schema.json)), so it can be imported
  again as it is.
