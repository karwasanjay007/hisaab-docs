# Hisaab: tech stack

| | |
| --- | --- |
| Expected users | one shop owner per phone; the grader's automated browser |
| Budget a month | $0 |
| Last revised | 23 Nov 2026 (rows for versions 1.1.0.0 and 1.2.0.0 added) |

## Constraints from the PRD

- No server data (D-1); must survive reload (F-5); must load instantly on a cheap phone.
- Graded by an automated browser reading fixed ids and data attributes.

## Choices

| Layer | Choice | Why | Simpler alternative considered | Cost a month |
| --- | --- | --- | --- | --- |
| Front end | Plain HTML, CSS and JavaScript, no framework, no build step | Smallest, fastest, nothing to break; every id under our control | React with Vite: more weight, no benefit here | $0 |
| Storage | Browser localStorage, one JSON document | Brief allows it; synchronous and simple | IndexedDB: needed only for many large photos | $0 |
| Screenshots | Resized in the browser to 640 px JPEG before saving | Keeps storage well under the browser limit | Store the original: fills storage fast | $0 |
| Amount from screenshot | Tesseract.js, loaded only when a screenshot is chosen | Free, runs on the phone, no key | A vision model: needs a key and a server | $0 |
| Fonts | Baloo 2 (headings), Noto Sans with Devanagari and Telugu | Indian scripts render properly | System fonts: Telugu can fall back badly | $0 |
| Hosting | Vercel, static, project `hisaab`, `*.vercel.app` | Free, HTTPS, deploy from GitHub | Netlify | $0 |
| Code | Private GitHub repository karwasanjay007/hisaab | History and rollback | | $0 |
| Tests | Playwright against the local and live site; an independent checker written from the brief only | Mirrors how the grader checks | Manual clicking | $0 |
| Add-ons (1.1.0.0 and 1.2.0.0) | Plain script files beside the ledger: `profile.js`, `shops.js`, `langs.js`, `extras.js`, `customer.js`, each with its own style file where it needs one. They reach the ledger through one hook, `window.Hisaab`, at the end of `app.js` | The ledger and what the tester reads stay as they were; an add-on that fails cannot stop the ledger | Writing the features into `app.js`: every change would touch tested code | $0 |
| Languages (1.2.0.0) | English, Hindi and Telugu built in; nine more as `i18n/<code>.json`, twelve languages in all, 216 text keys each, fetched when chosen | A visit in a built-in language fetches nothing extra | All twelve inside `app.js`: more weight on every visit | $0 |
| Translation checker (1.2.0.0) | `tests/i18n-check.cjs`, a Node script: same keys, same placeholders, the right script, the words that stay in Latin | Finds a missing or broken text before the deploy | | $0 |
| Offline | A service worker, `sw.js`, with the cache `hisaab-v4`. It holds the app shell and the add-on files, and keeps a language file after its first fetch | The app opens without signal after one visit | | $0 |

Still no server, no account and no build step, in version 1.2.0.0 as in 1.0.0.0.

**Total a month:** $0

## Security

- No keys, no server, no personal data leaves the phone. WhatsApp and UPI links are built locally.
- All text from imported files is escaped before display.

## Not used, and why

- A database or login: not needed for one owner on one phone; the brief says browser storage is fine.
- AI: the brief says Hisaab needs none; the screenshot reader runs on the phone.
