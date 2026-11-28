# Hisaab: implementation plan

This page describes version 1.5.2, the version on the live link.

How a developer runs Hisaab, tests it and releases it, and the order it was built in. What each file does is in
[the architecture](ARCHITECTURE.md#the-files).

## Run it

Hisaab is static files. There is nothing to install or build for the app itself.

1. Get the code folder. `index.html` is the app and `pay.html` is the pay page a reminder links to.
2. Serve the folder with any static file server on port 4173, the address the tests expect. For example:
   `npx serve -l 4173`.
3. Open `http://localhost:4173/`. The first visit shows an empty shop. Press "Load sample shop" to look around.

Serve it; do not open `index.html` from the disk. The sample shop, the nine newer languages and the offline
worker are fetched from the site, and the worker starts only on https or on localhost.

To see the figures as they were on a given day, add the date to the address:
`http://localhost:4173/?today=2026-11-15`.

## Test it

The tests are in `tests/`. Most drive a real browser with Playwright; two are plain Node scripts. Which test
proves which acceptance criterion is in [the epics](docs/epics.md).

Once, to set up. The code names Playwright but not a browser; Chromium is given here as an example:

```
cd tests
npm install
npx playwright install chromium
```

**Without a browser.** These two need no server:

```
node tests/ledger.cjs
node tests/i18n-check.cjs
```

`ledger.cjs` checks the ledger maths. `i18n-check.cjs` checks every language file against the English one: the
same keys, the same placeholders, nothing empty, the right script.

**Locally.** Serve the folder as in "Run it", then run a test by name. With no address it uses
`http://localhost:4173/`:

```
node tests/brief.mjs
```

`local-assets.mjs` and `contrast.mjs` have other defaults, so give them the address:
`node tests/local-assets.mjs http://localhost:4173/`.

**Against the live link.** Give the live address as the first argument and keep the output:

```
node tests/brief.mjs https://hisaab-hazel.vercel.app/ > tests/last-brief-live.txt
```

Each result is kept as `tests/last-<name>-live.txt`. A run is good when its last line reads "ALL PASS"; the
test `check.mjs` ends with a line of totals in its place. The latest results are in
[the report](REPORT.md#latest-results-on-the-live-link).

After a change, run the tests of what the change touches and `brief.mjs`. Run every test before a release and
again on the live link after it. A new feature brings its own test file; an earlier test is not edited to make a
change pass.

## Release it

1. **Raise the offline cache name.** In `sw.js`, raise the number in the cache name with every release (today
   `hisaab-v23`). Without it a returning visitor can keep old files. A new file of the app is also added to the
   list the worker keeps ([offline](ARCHITECTURE.md#offline)).
2. **Set the version.** Put the new number on the first line of `VERSION`.
3. **Run every test locally**, the two Node scripts first. All must pass.
4. **Compare with the live link.** The new build must have everything the live one has. If anything is missing,
   it is not released.
5. **Check nothing private is in the files** that will be served.
6. **Deploy.** Publish the code folder to the hosting. The code names no deploy command; for example
   `vercel deploy --prod` from the code folder. There is no build step. The file `.vercelignore`
   keeps the specification, the tests and the notes off the site.
7. **Check the live link.** Open `VERSION` on the live address and read the new number. Run every test against
   the live link and keep each result as `tests/last-<name>-live.txt`.
8. **Look at it.** On a phone width and a desktop width, in light and in dark, and once offline after one visit.
9. **Write it down.** One entry in [the changelog](CHANGELOG.md), the new results in
   [the report](REPORT.md#latest-results-on-the-live-link), and every other page changed where the product
   changed.

A release that fails on the live link is rolled back by promoting the earlier deployment in the hosting.

## The order it was built in

1. The page, light and dark, and a layout that holds at 375 px.
2. The ledger: customers, entries, balances, the total, the customer page, kept in the browser.
3. Loading a shop file, with checks on what is loaded.
4. The WhatsApp reminder.
5. The payment screenshot.
6. The optional features of the specification: the overdue list, edit and delete, CSV export, search, the UPI pay link, Hindi and
   Telugu, the amount read from a screenshot. Also the date taken from the page address.
7. Quick entry, typed and by voice; the UPI QR code; remind all; offline; an empty first visit with a sample
   shop to load.
8. The shop profile, the balance over time, and several shops on one device.
9. Nine more languages, the customer page tools, note shortcuts and the overdue total.
10. A layout that holds in every language, names in nine more scripts, and the pay page.
11. Other ways to send a reminder, and the WhatsApp check of a number.
12. Voice entry that always says why it failed; controls that say what they did.
13. Notices when storage is full or blocked, the brand look, and money in whole rupees.
14. The phone layout: the quick entry pill, the "+" menu and the customers on the first screen.
15. Fonts and the QR library served from the site, and unit tests of the ledger maths.
16. The desktop layout with its right panel, and Urdu right to left.

The dated history, version by version, is in [the changelog](CHANGELOG.md).
