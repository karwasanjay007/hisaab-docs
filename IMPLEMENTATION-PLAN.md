# Hisaab: implementation plan

| | |
| --- | --- |
| Last revised | 26 Nov 2026 (approach rebuild phases P0–P7) |

## Approach phases (2.0.0.0)

| Phase | What | Done when |
| --- | --- | --- |
| P0 | Vite React TypeScript, Tailwind, Zustand, Vitest, Playwright, theme toggle | Empty app builds; tests run with one command |
| P1 | `balanceOf`, `totalOutstanding`, newest-first, days since last payment | Sample + second shop Vitest pass |
| P2 | Persisted store; `#import-box` / `#import-go`; validate; replace-all | Import my-shop leaves no sample customer; bad JSON untouched |
| P3 | Home: `#total-outstanding`, customer rows, add customer | Sample totals match on screen |
| P4 | Customer page, entries, `?today=` | Payment lowers balance and survives reload |
| P5 | `wa.me` reminder; `#shot-input`; `img[data-shot]` | Three reminders decode; screenshot survives reload |
| P6 | Six bonuses with tests | Script proves each (UPI only when shop UPI set) |
| P7 | 375 px light/dark, cold-open announce, ship | Fresh browser imports second shop; no sideways scroll |

Tests first each phase. Never weaken a test to pass.

## Earlier plan (1.x)

See git history of this file for the plain-JS slices and version 1.1–1.2.5 add-on steps.
