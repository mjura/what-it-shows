# what-it-shows

Support ticket #4471 merged 412 date reports as one known issue. Read by cause, they are three problems:

| Issue | Problem | Reports |
|---|---|---|
| [#1](https://github.com/mjura/what-it-shows/issues/1) | Shared date parser reads day-first dates month first | 374 |
| [#2](https://github.com/mjura/what-it-shows/issues/2) | booking-v3 form hint says MM/DD/YYYY but the form reads the date day first | 33 |
| [#3](https://github.com/mjura/what-it-shows/issues/3) | Bookings move by one day, not month/day swap (time zone) | 5 |

## The kb page

- `kb/src/routes/kb/incidents/4471-split/+page.svx`: the page, signed `mjura`, at its place in the support kb.
- `kb/src/lib/kb/SplitTable.svelte`: the component that computes the split from `reports-4471.json` and `desk-notes.json`.
- `rendered/4471-split.html`: the page as the desk sees it, built with SvelteKit and mdsvex from the kb export (desk ref `DR-CD8Q-6C5C`).
