# Hisaab: implementation plan

| | |
| --- | --- |
| Last revised | 27 Nov 2026 (versions 1.1.0.0 to 1.5.0.0 added at the end) |

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

## Versions 1.1.0.0 to 1.2.4.0, as built (22 to 23 Nov 2026)

The method was the same for 1.1.0.0 and 1.2.0.0. The method for the later versions is given with their table.

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

### Versions 1.2.1.0 to 1.2.4.0

Method: each change was measured first, on the live site. After 1.2.1.0 only the delta was tested, with the new
test and the suites it touches. The documents were brought up to date after the owner approved the baseline
1.2.3.0 on 23 Nov 2026.

| Version | What was built | File | Test that covers it | What testing found |
| --- | --- | --- | --- | --- |
| 1.2.1.0 | A layout that holds in every language and width | `polish.css` (layout only) | `tests/layout.mjs` (7 checks, 120 screens) | The new layout check found the defects before the fix; all pass after it |
| 1.2.1.0 | Customer names in nine more scripts, built automatically | `names.js`; hook `nameIn` used by `localName`, and `translit`, in `app.js` | `tests/names.mjs` (33 checks) | Nothing after the spelling corrections |
| 1.2.1.0 | A pay link the customer can tap, before the UPI link in the reminder | `pay.html`; `payPage` and `reminderText` in `app.js` | `tests/pay.mjs` (15 checks) | Nothing |
| 1.2.2.0 | Other ways to send a reminder: SMS, any WhatsApp contact, copy; a note on sample numbers | `sendways.js` | `tests/sendways.mjs` (16 checks) | Nothing |
| 1.2.3.0 | A WhatsApp check when a customer is added by hand | `wacheck.js` | `tests/wacheck.mjs` (16 checks) | The first customer added after a shop file load was not offered the check. Fixed before the deploy |
| 1.2.3.1 | A wrong answer to the WhatsApp check is switched with one tap | `wacheck.js` (the button `#wa-check-flip`) | `tests/wacheck.mjs` (19 checks) | Nothing. Asked for by the owner after he pressed "No" by mistake in his own trial |
| 1.2.4.0 | Voice entry never ends in silence: a failure says why, with the keyboard microphone or the computer's own dictation as the way that always works | `app.js` (`listen` inside `setupVoice`); two text keys in every language file | `tests/voice-quiet.mjs` (11 checks); `tests/voice.mjs` (15, unchanged) | A laptop with a touch screen was taken for a phone and offered the wrong tip. A phone is now recognised by what it is. Fixed before the deploy |
| 1.2.5.0 | Controls that say what they did, after the project feedback: the theme button, a navigation press and the skip link; quick entry offers to add a customer it does not know | `assist.js` (new, loaded last); `index.html` (one script tag); `sw.js` (the shell list, cache `hisaab-v10`) | `tests/assist.mjs` (38 checks); `tests/quick.mjs` unchanged | The first version replaced the question "Which customer?", which the earlier quick-entry test expects; it failed on the live site only, because locally the page was read before the add-on ran. The add-on now keeps the question |
| 1.2.6.0 | Gaps against the course build approach closed on top: a refused or blocked store said plainly, an oversized imported picture left out and counted, an edit keeps its date, the shop name whole on a phone, small labels raised; screenshots at most 600 px | `guard.js`, `guard.css`; one number in `app.js` (640 to 600 px) | `tests/guard.mjs` (48 checks) | Nothing; the test was written first |
| 1.3.0.0 | The brand look with no green, and money in whole rupees | `brand.css`, `brand-mark.svg`, `icon.svg` in navy; `guard.js` (whole rupees) | `tests/brand.mjs` (184 checks), `tests/rupees.mjs` (28 checks) | Five older expectations changed at the owner's word ("Full guideline"): four sweep shops with paise now expect whole rupees, and `tests/final.mjs` types 1 where it typed 0.5 |
| 1.4.0.0 | The phone layout like the best chat apps: a 56 px top bar, a compact total, a quick-entry pill, a slim tab row and a + sheet | `compact.css`, `compact.js` | `tests/phone-ledger.mjs` (147 checks) | Nothing; customer rows on the first screen with the sample shop went from 0 to 4 at 375 x 667 and from 1 to 8 at 390 x 844 |
| 1.4.1.0 | The + sheet in all twelve languages; the fonts and the QR library from the site itself; ledger unit tests; the side sums use the one balance function | `compact.js`, `langs.js`, `i18n/*.json` (a `compact` section of 19 keys), `fonts/`, `vendor/qrcode.min.js`, `sw.js` (cache `hisaab-v15`), `customer.js`, `extras.js` | `tests/local-assets.mjs` (13 checks), `tests/ledger.cjs` (27 checks); `tests/phone-ledger.mjs` (147, unchanged) | Nothing; the screenshot reader still loads on demand from its host |
| 1.5.0.0 | The desktop layout like the best chat apps: a total strip, the customer list gets the height, the quick-entry pill with a "+" popover, a right panel from 1200 px | `compact.css`, `compact.js`, `sw.js` (cache `hisaab-v16`) | `tests/desk-ledger.mjs` (93 checks) | Nothing; the test was written first |

### Left for later

- A native speaker's read of the nine new languages. The wording may be improved by one.
- Voice entry in those languages. It uses the browser's English speech service today, and a name spoken in
  another script is not matched to a customer saved in Latin letters.
- Dates stay in the English short form in the nine new languages.
- Urdu is laid out left to right like the rest of the page.
- Storage-full during a shop switch, and a tab closed between the two writes of a switch: traced in the code,
  not run.
- The printed statement was checked by its markup, not on paper.
- A native speaker's read of the names in the nine newer scripts and of the strings added in 1.2.1.0 to 1.2.3.0.
- The SMS link, the pay page and the WhatsApp check on a real phone.
- The pay page in languages other than English.
- Real speech. A stand-in plays the browser's speech service in the voice tests; nobody has spoken an entry on a
  phone and on a laptop as a test, and the computer's own dictation was not tried on the owner's machine.

- A fresh reviewer's read of versions 1.3.0.0 and 1.4.0.0.
- Fonts and the QR library still load from other sites; no unit tests for the ledger maths; three places add amounts besides the one balance function.
