# Critique polish fixes (21 Nov 2026)

Source critique: store `docs/hisaab-critique.md` (9.4/10). Ranked near-term items that code can fix:

1. Relabel Credit/Payment to **You gave / You got** (radios keep `credit` / `payment` for hooks).
2. Cap Overdue **badge** and Remind-all to **30+ days** without payment; keep the full sorted owing list for the bonus.
3. Phone overview: money + customers (+ overdue) only; Record / Shop file / settings under the bottom bar.

Projects-page re-capture and spoken-voice demo remain human / auth-blocked.

## Local evidence (this branch)

```
node tests/brief.mjs http://127.0.0.1:4173/  → ALL PASS
node tests/check.mjs http://127.0.0.1:4173/ 4242
  → Summary: 27 PASS, 0 FAIL
  → Score estimate: 100/100
  → [9-bonus overdue] overdue list of 8 sorted … (full list kept)
node tests/final.mjs … → ALL PASS
  → Remind all overdue (4); badge 4; list still 8
node tests/quick.mjs … → ALL PASS
node tests/nav.mjs … → ALL PASS (phone overview shows 2 cards)
node tests/gaps.mjs … → ALL PASS
node tests/offline.mjs … → ALL PASS
```

Full logs: `evidence/last-brief-live.txt`, `evidence/last-run-live.txt` (base URL in the file headers is the local server for this polish run).

## Screenshots

Store media (after polish): `hisaab-ui-desktop-home.png`, `hisaab-ui-overdue.png`, `hisaab-ui-record-gave-got.png`, `hisaab-ui-customer-ravi.png`, `hisaab-ui-desktop-dark.png`, `hisaab-ui-mobile-375.png`.
