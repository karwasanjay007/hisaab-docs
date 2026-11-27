# Exports: entries CSV and balances CSV

Hisaab 2.0.1.0 has two export buttons, on the home view and in Settings. Both files are built in the browser
from the stored state (see [stored-state.schema.json](stored-state.schema.json)) and downloaded through a
temporary Blob link of type `text/csv;charset=utf-8`. Nothing is sent anywhere. Source: `entriesCsv()`,
`balancesCsv()` and `esc()` in `src/lib/csv.ts`, and `downloadCsv()` in `src/App.tsx`.

## File names

| Button | File |
| --- | --- |
| Export balances | `hisaab-balances.csv` |
| Export entries | `hisaab-entries.csv` |

The names are fixed: no shop name and no date in them.

## Entries CSV

Header: `phone,name,date,type,amount,note,running_balance`

One row per entry. Customers come in stored order; within a customer the entries run from oldest to newest
(by date, then by id).

| Column | Value |
| --- | --- |
| `phone` | the customer's 10-digit phone |
| `name` | the customer's name |
| `date` | `YYYY-MM-DD` |
| `type` | `credit` or `payment` |
| `amount` | whole rupees, always positive, plain digits |
| `note` | the note, may be empty |
| `running_balance` | the customer's balance after this entry: credit adds, payment takes away (`signedAmount()`); may be negative |

A customer with no entries has no row in this file. The screenshot is not exported.

## Balances CSV

Header: `phone,name,balance`

One row per customer, in stored order, including customers with no entries (balance 0). `balance` is
`balanceOf()`: credit minus payments, in plain digits, negative for an advance.

## Common CSV rules (both files)

- Rows are joined with a line feed (`\n`); there is no line break after the last row and no byte-order mark.
- A cell that holds a comma, a double quote or a line break is wrapped in double quotes, and a double quote
  inside it is doubled. Other cells are written as they are.
- Numbers are plain digits with no grouping and no currency sign.
- The header is in English in every language.

## Not in 2.0.1.0

Version 1 could also import a CSV and export a JSON backup. Neither was rebuilt: the import box reads JSON
only ([shop-file.schema.json](shop-file.schema.json)) and there is no JSON export.
