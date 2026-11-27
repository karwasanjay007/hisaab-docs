# Links: WhatsApp reminder and UPI pay address

Hisaab 2.0.1.0 sends nothing itself. Each customer row carries one link that opens WhatsApp with the text
already filled in; the shop owner presses Send. Source: `reminderHref()` and `reminderText()` in
`src/lib/ledger.ts`, used by `src/components/CustomerRow.tsx`.

## WhatsApp reminder URL

```
https://wa.me/91<phone>?text=<text>
```

- `<phone>` is the customer's 10-digit phone; `91` is always put in front.
- `<text>` is the message, encoded with `encodeURIComponent`.
- The link is the element `[data-remind="<phone>"]`, opens in a new tab (`target="_blank"`,
  `rel="noopener noreferrer"`) and is present on every row, whatever the balance.

## Message text

The wording follows the language chosen in the header and the sign of the balance. `{name}` is the customer's
name; `{rs}` is `Rs ` and the amount without its sign in Indian grouping (for example `Rs 3,470`), the same
in every language.

| Language | Owes (balance above 0) | Advance (balance below 0) | Zero |
| --- | --- | --- | --- |
| English (`en`) | `Namaste {name}, your balance with us is {rs}. Please pay when you can.` | `Namaste {name}, you have an advance of {rs} with us.` | `Namaste {name}, your balance with us is Rs 0.` |
| Hindi (`hi`) | `नमस्ते {name}, हमारे यहाँ आपका बकाया {rs} है। कृपया सुविधा से चुका दें।` | `नमस्ते {name}, आपके {rs} हमारे पास जमा हैं।` | `नमस्ते {name}, आपका बकाया Rs 0 है।` |
| Telugu (`te`) | `నమస్తే {name}, మా వద్ద మీ బకాయి {rs}. వీలైనప్పుడు చెల్లించండి.` | `నమస్తే {name}, మీ అడ్వాన్స్ {rs} మా వద్ద ఉంది.` | `నమస్తే {name}, మీ బకాయి Rs 0.` |

## UPI pay address

Only when the balance is above zero, this is added to the end of the message, after one space:

```
Pay: upi://pay?pa=<id>&am=<balance>&cu=INR
```

- `pa` is the shop's UPI id from Settings, trimmed and encoded; when none is set it is the demo id
  `hisaab.demo@upi`, and Settings says so.
- `am` is the balance in whole rupees, plain digits.
- `cu` is always `INR`. The order is `pa`, `am`, `cu`; there are no other parameters.
- The UPI id is not checked for shape in 2.0.1.0: what the owner types is used.

The address is text inside the WhatsApp message, not a link in the page. WhatsApp does not make a `upi://`
address tappable in every app; the customer may have to copy it.

## Not in 2.0.1.0

Version 1 also had a receipt link after saving an entry, a pay page with an https link, a UPI QR code, a
remind-all queue, SMS and copy as other ways to send, and Call and chat links on the customer page. None of
these was rebuilt.
