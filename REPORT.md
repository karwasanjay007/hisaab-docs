# Hisaab: report

This page describes version 1.5.2, the version on the live link.

## Status

| Part of the specification | Status |
| --- | --- |
| The nine required items | Met, all nine |
| The six ids kept stable for automated tests | In place, all six |
| Another shop loaded through the import box | Met; thirty other shops load and every figure matches |
| The seven optional features | Met, all seven |
| Phone at 375 px, and light and dark | Met |
| Live link | https://hisaab-hazel.vercel.app |

How each requirement is met is in [the specification check](SPEC-CHECK.md).

## Latest results on the live link

Each test runs in a fresh browser against the live link and writes its result to a file in
[evidence/](evidence/). The files hold no run date and no version number, so none is given here. All 32 end in
a pass.

### The specification

| Test | What it covers | Result |
| --- | --- | --- |
| `brief` | Every required item of the specification | All pass |
| `check` | The stable ids, the optional features and the figures of generated shops | All pass |
| `run` | A second record of the same test | All pass |
| `sweep` | Other shops loaded and every figure compared | All pass, 30 shops |
| `final` | QR, remind all, undo of an import, search, CSV | All pass |
| `gaps` | Mobile and UPI checks, import of amounts and dates, CSV import | All pass |
| `critic` | A screenshot kept when Save is pressed at once; one clean balance per row | All pass, 14 checks |
| `contrast` | Text contrast after each action | All pass |

### Features

| Test | What it covers | Result |
| --- | --- | --- |
| `quick` | Quick entry: the sentences it reads, fill and save | All pass |
| `voice` | Voice quick entry; each failure has its message | All pass, 15 checks |
| `voice-quiet` | Voice never ends in silence | All pass, 11 checks |
| `pay` | The tappable pay link in the reminder, and the pay page | All pass, 15 checks |
| `sendways` | SMS, pick a contact, copy the message | All pass, 16 checks |
| `wacheck` | The WhatsApp check of a new number | All pass, 19 checks |
| `customer` | Totals, call and chat links, change phone, delete with Undo | All pass, 36 checks |
| `profile` | Shop profile and the balance line | All pass, 36 checks |
| `shops` | Several shops on one device | All pass, 25 checks |
| `langs` | Nine languages loaded on demand; same figures in each | All pass, 25 checks |
| `names` | Names in the script of the nine newer languages | All pass, 33 checks |
| `rupees` | Whole rupees; imports with paise rounded | All pass, 28 checks |
| `guard` | Notices for a full or blocked browser store; picture limits | All pass, 48 checks |
| `assist` | Theme button, navigation, skip link, unknown name in quick entry | All pass, 38 checks |
| `offline` | One visit online, then offline: the app opens and the QR draws | All pass |
| `local-assets` | Fonts and the QR library come from the site; no other host asked | All pass, 13 checks |

### Layout and look

| Test | What it covers | Result |
| --- | --- | --- |
| `phone-ledger` | The chat-style layout at four phone sizes | All pass, 147 checks |
| `desk-ledger` | The chat-style layout from 768 px up | All pass, 149 checks |
| `phone-import` | Nothing scrolls sideways at 375 px; a shop file without entries loads | All pass, 12 checks |
| `addons-width` | No sideways scroll with profile and shops, three languages, both themes | All pass, 54 checks |
| `layout` | The layout in every language and width | All pass, 7 checks |
| `nav` | Every navigation item opens its own view | All pass |
| `brand` | The look at 375 and 1280 px, light and dark; text size; tap targets | All pass, 184 checks |
| `review-fixes` | Search box, labels under the total, Record label, demo UPI notice | All pass, 16 checks |

Each record is the file `last-<test>-live.txt`. Which test proves which acceptance criterion is in
[the epics](docs/epics.md).

## Requirement and feature map

Every requirement of the specification and every feature beyond it. R1 to R9 are required, B1 to B7 optional
and M is quality; N/A is a feature the specification does not ask for. "Pass" means a test record from the
live link proves it. "Yes" means the feature is there and no live record covers it by itself.

### Requirements of the specification

| Req | Feature | Result |
| --- | --- | --- |
| R1 | Add a customer with a name and a 10-digit phone | Pass |
| R2 | Add an entry: credit or payment, amount, date, note | Pass |
| R3 | Each balance, and the total outstanding at the top | Pass |
| R4 | Customer page lists entries, newest first | Pass |
| R5 | Payment screenshot shown on the entry | Pass |
| R6 | WhatsApp reminder with the name and the balance | Pass |
| R7 | Data still there after a reload | Pass |
| R8 | Import box for a shop file in the sample format | Pass |
| R9 | Works at 375 px | Pass |
| B1 | Overdue list sorted by days since the last payment | Pass |
| B2 | Edit and delete an entry | Pass |
| B3 | CSV export that matches the data | Pass |
| B4 | Search by name or phone | Pass |
| B5 | UPI pay link in the reminder | Pass |
| B6 | Hindi or Telugu labels | Pass |
| B7 | Reading the amount from the screenshot | Pass |
| M | Readable in light and dark | Pass |

