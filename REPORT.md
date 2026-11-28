# Hisaab: report

This page describes version 1.5.2, the version on the live link.

## Status

| Part of the brief | Status |
| --- | --- |
| The nine things it must do | Met, all nine |
| The six things the tester reads | In place, all six |
| Hidden data: another shop loaded through the import box | Met; thirty other shops load and every figure matches |
| Bonus menu | Met, all seven (six count) |
| Phone at 375 px, and light and dark | Met |
| Live link | https://hisaab-hazel.vercel.app |

How each line is met is in [the specification check](SPEC-CHECK.md).

## Latest results on the live link

Each test runs in a fresh browser against the live link and writes its result to a file in
[evidence/](evidence/). The files hold no run date and no version number, so none is given here. All 32 end in
a pass.

### The brief

| Test | What it covers | Result |
| --- | --- | --- |
| `brief` | Every "Done when" line of the brief | All pass |
| `check` | A checker written from the brief alone | Score estimate 100 of 100 |
| `run` | A second record of the same checker | Score estimate 100 of 100 |
| `sweep` | Hidden shops loaded and every figure compared | All pass, 30 shops |
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

Every requirement of the brief and every feature beyond it, with three other builds of the same project
beside Hisaab (called A, B and C). R1 to R9 are the nine "must do" items, B1 to B7 the bonus menu, M the light
and dark marks; N/A means the brief does not ask for it.

How to read the cells. Hisaab: "Pass" means a test record from the live link proves it; "Yes" means the
feature is there and no live record covers it by itself. Other builds: only their screens were read and
nothing was typed. "Seen" means visible on those screens, "Seen in part" that part of it is visible, "Not
tested" that it cannot be judged without using the app, "No" that it was not on the screens read, and "Not
looked for" that the feature came to Hisaab after those screens were read. Build C was read with no shop
loaded, so most of its rows cannot be judged.

### The brief's requirements

| Req | Feature | Hisaab | Build A | Build B | Build C |
| --- | --- | --- | --- | --- | --- |
| R1 | Add a customer with a name and a 10-digit phone | Pass | Seen | Seen | Seen |
| R2 | Add an entry: credit or payment, amount, date, note | Pass | Seen | Seen in part | Not tested |
| R3 | Each balance, and the total outstanding at the top | Pass | Seen | Seen | Seen in part |
| R4 | Customer page lists entries, newest first | Pass | Seen | Seen in part | Not tested |
| R5 | Payment screenshot shown on the entry | Pass | Seen in part | Seen in part | Not tested |
| R6 | WhatsApp reminder with the name and the balance | Pass | Seen in part | Seen in part | Not tested |
| R7 | Data still there after a reload | Pass | Not tested | Not tested | Not tested |
| R8 | Import box for a shop file in the sample format | Pass | Seen | Seen | Seen |
| R9 | Works at 375 px | Pass | Not tested | Not tested | Not tested |
| B1 | Overdue list sorted by days since the last payment | Pass | Seen in part | Seen in part | Seen in part |
| B2 | Edit and delete an entry | Pass | Seen | No | Not tested |
| B3 | CSV export that matches the data | Pass | Seen | Seen | Seen |
| B4 | Search by name or phone | Pass | Seen | Seen | No |
| B5 | UPI pay link in the reminder | Pass | Not tested | Seen in part | Not tested |
| B6 | Hindi or Telugu labels | Pass | Seen | Seen in part | Seen |
| B7 | Reading the amount from the screenshot | Pass | Seen in part | No | Not tested |
| M | Readable in light and dark | Pass | Seen in part | Seen in part | No |

### Features beyond the brief

