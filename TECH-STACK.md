# Hisaab: tech stack

| | |
| --- | --- |
| Expected users | one shop owner per phone; the grader's automated browser |
| Budget a month | $0 |
| Version described | 2.0.1.0 |
| Last revised | 27 Nov 2026, from `package.json` and the code |

## Stack of 2.0.1.0

| Layer | Choice | Why | Not this |
| --- | --- | --- | --- |
| Build | Vite 6 | Fast build to static files | A server framework: there is no server |
| Screen | React 19 with TypeScript 5.7 | Typed components; the ledger shapes are checked at build time | Plain scripts, as 1.x was |
| Styles | Tailwind CSS 4 (Vite plugin) | Light and dark from one set of classes | A component library |
| State and storage | Zustand 5 with its `persist` middleware, to `localStorage` | One store; every change is written to the browser at once | A database or a server |
| Fonts | Fraunces and Source Sans 3, bundled with the site (`@fontsource-variable`) | The page asks no other site for anything | A font service |
| Offline | A hand-written service worker, `public/sw.js`, cache `hisaab-v20`, network first | A new release is never hidden behind an old copy | A precache plugin |
| Unit tests | Vitest 3 with jsdom | Ledger sums, import rules, carry-over and the full-store case | |
| Browser tests | Playwright (Chromium) against the built site | The grader hooks and the fixes of 2.0.1.0 | |
| Checker | `tests/check.mjs`, written from the brief alone | The same checker 1.x was scored with | |
| Hosting | Static files on Vercel; every address is served `index.html` | Free, one command to deploy | |

There are five runtime packages: `react`, `react-dom`, `zustand` and the two font packages. There is no AI
service, no key and no backend.

## Commands

| Command | What it does |
| --- | --- |
| `npm test` | Vitest, 32 tests |
| `npm run build` | Type check, then the static build |
| `npm run test:e2e` | Build, then Playwright, 17 tests |
| `node tests/check.mjs <address> <seed>` | The checker against a running site |

## Security

- No secrets exist: nothing to leak from the build or the browser.
- Text from an imported file is shown as text by React, never as markup.
- An imported picture is kept only when it is an image data URL of 400,000 characters or fewer.
- CSV cells with a comma, a quote or a new line are quoted. A cell that starts with `=`, `+`, `-` or `@` is not prefixed in 2.0 (1.x did prefix it).
- `npm audit` reports 3 advisories in build tools; not looked into.

## Earlier stack (1.x)

Versions 1.0.0.0 to 1.2.5.0 were plain HTML, CSS and JavaScript with no build step, plus add-on scripts. That
code is no longer what runs. It is kept in the code repository under `legacy/`.
