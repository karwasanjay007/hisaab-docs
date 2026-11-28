# Hisaab: tech stack

This page describes version 1.5.2, the version on the live link.

Hisaab is plain HTML, CSS and JavaScript served as static files. There is no server of its own and no database.
A shop's notebook belongs to one owner and sits in one pocket: it has to open at once on a cheap phone, work
without signal, and show nobody else who owes what. Keeping everything in the browser of the device does all
three, and there is nothing to sign in to, nothing to pay for and nothing to leak. The price is that a ledger
does not follow the owner to a second device by itself; the backup file does that by hand.

## Choices

| Part | What is used | Why | Cost |
| --- | --- | --- | --- |
| Pages and logic | Plain HTML, CSS and JavaScript. No framework, no build step | Small and fast on a cheap phone; what is written is what is served | Nothing |
| Server | None. The site is static files and runs no code of its own on a server | Nothing a shop records needs to leave the device | Nothing |
| Database | None. The ledger is kept by the browser on the device ([where data is kept](docs/data-model.md#where-data-is-kept)) | One owner, one device; it works offline and needs no account | Nothing |
| Features beside the ledger | Separate script and style files that reach the ledger through one object, `window.Hisaab` ([the files](ARCHITECTURE.md#the-files)) | A feature that fails cannot stop the ledger | Nothing |
| QR code | qrcode-generator, kept on the site as `vendor/qrcode.min.js` (MIT licence), loaded when a QR code or a statement is first drawn | The QR is drawn on the device as SVG and still works offline | Nothing |
| Fonts | Fraunces, Inter, Baloo 2, Noto Sans, Noto Sans Devanagari and Noto Sans Telugu, served from the site: `fonts/fonts.css` and sixteen `.woff2` files, each family with its OFL licence text. Other scripts use the fonts of the device | Hindi and Telugu draw properly, and a visit asks no font host | Nothing |
| Languages | English, Hindi and Telugu inside the main script; nine more as one JSON file each in `i18n/`, fetched from the site when chosen | A visit in a built-in language fetches nothing extra | Nothing |
| Screenshot | Shrunk in the browser on a canvas and kept as a JPEG inside the entry | A full-size picture would fill the browser's storage quickly | Nothing |
| Screenshot reader | Tesseract.js 5.1.1, fetched from `https://cdn.jsdelivr.net/npm/tesseract.js@5.1.1/dist/tesseract.min.js` only when a screenshot is chosen. It then fetches its own engine and language files. The picture is read in the browser | No key and no server; too large to carry on every visit | Nothing |
| Voice entry | The browser's own speech recognition. In most browsers the audio goes to the browser maker's service. The microphone button is hidden where the browser has none | No key and no server; typing always works | Nothing |
| Statement as PDF | The browser's print, which saves the page as a PDF | No PDF library to load | Nothing |
| Offline and install | A service worker, `sw.js`, and a web app manifest ([offline](ARCHITECTURE.md#offline)) | The app opens without signal after one visit and can be put on the home screen | Nothing |
| Reminders and payment | Links only: `https://wa.me/` for WhatsApp, `sms:` and `tel:` for the phone's own apps, `upi://pay` for any UPI app. The pay page `pay.html` is one more static page | No messaging or payment account; the owner presses Send and the customer pays in their own app | Nothing |
| Hosting | Vercel, static files, on a `vercel.app` address with HTTPS ([hosting](ARCHITECTURE.md#hosting)) | Static hosting is all the product needs | Free plan |
| Tests | Playwright 1.62.0 driving a real browser, and two Node scripts for the ledger maths and the language files ([test it](IMPLEMENTATION-PLAN.md#test-it)) | The same checks run locally and against the live link | Nothing |

## Monthly cost

Nothing. Every part above is free: the hosting is on a free plan, the libraries and fonts are open source, and
there is no server, database, key or paid service.

## Security in brief

- **Nothing to break into.** There is no server code, no account, no key and no secret. No environment variable
  is read.
- **A plain visit talks to the site only.** Two features reach another party, and only when used: the screenshot
  reader is fetched from jsDelivr, and voice entry uses the browser's speech service. Neither is sent the ledger.
- **A message leaves only by the owner's hand.** A reminder, a receipt or a hello message opens in WhatsApp or
  SMS, and the owner presses Send there.
- **The pay page gets its details after `#`.** A browser does not send that part of an address to a server, so
  the shop, the customer's name, the UPI ID and the amount stay between the two phones.
- **Loaded files are treated as untrusted.** Text from a shop file is escaped before it is drawn, a picture is
  kept only when it is an image and small enough, and a UPI ID must have the right form.
- **Exports are safe to open in a spreadsheet.** A cell that begins like a formula is written as text.
- **No analytics.** Nothing counts visits or follows the owner.

What stays on the device and who can read it is in [the data model](docs/data-model.md#where-data-is-kept).

## Not used, and why

| Not used | Why |
| --- | --- |
| A server or serverless functions | Nothing in the product needs one, and the ledger would have to leave the device |
| A database, accounts and sign-in | One owner on one device; the brief allows browser storage |
| A framework and a build step | The pages are small; plain files load fastest and need no tooling to release |
| IndexedDB, cookies and session storage | The ledger is small enough for the browser's simple key and value store once screenshots are shrunk |
| An AI model | The brief says Hisaab needs none. The screenshot reader is text recognition that runs in the browser |
| A vision or speech service with a key | A key needs a server to keep it; the reader and the browser's speech service need neither |
| A WhatsApp business account | Needed only to send messages without the owner or to look a number up; the owner sends each message |
| A payment gateway | A UPI link and QR code let the customer pay the shop directly, with no fees and no account |
| Fonts and scripts from other hosts on every visit | They would tell another host about each visit and fail offline |