| Req | Feature | Hisaab | Build A | Build B | Build C |
| --- | --- | --- | --- | --- | --- |
| N/A | Languages beyond Hindi and Telugu | Pass, twelve in all | Yes, twelve | No | No |
| N/A | Quick entry by typing or by voice | Pass | No | No | No |
| N/A | UPI QR code | Pass | No | No | No |
| N/A | Printable statement (PDF) | Yes | No | No | No |
| N/A | Remind all overdue customers in turn | Pass | No | No | No |
| N/A | Undo after a delete, an edit or an import | Pass | No | No | No |
| N/A | Works without signal after the first visit | Pass | Not tested | Not tested | Not tested |
| N/A | Backup file (JSON) | Yes | Yes | No | No |
| N/A | Import by choosing a file | Yes | Yes | No | Yes |
| N/A | Sample or demo shop in one tap | Yes | Yes | Yes | No |
| N/A | Overview figures beyond the total | Yes | Yes | Seen in part | No |
| N/A | Total unpaid for 30 days or more | Yes | Yes | No | No |
| N/A | The date the figures are for | Yes | Yes | No | Yes |
| N/A | Shop profile: owner, address, logo | Pass | No | No | Not tested |
| N/A | Balance history line | Pass | No | No | No |
| N/A | More than one shop on the same phone | Pass | Seen in part | No | No |
| N/A | Customer page totals and days since the last payment | Pass | Yes | No | Not tested |
| N/A | Call and WhatsApp chat links for a customer | Pass | Yes | No | Not tested |
| N/A | Rename a customer and change the phone number | Pass | Yes | No | Not tested |
| N/A | Delete a customer | Pass, with Undo | Yes | No | Not tested |
| N/A | Note shortcuts | Yes | Yes | No | No |
| N/A | Initial of the name beside each customer | Yes | Yes | No | No |
| N/A | Names in the script of every language offered | Pass, twelve | Not tested | Not tested | Not tested |
| N/A | Tappable pay link in the WhatsApp reminder | Pass | Not tested | Not tested | Not tested |
| N/A | Other ways to send when WhatsApp refuses a number | Pass | Not tested | Not tested | Not tested |
| N/A | WhatsApp check when a customer is added | Pass | Not tested | Not tested | Not tested |
| N/A | A voice failure says why and offers another way to speak | Pass | Not tested | Not tested | Not tested |
| N/A | Quick entry offers to add a name no customer has | Pass | Not looked for | Not looked for | Not looked for |
| N/A | Money in whole rupees | Pass | Not looked for | Not looked for | Not looked for |
| N/A | A notice when the browser cannot keep a change | Pass | Not looked for | Not looked for | Not looked for |
| N/A | Chat-style layout on phone and desktop | Pass | Not looked for | Not looked for | Not looked for |
| N/A | Urdu right to left | Pass | Not looked for | Not looked for | Not looked for |
| N/A | Fonts and the QR library served from the site | Pass | Not looked for | Not looked for | Not looked for |

None of the features seen on the three other builds is missing from Hisaab.

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
- The judges' own tester. Its exact steps are not known; the score estimate above is our checker's.

## The note sent to the judges

Sent with the submission on 20 November 2026, 9:29 pm IST, as it was written then:

> Hisaab is a digital khata for a local kirana shop, like Khatabook. It opens empty, ready for the shop's own ledger, with one tap to load the sample shop; paste any shop file (JSON, or a spreadsheet saved as CSV) into #import-box and press #import-go to replace it (Undo puts the old shop back). All six tester hooks are in place, ?today= is honoured, and all seven bonus items are built: overdue list, edit and delete with undo, CSV export (entries and balances), search, a UPI pay link in every reminder, Hindi and Telugu, and reading the amount from a payment screenshot.
>
> The automations from the coaching sessions are in too: a WhatsApp reminder with the exact UPI amount, a UPI QR code on each customer page, a "Remind all overdue" queue that opens each chat in turn, a WhatsApp receipt after every entry, and a printable statement with a scan-to-pay QR.
>
> What makes it different: voice quick entry. Tap the microphone and say "Sunita 500 udhaar" (English, Hindi or Telugu) and the form fills itself; the shopkeeper checks and presses Save. Names show in Devanagari or Telugu script in brackets, money in ₹ with lakh grouping, light and dark themes, a left menu on desktop and a bottom bar at 375 px, and it works offline. No login; data stays on the phone.
>
> Tested on the live link: every "Done when" passes, and an independent checker scores 27/27 on four random shops.

Updated on 21 November 2026 for version 1.0.0.0, the short form:

> Hisaab is a kirana khata on the phone: customers, You gave / You got entries, total outstanding, and WhatsApp reminders with name, Rs amount and UPI. Open https://hisaab-hazel.vercel.app, Load sample shop, then record "Sunita 500 udhaar" (voice or type) and Save. Tap WhatsApp on any customer; open a customer for QR and statement. Starts empty; import via #import-box; works at 375 px, light and dark, English / हिंदी / తెలుగు; offline after one visit. Data stays on the device. Demo UPI until the shop sets its own. Built by Sanjay Karwa, Smart AI for Business. Docs: https://github.com/karwasanjay007/hisaab-docs
