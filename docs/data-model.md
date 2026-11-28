# Hisaab: data model

This page describes version 1.5.2, the version on the live link.

## Where data is kept

Hisaab has no server of its own: everything a shop records stays in the browser of the device. There is no
database, no account, no IndexedDB, no session storage and no cookie.

| What | Where exactly | Who can read it | How long it stays | When the browser's storage is cleared |
| --- | --- | --- | --- | --- |
| The open shop: its name, UPI ID, profile, customers, entries and screenshots | `localStorage` key `hisaab.v1`, in the browser on the device | Anyone who can open that browser on that device. Nobody else | Until the owner deletes it, loads another shop over it, or clears the browser | Lost. Only an exported file brings it back |
| The other shops, when the owner keeps more than one | `localStorage` key `hisaab.shops` | The same | Until the shop is removed | Lost |
| The owner's answer to the WhatsApp check, per phone number | `localStorage` key `hisaab.wa` | The same | Until the browser is cleared | Lost; numbers show as not checked |
| The chosen language and theme | `localStorage` keys `hisaab.lang`, `hisaab.theme` | The same | Until changed | Back to English and the device's theme |
| Copies of the site's files, for offline use | The browser cache `hisaab-v22`, written by the service worker | The same; it holds no ledger data | Until a newer cache replaces it | Fetched again on the next visit online |
| Exported files | The device's downloads folder, where the browser saves them | Whoever has the file | Until deleted | Not touched |

