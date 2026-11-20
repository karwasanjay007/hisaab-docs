# Hisaab: final visual and UX critique

Reviewed 20 Nov 2026 against the live build at https://hisaab-hazel.vercel.app/?today=2026-11-15, read only.
Evidence: 71 screenshots (JPG, quality 60) at 375x812, 768x1024, 1366x768, 1440x900, 1920x1080 (plus one
at 2560x1440), light and dark, English and हिंदी, for the overview, `#/s/customers` and `#/c/9000000102`,
plus full-page shots of the overview and customer page and every section view at 1440. Folder:
`C:\Users\sskar\AppData\Local\Temp\claude\a153659dba92c228\3568ccd8-320a-4379-8379-a3641d808a21\scratchpad\final\visual\`.
Contrast ratios below were computed (WCAG relative luminance), not judged by eye.

Known issue confirmed, not reworked here: at 1920 the overview content stops at about 1408 px and section
views at 880 px, leaving an empty band on the right (see `home-1920-dark-en.jpg`, `home-2560-light-en.jpg`).

## Strongest objection

The overview is not a dashboard, it is the whole app poured onto one page. Below the hero it stacks the
record form, Add a customer, Shop settings, Customers, Overdue, eight Recent entries and Load a shop with
three export buttons. At 1440 the left column ends at about 1,650 px while the right column runs to about
3,450 px (`full-home-1440-light-en.jpg`), so most of the scroll is a single right-hand column beside an empty
beige field. Admin forms (shop settings, JSON import) sit at the same visual weight as the money. Khatabook,
OkCredit and Vyapar web all open on a balance summary plus a customer list and keep settings and backup
behind the menu. Every one of those cards already has its own route, so the overview does not need them.

## Ranked fixes

Severity: High (a judge notices in the first minute), Medium (noticed on a second look), Low (polish).

### 1. High: trim the overview to money, customers, overdue and a short recent list

```css
/* Overview only: admin cards live on their own routes */
#view-home:not([data-section]) #sec-add,
#view-home:not([data-section]) #sec-settings,
#view-home:not([data-section]) #sec-import { display: none; }
/* Five recent entries on the overview; the full list stays at #/s/recent */
#view-home:not([data-section]) #sec-recent .entry:nth-child(n+6) { display: none; }
```
Markup: add after `#recent-list` (inside `#sec-recent`) a link shown only on the overview:
`<a class="see-all" href="#/s/recent" data-i18n="seeAll">See all entries</a>` with
`.see-all { display: inline-flex; align-items: center; min-height: 44px; font-weight: 700; color: var(--brand); }`
and `#view-home[data-section] .see-all { display: none; }`.
Before applying: `tests/nav.mjs` expects at least 7 visible cards on the overview (`visibleCards.length >= 7`);
lower that to 4, and confirm no grader looks for `#sec-add` or `#sec-import` on the landing view. If that
risk is unacceptable, apply only the recent-entries cap, which alone removes about 1,000 px of scroll.

### 2. High: entry rows are misaligned and twice as tall as they need to be

