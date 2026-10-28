<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { groupByCause, readAs, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  let { ticket }: { ticket: number } = $props();

  // Every number on the page comes from the export, never from a sentence.
  const unique = $derived(uniqueById(reports as Report[]));
  const merged = $derived(unique.filter((r) => r.mergedInto === ticket));
  const groups = $derived(groupByCause(merged, tenantOf));
  const hint = $derived(groups.find((g) => g.cause === 'hint'));
  const parser = $derived(groups.find((g) => g.cause === 'parser'));
  const moved = $derived(groups.reduce((n, g) => n + readAs(g, notes.oneDayMove), 0));
  // A tag that names another group's cause is a wrong tag.
  const misTagged = $derived(
    groups.reduce(
      (n, g) => n + Object.entries(g.taggedAs).filter(([tag]) => tag !== g.cause).reduce((s, [, c]) => s + c, 0),
      0,
    ),
  );

  const rows = $derived([
    {
      problem: 'second cause: booking-v3 date hint',
      reports: hint?.reports.length ?? 0,
      where: 'no ticket of its own',
    },
    { problem: 'one-day moves read as flips', reports: moved, where: `counted under ${parser?.cause}` },
    { problem: 'intake tag names the wrong cause', reports: misTagged, where: 'metadata, both causes' },
    { problem: 'duplicate rows in the export', reports: (reports as Report[]).length - unique.length, where: 'outside the unique count' },
  ]);
</script>

<p class="sum">
  <strong>{merged.length}</strong> unique reports in #{ticket} ·
  <strong>{(reports as Report[]).length}</strong> rows in the export ·
  <strong>{groups.length}</strong> causes
</p>

<table>
  <thead><tr><th>problem</th><th>reports</th><th>where it sits</th></tr></thead>
  <tbody>
    {#each rows as r (r.problem)}
      <tr class:wrong={r.problem.startsWith('second cause')}>
        <td>{r.problem}</td>
        <td>{r.reports}</td>
        <td>{r.where}</td>
      </tr>
    {/each}
  </tbody>
</table>

<style>
  .sum { font-size: 1.1rem; }
  .wrong td { background: var(--warn-bg); }
</style>
