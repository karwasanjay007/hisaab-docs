# Hisaab: implementation plan

| | |
| --- | --- |
| Last revised | 20 Nov 2026 |

## Steps

| # | Slice | Done when | Requirement |
| --- | --- | --- | --- |
| 1 | Page shell, light and dark theme, 375 px layout | Page loads locally; no sideways scroll at 375 px | 5.2 stage 1 |
| 2 | Store, balances, total, rows with the grader's attributes | Sample loaded: `#total-outstanding` shows 11820; every `[data-balance]` matches `sample-expected.json` | F-1, F-3, S-1 |
| 3 | Import box `#import-box` and `#import-go`, with validation | A different shop replaces the data; a bad file changes nothing | D-2, S-4 |
| 4 | Add customer, add entry, customer page newest first | Entries persist after reload | F-2, F-5, S-3 |
| 5 | Reminder link `a[data-remind]` to `https://wa.me/91<phone>?text=` | Message holds name and balance in rupees | S-2 |
| 6 | Payment screenshot `#shot-input`, `img[data-shot]` | Saved payment shows the picture after reload | 5.2 stage 2 |
| 7 | Bonus: overdue list, edit and delete, CSV export, search, UPI link, Hindi and Telugu, amount from screenshot | Each checked by script | 5.2 stage 2 |
| 8 | `?today=` honoured everywhere | Overdue days computed from the given date | F-4 |
| 9 | Independent checker (separate agent, brief only) passes on local and live | All checks green | All |

## Where the build will likely get stuck

- The grader reads the form on the page it lands on: keep the entry form and the import box on the home
  view, with recent entries (and their screenshots) visible there too.
- Pop-up dialogs stall automated browsers: no `alert` or `confirm`; delete uses undo.
- localStorage quota with photos: resize before saving.

## Release

| # | Step | Done when |
| --- | --- | --- |
| R1 | Security pass: no secrets, imported text escaped | Review output in REPORT.md |
| R2 | Deploy to a `*.vercel.app` address (never sai4b.com) | Live URL fetched, 200 pasted |
| R3 | Check at 1440 px and 375 px, light and dark | Screenshots looked at; fixes in REPORT.md |
