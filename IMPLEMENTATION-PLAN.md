# Hisaab: implementation plan

| | |
| --- | --- |
| Last revised | 23 Nov 2026 (versions 1.1.0.0 and 1.2.0.0 added at the end) |

## Steps

| # | Slice | Done when | Requirement |
| --- | --- | --- | --- |
| 1 | Page shell, light and dark theme, 375 px layout | Page loads locally; no sideways scroll at 375 px | 5.2 stage 1 |
| 2 | Store, balances, total, rows with the grader's attributes | Sample loaded: `#total-outstanding` shows 11820; every `[data-balance]` matches `sample-expected.json` | F-1, F-3, S-1 |
| 3 | Import box `#import-box` and `#import-go`, with validation | A different shop replaces the data; a bad file changes nothing | D-2, S-4 |
| 4 | Add customer, add entry, customer page newest first | Entries persist after reload | F-2, F-5, S-3 |
| 5 | Reminder link `a[data-remind]` to `https://wa.me/91<phone>?text=` | Message holds name and balance in rupees | S-2 |
| 6 | Payment screenshot `#shot-input`, `img[data-shot]` | Saved payment shows the picture after reload | 5.2 stage 2 |
| 7 | Bonus: overdue list, edit and delete, CSV export, search, UPI link, Hindi and Telugu, amount from screenshot | Each checked by script | 5.2 stage 2 |
| 8 | `?today=` honoured everywhere | Overdue days computed from the given date | F-4 |
| 9 | Independent checker (separate agent, brief only) passes on local and live | All checks green | All |

## Where the build will likely get stuck

- The grader reads the form on the page it lands on: keep the entry form and the import box on the home
  view, with recent entries (and their screenshots) visible there too.
- Pop-up dialogs stall automated browsers: no `alert` or `confirm`; delete uses undo.
- localStorage quota with photos: resize before saving.

## Release

| # | Step | Done when |
| --- | --- | --- |
| R1 | Security pass: no secrets, imported text escaped | Review output in REPORT.md |
| R2 | Deploy to a `*.vercel.app` address (never sai4b.com) | Live URL fetched, 200 pasted |
| R3 | Check at 1440 px and 375 px, light and dark | Screenshots looked at; fixes in REPORT.md |

## Versions 1.1.0.0 and 1.2.0.0, as built (22 to 23 Nov 2026)

The method was the same for both versions.

- Each addition is an add-on file beside the ledger. The main script gained only a small hook,
  `window.Hisaab`, at its end. The ids, attributes, totals and the import that the tester uses did not change.
- No earlier test was edited.
- Each change was tested as a delta, with its own new test.
- One review round by an independent reviewer, before the deploy.
- One full regression before the deploy and one after it, on the live site.
- One deploy per version, with the earlier deployment noted for rollback.
- Translations were produced as one file per language and checked by a script (`tests/i18n-check.cjs`).

### Version 1.1.0.0

| What was built | File | Test that covers it | What the review changed |
| --- | --- | --- | --- |
| Add-on hook: events `render`, `statement`, `imported`, `export`, each listener in its own guard | `app.js` (end of file) | The earlier suites, unedited | Undo in a second tab after a shop switch was fixed |
| Shop profile: owner, address, logo, on the header, the statement and in the backup | `profile.js`, `profile.css` | `tests/profile.mjs` (36 checks) | Unsaved profile text is kept through a render |
| Balance history line on the customer page, as SVG | `profile.js`, `profile.css` | `tests/profile.mjs` | Nothing |
| Several shops: add, switch, remove, "Import as a new shop" | `shops.js`, `shops.css` | `tests/shops.mjs` (25 checks) | The Shops block now sits inside the Shop settings card. A message is shown when a refused "Import as a new shop" cannot switch back |
| Phone width of the add-ons at 320 and 375 px | the add-on style files | `tests/addons-width.mjs` (54 checks) | Nothing |
| Service worker lists the new files | `sw.js` | The offline suite, unedited | Nothing |

### Version 1.2.0.0

| What was built | File | Test that covers it | What the review changed |
| --- | --- | --- | --- |
| Hook extended: `addLanguage`, `hasLanguage`, `setLang`, `text`, `balances`, `overdue`, `quickWords`, `forgetUndo` | `app.js` (end of file) | The earlier suites, unedited | Nothing |
| Nine more languages, 216 text keys each, fetched when chosen | `langs.js`, `i18n/<code>.json` | `tests/i18n-check.cjs` (11 languages), `tests/langs.mjs` (25 checks) | The quick-entry hint showed an example the parser did not understand in the new languages. The parser now learns each language's words, and the example uses the customer's name as stored |
| Customer page tools: totals, days since the last payment, Call and chat links, change phone, delete with Undo | `customer.js`, `customer.css` | `tests/customer.mjs` (36 checks) | The ledger's own Undo is cleared when a number changes or a customer is deleted. Two quick taps on Delete count as one press |
| Note shortcuts, overdue total, the date the figures are for | `extras.js`, `extras.css` | `tests/langs.mjs` | A note shortcut respects the field's length |
| Service worker: cache `hisaab-v4`, the five new add-on files, a language file kept after its first fetch | `sw.js` | The offline suite, unedited | Nothing |

One review point was left as it is: entries are put back in a slightly different order after Undo of a customer
delete. Nothing is lost.

### Left for later

- A native speaker's read of the nine new languages. The wording may be improved by one.
- Voice entry in those languages. It uses the browser's English speech service today, and a name spoken in
  another script is not matched to a customer saved in Latin letters.
- Dates stay in the English short form in the nine new languages.
- Urdu is laid out left to right like the rest of the page.
- Storage-full during a shop switch, and a tab closed between the two writes of a switch: traced in the code,
  not run.
- The printed statement was checked by its markup, not on paper.