On the customer page and Recent entries the note text starts at x 374 after a PAYMENT pill and x 361 after a
CREDIT pill, so the column wobbles row to row; Edit and Delete sit on their own third line, bold, green and
underlined, making each entry about 110 to 140 px tall (`full-cust-1440-light-en.jpg`,
`sec-recent-1440-light-en.jpg`).
```css
.entry { grid-template-columns: 84px minmax(0, 1fr) auto; row-gap: 0; padding: 14px 0; }
.entry-type { justify-self: start; }
.entry-amt { grid-column: 3; grid-row: 1; text-align: right; font-size: 1.05rem; }
.entry-tools { grid-column: 3; grid-row: 2; justify-self: end; gap: 0; }
.entry-tools .link-btn { text-decoration: none; color: var(--muted); font-weight: 600; font-size: .88rem; min-height: 44px; padding: 0 8px; }
.entry-tools .link-btn:hover { color: var(--ink); text-decoration: underline; }
.entry-tools .link-btn[data-del] { color: var(--owed); }
.entry-shot { grid-column: 2; }
```
(Check the delete button's attribute name in `app.js` line 557 and use it in the last selector.)

### 3. High: form fields fail the 3:1 boundary rule in both themes

`--line` is used for input borders: #E3DACA on #FFFFFF is 1.39:1 and #263731 on #13201B is 1.34:1. WCAG
1.4.11 asks 3:1 for the edge that identifies a field. On screen the Amount and Note boxes are faint ghosts,
which is why the form looks unfinished next to Khatabook's crisp fields.
```css
:root { --field-line: #9A917F; }                 /* 3.12:1 on #FFFFFF */
:root[data-theme="dark"] { --field-line: #5E7A70; } /* 3.59:1 on #13201B */
@media (prefers-color-scheme: dark) { :root:not([data-theme="light"]) { --field-line: #5E7A70; } }
input[type="text"], input[type="tel"], input[type="number"], input[type="date"], input[type="search"], select, textarea,
.seg-opt, .btn-ghost, input[type="file"]::file-selector-button { border-color: var(--field-line); }
```
Keep `--line` for dividers and card edges, where 3:1 is not required.

### 4. High: in dark mode the primary button disappears into the card

`.btn-primary` in dark is #0F4A39 on a #13201B card: 1.65:1. "Save entry", "Add customer" and "Load shop"
read as dark smudges (`home-1920-dark-en.jpg`). Cards themselves are 1.11:1 against the page.
```css
:root[data-theme="dark"] .btn-primary { background: #2A9D76; color: #0B1511; }      /* 4.95:1 vs card, text 5.47:1 */
:root[data-theme="dark"] .btn-primary:hover { background: #33B086; }
@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) .btn-primary { background: #2A9D76; color: #0B1511; }
  :root:not([data-theme="light"]) .btn-primary:hover { background: #33B086; }
}
:root[data-theme="dark"] .card { border-color: #2E443C; }
@media (prefers-color-scheme: dark) { :root:not([data-theme="light"]) .card { border-color: #2E443C; } }
```

### 5. High: the top bar and the hero melt into one green slab

Top bar #0A4334 against hero #0E5C45 is 1.41:1 (dark: 1.42:1). There is no edge between brand bar and
content block, so the brand bar does not read as a bar, and the hero looks like it starts at the top of the
window. A world-class shell separates the chrome from the canvas.
```css
.top { background: #062A20; box-shadow: inset 0 -3px 0 var(--accent), 0 2px 12px rgba(0, 0, 0, .18); }
```
#062A20 keeps white text at 15.4:1. The amber underline also carries the brand accent across every view,
including section views where there is no hero.

### 6. Medium: hero composition on desktop

The total sits bottom left (`align-items: end`), the four KPI tiles sit top right, and the decorative ring
cuts through the right-hand tiles at 1366 and 1440. All four tiles have the same weight, so "who owes the
most" (the one actionable tile) does not lead. The accent text in that tile is 3.40:1 on the tile; it
passes only because 20.8 px bold counts as large text.
```css
@media (min-width: 1024px) {
  .hero-in { align-items: center; }
  .hero::after { right: auto; top: auto; left: -110px; bottom: -150px; width: 340px; height: 340px; border-width: 48px; border-color: rgba(242, 181, 68, .12); }
}
.kpi { background: rgba(0, 0, 0, .14); border-color: rgba(255, 255, 255, .14); }   /* accent text rises to about 4.9:1 */
.kpi:first-child { background: var(--accent); color: var(--on-accent); border-color: transparent; }
.kpi:first-child .kpi-label { color: var(--on-accent); opacity: .8; }
.kpi:first-child .kpi-link { color: var(--on-accent); }
```
(Check the darker tile background against `--on-brand-muted` with the WORKLOG contrast script before
committing; it should rise, not fall.)

### 7. Medium: on phones the hero pushes the main job below the fold

At 375x812 the hero is about 400 px tall; the record form starts at about 480 px and its Amount field sits
under the bottom bar (`home-375-light-en.jpg`). In हिंदी the KPI tiles wrap to three lines
("Prakash Rao (प्रकाश राव) · 61 दिन") with uneven heights (`home-375-dark-hi.jpg`).
```css
@media (max-width: 559px) {
  .hero-in { padding: 16px 16px 20px; gap: 14px; }
  .hero-total { font-size: 2.6rem; margin: 2px 0 4px; }
  .kpis { gap: 8px; grid-auto-rows: 1fr; }
  .kpi { padding: 10px; }
  .kpi-label { font-size: .74rem; }
  .kpi-value { font-size: 1.05rem; }
  .kpi .loc { display: none; }
  .hero::after { width: 180px; height: 180px; border-width: 28px; right: -50px; top: -50px; }
}
```

### 8. Medium: customer page wastes half the screen and buries the history

At 1366 and 1440 the content is capped at 820 px (`.cust-grid { max-width: 820px }`), the record form comes
first, and the ledger history (the thing a shopkeeper opens a customer to see) starts below the fold. The
hero actions float at the far right, about 330 px from the balance they act on.
```css
@media (min-width: 1100px) {
  .cust-grid { max-width: none; grid-template-columns: minmax(340px, 420px) minmax(0, 1fr); align-items: start; }
  .cust-grid > #entry-slot { grid-column: 1; grid-row: 1; position: sticky; top: calc(var(--top-h) + 24px); }
  .cust-grid > .cust-hist { grid-column: 2; grid-row: 1 / span 2; }
  .cust-grid > .cust-details { grid-column: 1; grid-row: 2; }
}
@media (min-width: 1024px) {
  .cust-hero .hero-in { grid-template-columns: minmax(0, 1fr); }
  .cust-actions { justify-content: flex-start; }
}
```

### 9. Medium: eight solid WhatsApp squares out-shout the balances

On every customer row the most saturated object is a filled green button, a secondary action, sitting next
to the red balance, the primary fact. Down the list it becomes a green stripe (`customers-1440-dark-hi.jpg`).
```css
.wa-btn { background: transparent; color: var(--wa); border: 1.5px solid color-mix(in srgb, var(--wa) 40%, transparent); }
.wa-btn:hover { background: var(--wa); color: #fff; }
:root[data-theme="dark"] .wa-btn { color: var(--paid); }
@media (prefers-color-scheme: dark) { :root:not([data-theme="light"]) .wa-btn { color: var(--paid); } }
```
#0F7A3F on white is 5.42:1 and #7BDDB3 on #13201B is 10.27:1, so the outline version stays well readable.
Keep the solid WhatsApp button on the customer page, where it is the main action.

### 10. Medium: the credit and payment switch has no colour until chosen

Unchosen "Payment (received)" is plain black text in a faint box; chosen "Credit (gave goods)" is pink.
The two options should look like the two sides of the ledger before a tap.
```css
.seg-credit { color: var(--owed); }
.seg-payment { color: var(--paid); }
.seg-opt:has(input:checked) { border-width: 2px; }
```
Add a leading glyph in the markup for speed of recognition: `<span aria-hidden="true">+</span>` in the
credit label and `<span aria-hidden="true">−</span>` in the payment label, styled
`.seg-opt span[aria-hidden] { font-size: 1.2rem; margin-right: 6px; }`.

### 11. Medium: the minus sign sits before the ₹ in entry amounts

Entries render "− ₹950" (`app.js` line 555: `sign + ' ' + money(e.amount)`). The project checklist
(client-brief-checklist C) says the minus goes next to the digits, never before the ₹, or a parser loses the
sign. Visually the pill and the colour already say credit or payment, so the cleanest fix is to drop the
sign: `'<span class="entry-amt ' + e.type + '">' + money(e.amount) + '</span>'`. Run the checker before
changing, in case a test reads the sign.

### 12. Low: icons are inconsistent on action buttons

The customer hero has an icon on "WhatsApp reminder" only; "UPI pay link" and "Statement (PDF)" are bare.
Save buttons have none while "Choose screenshot" has one. Add the same 20 px stroke icon set
(`class="ic"`, stroke 2) to UPI pay link (a link or QR glyph) and Statement (a file glyph), and give
`.btn .ic { width: 20px; height: 20px; }` so every button icon matches.

### 13. Low: section views float a narrow card in a large empty field

Shop file, Shop settings and Add a customer render one short card at 880 px with the rest of the canvas
empty (`sec-data-1440-light-en.jpg`). Once the width fix lands, give single-form views a page heading
outside the card and a short helper column, or cap these three at a form width and left-align them under a
heading so the emptiness reads as deliberate:
```css
#view-home[data-section="add"] .home-grid,
#view-home[data-section="settings"] .home-grid,
#view-home[data-section="data"] .home-grid { grid-template-columns: minmax(0, 640px); }
```
(Use the actual `data-section` values set by `app.js`.)

### 14. Low: "(fictional)" in the shop chip reads as a bug in the brand bar

The checklist requires sample data to be marked, so keep the marking, but style it: render the shop name
and a separate pill, `<span class="shop-sample" data-i18n="sample">Sample</span>`, with
`.shop-sample { font-size: .7rem; font-weight: 800; text-transform: uppercase; letter-spacing: .04em; background: var(--accent); color: var(--on-accent); border-radius: 999px; padding: 1px 8px; }`.
On phones the chip already truncates to "Laksm…", so the pill is also the only part that survives there.

### 15. Low: brand voice and mark

The amber notepad mark is generic and the tagline is 11.5 px. Against the category: Khatabook is blue,
Vyapar red and blue, OkCredit green, so Hisaab's deep green sits close to OkCredit; the warm paper ground
and the amber accent are what make it its own. Lean on them: put the Devanagari wordmark beside the Latin
one, `<span class="brand-hi" lang="hi">हिसाब</span>` with
`.brand-hi { font-family: var(--head); font-weight: 700; font-size: .9rem; color: var(--accent); margin-left: 6px; }`,
and raise `.brand-tag` to `.78rem`. In हिंदी the tagline already reads "खाता, आसान"; keep both lines.

## What was checked and is fine

Body and muted text pass AA everywhere measured (muted on page 6.65:1, owed and paid pills 6.00:1 and
5.36:1, dark owed on card 8.50:1, white on WhatsApp 5.42:1). One radius, two fonts, inline stroke icons in
the nav and bottom bar, 44 px targets, no sideways scroll at 375, and the हिंदी layouts hold without overflow.
Empty states exist in markup (`customer-empty`, `overdue-empty`, `cust-empty`) but were not exercised on
screen in this pass, because the first visit loads the sample shop.

## Not done in the time box

- Empty, missing and failed states not captured (needs a run with storage cleared and sample loading off).
- Telugu not captured.
- The colour fixes above are computed; re-run the project's WORKLOG contrast script after applying them.
