<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { groupByCause, readAs, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  // The desk's view: one row per cause (causes.ts), counted by tenant. Reports the desk read as a
  // one-day move (desk-notes.json) stay counted under their cause and get a row of their own too.
  type Problem = 'parser' | 'hint' | 'one-day' | 'unplaced';
  let { ticket, issues, repo }: { ticket: number; issues: Partial<Record<Problem, number>>; repo: string } = $props();

  const merged = $derived(uniqueById(reports as Report[]).filter((r) => r.mergedInto === ticket));
  const groups = $derived(groupByCause(merged, tenantOf));
  const rows = $derived.by(() => {
    const byCause = groups.map((g) => ({ problem: g.cause as Problem, n: g.reports.length, tenants: g.tenants, first: g.firstSeen, last: g.lastSeen, within: null as string | null }));
    const moved = merged.filter((r) => notes.oneDayMove.includes(r.id));
    const days = moved.map((r) => r.receivedAt.slice(0, 10)).sort();
    const home = groups.filter((g) => readAs(g, notes.oneDayMove)).map((g) => g.cause).join(', ');
    return moved.length
      ? [...byCause, { problem: 'one-day' as Problem, n: moved.length, tenants: new Set(moved.map((r) => r.tenant)).size, first: days[0], last: days[days.length - 1], within: home }]
      : byCause;
  });
  const total = $derived(groups.reduce((s, g) => s + g.reports.length, 0));
</script>

<p class="sum">
  <strong>{merged.length}</strong> reports merged into #{ticket} · <strong>{rows.length}</strong> problems ·
  {total === merged.length ? 'every report counted under one cause' : `${merged.length - total} reports not counted`}
</p>

<table>
  <thead><tr><th>problem</th><th>reports</th><th>tenants</th><th>seen</th><th>ticket</th></tr></thead>
  <tbody>
    {#each rows as r (r.problem)}
      <tr>
        <td><code>{r.problem}</code></td>
        <td>{r.n}{#if r.within}<span class="within"> · also in <code>{r.within}</code></span>{/if}</td>
        <td>{r.tenants}</td>
        <td>{r.first} → {r.last}</td>
        <td>
          {#if issues[r.problem]}<a href="https://github.com/{repo}/issues/{issues[r.problem]}">{repo}#{issues[r.problem]}</a>{:else}none{/if}
        </td>
      </tr>
    {/each}
  </tbody>
</table>

<style>
  .sum { font-size: 1.1rem; }
  .within { color: var(--muted); font-size: 0.85em; }
</style>
