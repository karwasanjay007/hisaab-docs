# Hisaab: tech stack

| | |
| --- | --- |
| Expected users | one shop owner per phone; the grader's automated browser |
| Budget a month | $0 |
| Last revised | 20 Nov 2026 |

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
| Hosting | Vercel, static, project `hisaab` in team elodos, `*.vercel.app` | Free, HTTPS, deploy from GitHub | Netlify | $0 |
| Code | Private GitHub repository karwasanjay007/hisaab | History and rollback | | $0 |
| Tests | Playwright against the local and live site; an independent checker written from the brief only | Mirrors how the grader checks | Manual clicking | $0 |

**Total a month:** $0

## Security

- No keys, no server, no personal data leaves the phone. WhatsApp and UPI links are built locally.
- All text from imported files is escaped before display.

## Not used, and why

- A database or login: not needed for one owner on one phone; the brief says browser storage is fine.
- AI: the brief says Hisaab needs none; the screenshot reader runs on the phone.
