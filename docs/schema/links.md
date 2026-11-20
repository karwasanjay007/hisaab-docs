# Links: WhatsApp reminders, receipts and UPI

Hisaab sends nothing itself. It builds links that open WhatsApp or a UPI app with the text or payment
already filled in; the shopkeeper presses Send or Pay. Source: `waLink()`, `reminderText()`,
`upiLink()`, `receiptLink()`, `renderRemindAll()` and `remindNextOne()` in `app.js`, and the `msg*` and
`receipt*` strings in its `I18N` table.

## WhatsApp reminder URL

```
https://wa.me/91<phone>?text=<encodeURIComponent(message)>
```

- `<phone>` is the customer's stored 10-digit phone; `91` (India) is always put in front. No `+`.
- `message` is encoded once with `encodeURIComponent` (spaces become `%20`, the UPI link inside the
  message is encoded again as part of the text).
- Where it appears: `a[data-remind="<phone>"]` on every customer row (with `target="_blank"`), the
  Remind button on the customer page (`#cust-remind`), and the "Remind all" button (`#remind-all`).
- The link is rebuilt on every render, so it always carries the current balance.

## Message text

The message follows the language chosen in the app (`hisaab.lang`: `en`, `hi` or `te`) at the time the
link is built. `{name}` is the customer's `name` (the English name, not `name_hi` or `name_te`), `{shop}`
is the shop name, and `{amt}` is the amount formatted with `toLocaleString('en-IN')`, at most 2 decimals,
Indian grouping and Latin digits even when Hindi or Telugu digits are switched on (`3,470`,
`1,23,456.5`). Amounts are prefixed `Rs`, not the rupee sign.

Which template is used depends on the balance:

| Balance | Template key | Contents |
| --- | --- | --- |
| more than 0 | `msg` with `{upi}` = `msgUpi` | reminder with the amount and a UPI pay link |
| exactly 0 | `msgClear` | account settled, Rs 0 due |
| less than 0 | `msgAdvance` with `{amt}` = the advance as a positive number | money held in advance |

### English (`en`)

| Key | Text |
| --- | --- |
| `msg` | `Namaste {name}, this is a reminder from {shop}. Your balance is Rs {amt}.{upi} Thank you.` |
| `msgUpi` | ` Pay by UPI: {link}` (note the leading space) |
| `msgAdvance` | `Namaste {name}, this is {shop}. You have Rs {amt} in advance with us. Thank you.` |
| `msgClear` | `Namaste {name}, this is {shop}. Your account is settled, Rs 0 due. Thank you.` |

Example for the sample customer Sunita Devi (balance 3470, no UPI ID saved):

```
Namaste Sunita Devi, this is a reminder from Lakshmi Provision Store (fictional). Your balance is Rs 3,470. Pay by UPI: upi://pay?pa=hisaab.demo%40upi&pn=Lakshmi%20Provision%20Store%20(fictional)&am=3470.00&cu=INR&tn=Hisaab%20Sunita%20Devi Thank you.
```

### Hindi (`hi`)

| Key | Text |
| --- | --- |
| `msg` | `नमस्ते {name}, {shop} से याद दिला रहे हैं। आपका बकाया Rs {amt} है।{upi} धन्यवाद।` |
| `msgUpi` | ` UPI से भुगतान करें: {link}` |
| `msgAdvance` | `नमस्ते {name}, {shop} से। आपके Rs {amt} अग्रिम जमा हैं। धन्यवाद।` |
| `msgClear` | `नमस्ते {name}, {shop} से। आपका खाता चुकता है, Rs 0 बाकी। धन्यवाद।` |

### Telugu (`te`)

