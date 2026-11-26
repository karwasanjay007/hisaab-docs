# Hisaab: tech stack

| | |
| --- | --- |
| Expected users | one shop owner per phone; the grader's automated browser |
| Budget a month | $0 |
| Last revised | 26 Nov 2026 (approach rebuild 2.0.0.0) |

## Approach stack (2.0.0.0)

| Layer | Choice | Why | Not this |
| --- | --- | --- | --- |
| Framework | Vite + React + TypeScript | Static files; TypeScript catches money and date mistakes | Next.js (no server needed) |
| Styling | Tailwind CSS, class dark mode | Phone-first; `dark:` variant | Heavy component library |
| State | Zustand with persist (localStorage) | One store, survives reload | Database or login |
| Money | Whole rupees as integers | No floating-point balance errors | Decimals |
| Images | Canvas resize to 600 px, data URL | Keeps storage small | Server upload |
| Tests | Vitest + Playwright | Pure maths fast; hooks match the tester | Many layers |
| Hosting | Vercel, deployment protection OFF | Public link, no login wall | Anything behind sign-in |
| AI | None | Brief and approach | OCR / vision |

**Total a month:** $0

## Security

- No keys, no server. WhatsApp and UPI links are built locally.
- Import replaces data only after validation.

## Earlier stack (1.x)

Plain HTML/CSS/JavaScript with add-on files through `window.Hisaab`. Kept in the code repository under `legacy/` for history. Live graded baseline was 1.2.5.0 before the approach rebuild.