A private window, or a browser that blocks storage, keeps nothing after the page is closed; the app says so
at the top of the page. What reaches another party, and when, is under
[security and privacy](../ARCHITECTURE.md#security-and-privacy).

## Entities

![Hisaab data model](diagrams/data-model.svg)

### Shop (the value of `hisaab.v1`)

| Field | Type | Notes |
| --- | --- | --- |
| `shop` | text | Shop name, at most 60 characters typed. "My shop" on a first visit |
| `upi` | text | The shop's UPI ID, or empty. When empty, pay links use the demo ID `hisaab.demo@upi` |
| `nativeDigits` | true or false, optional | Hindi or Telugu digits; present only after the tick box is used |
| `owner`, `address`, `logo` | text, optional | Shop profile: owner up to 60 characters, address up to 160, logo as a `data:image/` URL |
| `customers` | list of Customer | In the order added |
| `entries` | list of Entry | In the order added |

A stored value without `customers` and `entries` lists is ignored, and the app starts with an empty shop.
The diagram draws `shop`, `upi` and `nativeDigits` as Shop settings and `owner`, `address` and `logo` as Shop
profile; all six sit at the top level of the document.

### Customer

| Field | Type | Notes |
| --- | --- | --- |
| `phone` | 10 digits | The customer's identity; no two customers share one. Entries point to it. "Change phone number" moves the entries to the new number |
| `name` | text | At most 60 characters typed |
| `name_hi`, `name_te` | text, optional | The owner's own spelling in Hindi and Telugu; when absent the app works one out |

### Entry

| Field | Type | Notes |
| --- | --- | --- |
| `id` | text | `e`, the time and random letters |
| `phone` | 10 digits | The customer the entry belongs to |
| `type` | `credit` or `payment` | "You gave" or "You got" |
| `amount` | number | Whole rupees, above 0, at most ₹1,00,00,000 for a typed entry |
| `date` | `YYYY-MM-DD` | |
| `note` | text | Up to 80 characters typed, 200 from a file |
| `shot` | JPEG data URL or `null` | The payment screenshot |
| `seq` | whole number | Orders entries that share a date |

## Derived values

None of these is stored; each is worked out from the entries every time the page is drawn.

- **Balance** of a customer: all credit minus all payments. Above zero the customer owes; below zero the
  customer is in advance.
- **Balance after** each entry on the customer page: the customer's entries by date, then `seq`, oldest
  first, with the balance after each one.
- **Total outstanding**: the sum of every customer's balance, so an advance lowers it. When that differs
  from what is owed by those who owe, a note under the total shows both figures.
- **Days without paying**: for a customer who owes, the days from the last payment to today, or from the
  first credit when there was no payment.
- **Overdue**: 30 days or more without paying (`OVERDUE_DAYS = 30`). Only these customers carry the
  "30+ days" badge, count in the navigation, and are in "Remind all overdue".
- **Collected** and **given on credit, last 30 days**: entries dated 0 to 29 days before today.
- **Today**: the device's date, unless the address carries `?today=YYYY-MM-DD` with a valid date.

Money is rounded to two decimals in the sums and shown with Indian grouping (1,00,000).

## Storage keys

| Key | Written by | Holds |
| --- | --- | --- |
| `hisaab.v1` | `app.js` | The open shop, as one JSON document ([its shape](schema/stored-state.schema.json)) |
| `hisaab.shops` | `shops.js` | `{ active: id, list: [{ id, name, data }] }`, plus `pending: id` while a switch is under way. Each `id` is `s`, the time and random letters. The open shop's entry has `data: null`, because its ledger is under `hisaab.v1`; every other entry's `data` is a whole shop document. Not written until a second shop exists; removed when one shop is left |
| `hisaab.lang` | `app.js` | The language code, one of the twelve; `en` when absent |
| `hisaab.theme` | `app.js` | `light` or `dark`; absent means follow the device |
| `hisaab.wa` | `wacheck.js` | `{ "<10 digits>": { s: "ok" or "no", d: "YYYY-MM-DD" } }`. Keyed by number, outside the ledger, so it is in no shop file and no backup |
| `hisaab.guard` | `guard.js` | A test write of `1` when the page opens, removed at once, to find out whether storage works |

The service worker's cache is named `hisaab-v22`. It is in the browser's Cache API, not in `localStorage`
([what it holds](../ARCHITECTURE.md#offline)).

## Storage limits and screenshots

- The code sets no total size. The limit is what the browser allows for `localStorage`. When a write is
  refused, a notice at the top of the page says the change was not kept and offers "Backup file (JSON)".
- A screenshot is drawn on a canvas with its longest side at most 600 px, on white, and stored inside its
  entry as a JPEG data URL at quality 0.72. Screenshots are the largest thing in storage.
- A picture in an imported file is left out when it is not an image or is longer than 400,000 characters;
  its entry is kept.
- A logo is shrunk until its data URL is under 40 KB; one from a backup file is accepted up to 60 KB.
- A typed amount with paise is not saved; an imported one is rounded to whole rupees, halves up.

## Shop file and exports

- [The shop file](schema/shop-file.schema.json): the JSON that "Load shop" reads, and the rules for what is
  skipped.
- [The exports](schema/export-entries.csv.md): the two CSV files, the backup file and reading a CSV. The
  backup file holds no screenshots, no `hisaab.wa` answers and no other shop.
- `hisaab-<shop>.json`, the backup file: `shop`, `upi`, `customers` (`name`, `phone`, `name_hi`, `name_te`),
  `entries` (`phone`, `type`, `amount`, `date`, `note`), and `owner`, `address`, `logo` when set. "Load shop"
  also reads `shop_name` or `name` for the shop, `upi_id` for the UPI ID, and `shot` and `name` in an entry.
- `hisaab-<shop>-entries.csv`: `date`, `name`, `phone`, `type`, `amount`, `note`, `has_screenshot` (`yes` or
  `no`), one row per entry.
- `hisaab-<shop>-balances.csv`: `name`, `phone`, `total_credit`, `total_payment`, `balance`, `last_payment`,
  one row per customer, and a last row `TOTAL OUTSTANDING`.
- [The stored state](schema/stored-state.schema.json), [the links](schema/links.md) and
  [the quick entry result](schema/quick-entry-result.schema.json).