| Key | Text |
| --- | --- |
| `msg` | `నమస్తే {name}, {shop} నుండి గుర్తు చేస్తున్నాం. మీ బకాయి Rs {amt}.{upi} ధన్యవాదాలు.` |
| `msgUpi` | ` UPI ద్వారా చెల్లించండి: {link}` |
| `msgAdvance` | `నమస్తే {name}, {shop} నుండి. మీ Rs {amt} అడ్వాన్స్ మా వద్ద ఉంది. ధన్యవాదాలు.` |
| `msgClear` | `నమస్తే {name}, {shop} నుండి. మీ ఖాతా పూర్తయింది, Rs 0 బాకీ. ధన్యవాదాలు.` |

## Receipt link (after saving an entry)

When an entry is saved, the toast offers a "Send receipt" link to the same customer:

```
https://wa.me/91<phone>?text=<encodeURIComponent(receipt message)>
```

| Key | English text |
| --- | --- |
| `receiptCredit` | `Namaste {name}, {shop}: Rs {amt} added to your account on {date}. Balance now Rs {bal}.` |
| `receiptPayment` | `Namaste {name}, {shop}: payment of Rs {amt} received on {date}, thank you. Balance now Rs {bal}.` |

`{amt}` is the entry amount and `{bal}` the customer's new balance, both formatted as above (a negative
balance shows as `-180`). `{date}` is the entry date written out in the app language (for example
`6 Oct 2026` in English, in Devanagari or Telugu digits when that setting is on). Hindi and Telugu
versions exist under the same keys.

## UPI pay URL

```
upi://pay?pa=<pa>&pn=<pn>&am=<am>&cu=INR&tn=<tn>
```

| Parameter | Value |
| --- | --- |
| `pa` | `encodeURIComponent` of the saved UPI ID (`state.upi`, trimmed); when none is saved, the built-in demo ID `hisaab.demo@upi` |
| `pn` | `encodeURIComponent` of the shop name |
| `am` | the balance, never below 0, rounded to 2 decimals and written with exactly 2 decimals (`3470.00`) |
| `cu` | always `INR` |
| `tn` | `encodeURIComponent` of `Hisaab ` followed by the customer's name |

Where it appears: inside the reminder message when the balance is above 0, the "Pay by UPI" button on the
customer page (`#cust-upi`, shown only when the balance is above 0), the UPI QR code (`#cust-qr`, the same
link drawn as a QR in the page), and the QR on the printed statement.

### UPI ID check

A UPI ID must pass `validUpi()` before it is saved:

```
^[a-zA-Z0-9._-]{2,256}@[a-zA-Z][a-zA-Z0-9]{1,63}$
```

That is 2 to 256 letters, digits, dots, underscores or hyphens, then `@`, then a letter followed by 1 to
63 letters or digits (for example `shopname@okbank`).

- Shop settings: an empty value is allowed, and the demo ID `hisaab.demo@upi` is then used. A value that
  fails shows "Enter a UPI ID like shopname@okbank, or leave it empty." in `#settings-msg`, focuses
  `#upi-id`, and nothing is saved.
- Import: a `upi` or `upi_id` in the file that fails is listed under Skipped, and the UPI ID already saved
  in the app is kept; the rest of the file still loads.

## Remind-all queue

`#remind-all` sends the reminders one WhatsApp chat at a time:

1. The queue is the phones of the overdue list, in its order: customers with a balance above 0, sorted by
   days since their last payment (or since their first credit when they never paid), most days first,
   then by larger balance. See [../data-model.md](../data-model.md) for the day count.
2. The button is hidden when fewer than 2 customers are overdue.
3. The button is a link to the reminder for the customer at the current position. Each press opens that
   chat and moves the position on by one; the label then names the next customer (`Next reminder: {name} ({i} of {n})`
   in English; it starts as `Remind all overdue ({n})`).
4. After the last customer the label reads `All {n} reminders opened` and the link points to the first customer again;
   the next press starts the round again.
5. When the overdue list changes (a payment saved, a new import), the queue is rebuilt and the position
   goes back to the start. The position is held in memory only and is lost on reload.
