# #4471 split — MEGA S01E03 "what it shows"

The support desk merged 412 reports about dates into ticket **#4471** and marked it `known`.
This repo splits it into the problems it really holds, one issue per problem, and carries the kb
page the desk reads.

## The split

| Problem | Reports | Issue |
|---|---|---|
| A second cause inside the ticket: booking-v3's `MM/DD` hint on day-first tenants | 33 | [#1](../../issues/1) |
| One-day moves inside the flip count, from the desk's own note | 5 | [#2](../../issues/2) |
| Intake tag names the wrong cause (23 parser→`hint`, 15 `hint`→`parser`) | 38 | [#3](../../issues/3) |
| Duplicate rows in the export: 427 rows for 412 reports | 15 | [#4](../../issues/4) |

The parser cause keeps 379 reports, 374 of them after the 5 one-day moves are set aside.

## Files

```
kb/src/routes/kb/incidents/4471-split/+page.svx   the page, for the kb
kb/src/lib/kb/IncidentSplit.svelte                 its table, computed from the data
page/4471-split.html                               the page as the desk sees it
page/4471-split.png                                the same, captured
```

The page carries the course `ticket` as `signature:` in its frontmatter and ends with
`<RenderStamp {signature} />`, as the kb README requires. Drop the two `kb/` files into the
export and the page renders at `/kb/incidents/4471-split`.

## How the numbers were counted

The kb's own rules, not a guess: `causeOf(report, tenantOf(report))` and `uniqueById()` from
`src/lib/kb/causes.ts`, run over `src/lib/data/reports-4471.json`. Every number on the page comes
from the export; none is typed into a sentence.
