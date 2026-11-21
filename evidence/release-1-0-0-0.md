# Hisaab Version 1.0.0.0 — release evidence

**Owner:** Sanjay Karwa, Smart AI for Business  
**Date:** 21 November 2026  
**App:** https://hisaab-hazel.vercel.app  
**Code baseline:** private repository `hisaab`, tag `v1.0.0.0`  
**Docs baseline:** this repository, tag `v1.0.0.0`

## What this baseline includes

- You gave / You got on the record form (tester radio values unchanged).
- Overdue badge and Remind-all: 30 or more days without payment; full sorted owing list kept for the bonus.
- Phone overview: money and customers first; Shop file and settings under More.
- Offline after one visit; UPI QR; Hindi and Telugu; screenshot amount reading.

## Checks run on this baseline (local server)

```
node tests/brief.mjs http://127.0.0.1:4173/  → ALL PASS
node tests/check.mjs http://127.0.0.1:4173/ 4242
  → Summary: 27 PASS, 0 FAIL
  → Score estimate: 100/100
node tests/final.mjs … → ALL PASS (Remind all overdue (4); badge 4; list 8)
node tests/quick.mjs … → ALL PASS
node tests/nav.mjs … → ALL PASS
node tests/gaps.mjs … → ALL PASS
node tests/offline.mjs … → ALL PASS
```

Full checker log: `evidence/last-run-live.txt` (header shows the server URL used for the run).
