# Hisaab: tech stack

| | |
| --- | --- |
| Expected users | one shop owner per phone; the grader's automated browser |
| Budget a month | $0 |
| Last revised | 27 Nov 2026 (version 1.4.1.0; rows for versions 1.1.0.0, 1.2.0.0, 1.2.1.0 to 1.2.3.0, 1.2.4.0, 1.2.5.0, 1.2.6.0 to 1.4.0.0 and 1.4.1.0 added) |

## Constraints from the PRD

- No server data (D-1); must survive reload (F-5); must load instantly on a cheap phone.
- Graded by an automated browser reading fixed ids and data attributes.

## Choices

| Layer | Choice | Why | Simpler alternative considered | Cost a month |
| --- | --- | --- | --- | --- |
| Front end | Plain HTML, CSS and JavaScript, no framework, no build step | Smallest, fastest, nothing to break; every id under our control | React with Vite: more weight, no benefit here | $0 |
| Storage | Browser localStorage, one JSON document | Brief allows it; synchronous and simple | IndexedDB: needed only for many large photos | $0 |
| Screenshots | Resized in the browser to a 600 px JPEG before saving (640 px until 1.2.6.0) | Keeps storage well under the browser limit | Store the original: fills storage fast | $0 |
| Amount from screenshot | Tesseract.js, loaded only when a screenshot is chosen | Free, runs on the phone, no key | A vision model: needs a key and a server | $0 |
| Fonts | Baloo 2 (headings), Noto Sans with Devanagari and Telugu, served from the site itself since 1.4.1.0 (`fonts/fonts.css` and its woff2 files, with the OFL notes beside them; from Google Fonts before). Marathi uses the Devanagari font. The eight other scripts added in 1.2.0.0 (Bengali, Tamil, Gujarati, Kannada, Malayalam, Gurmukhi, Odia, Arabic for Urdu) are drawn with the phone's own fonts; no further font is loaded | Indian scripts render properly | System fonts: Telugu can fall back badly | $0 |
| Hosting | Vercel, static, project `hisaab`, `*.vercel.app` | Free, HTTPS, deploy from GitHub | Netlify | $0 |
| Code | Private GitHub repository | History and rollback | | $0 |
| Tests | Playwright against the local and live site; an independent checker written from the brief only | Mirrors how the grader checks | Manual clicking | $0 |
| Add-ons (1.1.0.0 and 1.2.0.0) | Plain script files beside the ledger: `profile.js`, `shops.js`, `langs.js`, `extras.js`, `customer.js`, each with its own style file where it needs one. They reach the ledger through one hook, `window.Hisaab`, at the end of `app.js` | The ledger and what the tester reads stay as they were; an add-on that fails cannot stop the ledger | Writing the features into `app.js`: every change would touch tested code | $0 |
| Languages (1.2.0.0) | English, Hindi and Telugu built in; nine more as `i18n/<code>.json`, twelve languages in all, 216 text keys each at 1.2.0.0, 233 at 1.2.3.0, 235 at 1.2.4.0 and 254 at 1.4.1.0, fetched when chosen | A visit in a built-in language fetches nothing extra | All twelve inside `app.js`: more weight on every visit | $0 |
| Translation checker (1.2.0.0) | `tests/i18n-check.cjs`, a Node script: same keys, same placeholders, the right script, the words that stay in Latin | Finds a missing or broken text before the deploy | | $0 |
| Add-ons (1.2.1.0 to 1.2.3.0) | `names.js` (names in nine more scripts, through the hook `nameIn` and `translit`), `sendways.js` (the reminder as SMS, to a chosen contact, or copied), `wacheck.js` (a hello message on WhatsApp after a customer is added), `polish.css` (layout only) | Same pattern as the earlier add-ons; nothing in the ledger changed | | $0 |
| Pay page (1.2.1.0) | `pay.html`, a separate static page. The details ride after `#`, so they never reach a server | WhatsApp may not make a `upi://` link tappable; an https link always is | Only the QR code | $0 |
| WhatsApp check (1.2.3.0) | The owner is asked, after a hello message, whether the number is on WhatsApp; the answer is kept in `hisaab.wa` | No lookup of a number exists without a WhatsApp business account and a server | The official business platform: needs an account and a server | $0 |
| Voice quick entry (1.2.4.0) | The browser's own speech service (Web Speech API). The app cannot repair it: it can fail on a network, in an in-app browser or with dictation turned off. When it fails the app says why, or "Did not catch that", and adds a tip: the keyboard's microphone key on a phone or tablet, the computer's own dictation (Windows key and H) elsewhere | No server and no key; typing always works | Recording the voice and sending it to a speech service: needs a server | $0 |
| Tests (1.2.1.0 to 1.2.3.0) | `tests/layout.mjs`, `names.mjs`, `pay.mjs`, `sendways.mjs`, `wacheck.mjs`, Playwright like the rest | Each new add-on has its own test | | $0 |
| Controls that say what they did (1.2.5.0) | `assist.js`, plain JavaScript like the other add-ons, with `tests/assist.mjs` | A press that changed nothing on screen now names what happened, without touching the ledger code | Changing `app.js`: it is the graded code | $0 |
| Storage and input guards (1.2.6.0) | `guard.js` and `guard.css`, plain JavaScript and CSS like the other add-ons, with `tests/guard.mjs` | A refused or blocked store, an oversized imported picture and a lost edit date are said, without touching the ledger code | Changing `app.js`: it is the graded code (only the screenshot size, 640 to 600 px, changed there) | $0 |
| Whole rupees (1.3.0.0) | Inside `guard.js`, with `tests/rupees.mjs` | The course guideline keeps money as whole-rupee integers; typed paise are held, imports rounded halves up and counted | Rounding inside `app.js`: the graded code | $0 |
| Brand look (1.3.0.0) | `brand.css`, `brand-mark.svg`, `icon.svg` in navy; Fraunces and Inter (from the site itself in `fonts/` since 1.4.1.0, from Google Fonts before), with `tests/brand.mjs` | One sheet loaded after the others sets the palette and fonts; no green | Editing `styles.css`: the graded layout | $0 |
| Phone layout (1.4.0.0) | `compact.css` and `compact.js`, with `tests/phone-ledger.mjs` | On a phone the ledger gets the screen: a slim top bar, a compact total, a quick-entry pill and a + sheet; the desktop layout is unchanged | A separate phone page: two places to keep in step | $0 |
| Files from the site itself (1.4.1.0) | `fonts/fonts.css` with woff2 files (OFL notes), the QR library qrcode-generator 1.4.4 as `vendor/qrcode.min.js` (MIT notice), with `tests/local-assets.mjs` | A plain visit asks no other host, so nothing about the visit reaches a font or script host; the screenshot reader, Tesseract.js, still loads on demand from jsDelivr | Fonts and the QR library from Google Fonts and cdnjs: two other hosts on every visit | $0 |
| + sheet in twelve languages (1.4.1.0) | `compact.js` and `langs.js`, a `compact` section of 19 keys in each `i18n/*.json` | The phone sheet follows the chosen language like the rest of the app | | $0 |
| Ledger unit tests (1.4.1.0) | `tests/ledger.cjs`, a Node script with a small stand-in for the browser; it calls the ledger's own functions through `window.Hisaab.ledger` | The ledger maths are tested on their own; `customer.js` and `extras.js` use the one balance function, so their sums cannot drift | | $0 |
| Offline | A service worker, `sw.js`, with the cache `hisaab-v15` (v4 at 1.2.0.0, v8 at 1.2.3.1, v9 at 1.2.4.0, v10 at 1.2.5.0, v11 at 1.2.6.0, v14 at 1.4.0.0, v15 at 1.4.1.0). It holds the app shell, the add-on files, `pay.html`, and since 1.4.1.0 the fonts and the QR library, and keeps a language file after its first fetch | The app opens without signal after one visit | | $0 |

Still no server, no account and no build step, in version 1.4.1.0 as in 1.0.0.0. Version 1.2.4.0 adds `tests/voice-quiet.mjs`; version 1.2.5.0 adds `assist.js` and `tests/assist.mjs`; versions 1.2.6.0 to 1.4.0.0 add `guard.js`, `guard.css`, `brand.css`, `brand-mark.svg`, `compact.css` and `compact.js`, with `tests/guard.mjs`, `tests/brand.mjs`, `tests/rupees.mjs` and `tests/phone-ledger.mjs`; version 1.4.1.0 adds `fonts/`, `vendor/qrcode.min.js`, `tests/local-assets.mjs` and `tests/ledger.cjs`.

**Total a month:** $0

## Security

- No keys, no server, no personal data leaves the phone. WhatsApp and UPI links are built locally.
- All text from imported files is escaped before display.

## Not used, and why

- A database or login: not needed for one owner on one phone; the brief says browser storage is fine.
- AI: the brief says Hisaab needs none; the screenshot reader runs on the phone.