### Features beyond the specification

| Req | Feature | Result |
| --- | --- | --- |
| N/A | Languages beyond Hindi and Telugu | Pass, twelve in all |
| N/A | Quick entry by typing or by voice | Pass |
| N/A | UPI QR code | Pass |
| N/A | Printable statement (PDF) | Yes |
| N/A | Remind all overdue customers in turn | Pass |
| N/A | Undo after a delete, an edit or an import | Pass |
| N/A | Works without signal after the first visit | Pass |
| N/A | Backup file (JSON) | Yes |
| N/A | Import by choosing a file | Yes |
| N/A | Sample or demo shop in one tap | Yes |
| N/A | Overview figures beyond the total | Yes |
| N/A | Total unpaid for 30 days or more | Yes |
| N/A | The date the figures are for | Yes |
| N/A | Shop profile: owner, address, logo | Pass |
| N/A | Balance history line | Pass |
| N/A | More than one shop on the same phone | Pass |
| N/A | Customer page totals and days since the last payment | Pass |
| N/A | Call and WhatsApp chat links for a customer | Pass |
| N/A | Rename a customer and change the phone number | Pass |
| N/A | Delete a customer | Pass, with Undo |
| N/A | Note shortcuts | Yes |
| N/A | Initial of the name beside each customer | Yes |
| N/A | Names in the script of every language offered | Pass, twelve |
| N/A | Tappable pay link in the WhatsApp reminder | Pass |
| N/A | Other ways to send when WhatsApp refuses a number | Pass |
| N/A | WhatsApp check when a customer is added | Pass |
| N/A | A voice failure says why and offers another way to speak | Pass |
| N/A | Quick entry offers to add a name no customer has | Pass |
| N/A | Money in whole rupees | Pass |
| N/A | A notice when the browser cannot keep a change | Pass |
| N/A | Chat-style layout on phone and desktop | Pass |
| N/A | Urdu right to left | Pass |
| N/A | Fonts and the QR library served from the site | Pass |

## Known limits

- **One browser, one ledger.** The data is in the browser of one device. It does not follow the owner to
  another phone, and it is lost if that browser's data is cleared, unless a backup file was exported first.
  The backup file does not hold the screenshots.
- **Nine languages need one online use.** Bengali, Marathi, Tamil, Gujarati, Kannada, Malayalam, Punjabi, Odia
  and Urdu each load when first chosen. Before that they are not available offline, and the app stays in
  English with a message.
- **The screenshot reader needs a connection.** Offline, the picture is still attached; the amount is typed
  by hand.
- **Voice depends on the browser.** The microphone button shows only where the browser has speech
  recognition, and it needs the internet. Typing the same sentence always works.
- **Some features know three languages only.** Voice and screenshot reading work in English, Hindi and Telugu.
  Native digits are for Hindi and Telugu. The notices about a full browser store and about whole rupees are
  in English, Hindi and Telugu, and show in English in the other nine languages.
- **The demo UPI ID.** Until the shop sets its own UPI ID in Shop settings, reminders and QR codes ask
  customers to pay `hisaab.demo@upi`. The app says so beside the reminder.
- **A line in the app says more than is true.** The foot of the navigation reads "Your ledger stays on this
  phone. Nothing is sent anywhere." The ledger does stay, but two features contact another party when used;
  the exact account is under [Privacy](README.md#privacy).
- **The total is net of advances.** "Total outstanding" adds every balance, so a customer in advance lowers
  it; a note under it gives the amount owed and the amount in advance.
- **A reminder is opened, not sent.** The owner presses Send in WhatsApp for each one, also with "Remind all
  overdue".

## Not tested

- A real phone in a shop owner's hand; the tests run in a browser set to phone sizes.
- A real spoken entry. Voice is tested with a stand-in for the browser's speech service.
- Reading the amount from a real UPI screenshot. It is tested on generated pictures only.
- A native speaker's read of the translations and of the newest notices in eleven languages.
- Step 7 of the README's "Try it" (opening the app with no connection) was not tried again by hand on the
  live link; steps 1 to 6 were.
- Other browsers than the one the tests drive.
