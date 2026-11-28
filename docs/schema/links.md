# Hisaab: the links it builds

This page describes version 1.5.2, the version on the live link.

Hisaab sends no message itself. It builds links that open WhatsApp, the SMS app, the dialer or a UPI app with
the text or the payment already filled in; the owner presses Send or Pay there. The code is `waLink()`,
`reminderText()`, `payPage()`, `upiLink()` and `receiptLink()` in `app.js`, with `customer.js`, `sendways.js`
and `wacheck.js` for the customer page.

## Every link

| Link | Form | Where |
| --- | --- | --- |
| WhatsApp reminder | `https://wa.me/91<phone>?text=<message>` | Each customer row, "WhatsApp reminder" on the customer page, "Remind all overdue" |
| Receipt | `https://wa.me/91<phone>?text=<receipt>` | "Send receipt" in the toast after an entry is saved |
| Hello message | `https://wa.me/91<phone>?text=<hello>` | "Check on WhatsApp" |
| WhatsApp chat | `https://wa.me/91<phone>` | "WhatsApp chat" |
| Any contact | `https://wa.me/?text=<message>` | "Choose a contact in WhatsApp" |
| SMS | `sms:+91<phone>?&body=<message>` | "Send as SMS" |
| Call | `tel:+91<phone>` | "Call" |
| UPI pay | `upi://pay?pa=<pa>&am=<am>&pn=<pn>&cu=INR&tn=<tn>` | "UPI pay link", the QR code, the statement, inside the reminder |
| Pay page | `<site>/pay.html#pn=<pn>&tn=<tn>&pa=<pa>&am=<am>` | Inside the reminder |

`<phone>` is the customer's stored 10 digits; `91` is always put in front. Each message is encoded once with
`encodeURIComponent`. A link is rebuilt every time the page is drawn, so it carries the current balance.

## The reminder message

The message is in the language chosen in the app. `{name}` is the customer's name as typed, `{shop}` the shop
name, `{amt}` the amount with Indian grouping and Latin digits, written "Rs", not the rupee sign.

| Balance | English text |
| --- | --- |
| Above 0 | `Namaste {name}, this is a reminder from {shop}. Your balance is Rs {amt}.{upi} Thank you.` |
| Exactly 0 | `Namaste {name}, this is {shop}. Your account is settled, Rs 0 due. Thank you.` |
| Below 0 | `Namaste {name}, this is {shop}. You have Rs {amt} in advance with us. Thank you.` |

`{upi}` is, on its own lines, `UPI pay link: <pay page link>`, then ` Pay by UPI: <upi://pay link>`. The pay
page link is there because WhatsApp makes only https addresses tappable. It is built only when the app runs
on http or https.

## The receipt and the hello message

| Message | English text |
| --- | --- |
| Receipt, credit | `Namaste {name}, {shop}: Rs {amt} added to your account on {date}. Balance now Rs {bal}.` |
| Receipt, payment | `Namaste {name}, {shop}: payment of Rs {amt} received on {date}, thank you. Balance now Rs {bal}.` |
| Hello | `Namaste {name}, this is {shop}. We have saved this number for your account with us. If this is not you, please tell us.` |

## The UPI pay link

| Parameter | Value |
| --- | --- |
| `pa` | The shop's UPI ID; when none is saved, the demo ID `hisaab.demo@upi` |
| `am` | The balance, never below 0, with exactly two decimals (`3470.00`) |
| `pn` | The shop name |
| `cu` | Always `INR` |
| `tn` | `Hisaab ` followed by the customer's name |

A UPI ID must match `^[a-zA-Z0-9._-]{2,256}@[a-zA-Z][a-zA-Z0-9]{1,63}$` before it is saved, for example
`shopname@okbank`. An empty value is allowed in Shop settings; the demo ID is then used and the app says so.

## The pay page

`pay.html` reads `pn`, `tn`, `pa` and `am` from the part of the address after `#` (or after `?`). A browser
does not send the part after `#` to a server. The page checks the UPI ID pattern and an amount above 0 and
under 10,00,00,000, shows the shop, the amount and the UPI ID with "Copy", and offers "Open your UPI app". On a
phone it opens the `upi://pay` link by itself after 0.4 seconds unless the address has `stay=1`. A link that
fails the checks shows "This payment link is not complete. Ask the shop to send it again."

## Remind all overdue

1. The queue is the customers who are [overdue](../data-model.md#derived-values), longest without paying
   first, then the larger balance.
2. The button shows only when two or more customers are in the queue. Its label starts as
   `Remind all overdue ({n})`.
3. Each press opens the next customer's WhatsApp chat; the label becomes
   `Next reminder: {name} ({i} of {n})`, and after the last one `All {n} reminders opened`.
