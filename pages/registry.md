---
layout: page
title: GLC registry
permalink: /registry/
---

# GLC registry

<p class="meta">
  Source: <a href="https://tscnlab.github.io/glc-registry/registry.json">registry.json</a>
</p>

<div id="app">Loading…</div>

<script>
  function pill(status) {
    const s = (status || "unknown").toLowerCase();
    const cls = s === "pass" ? "pass" : s === "fail" ? "fail" : "unknown";
    return `<span class="pill ${cls}">${s}</span>`;
  }

  function fmtIso(iso) {
    if (!iso) return "";
    // keep it simple: show ISO or a short local time
    try {
      const d = new Date(iso);
      if (isNaN(d.getTime())) return iso;
      return d.toISOString().replace("T", " ").replace("Z", " UTC");
    } catch {
      return iso;
    }
  }

  async function main() {
    const url = "https://tscnlab.github.io/glc-registry/registry.json";
    const res = await fetch(url, { cache: "no-store" });
    if (!res.ok) throw new Error(`HTTP ${res.status} fetching registry.json`);
    const data = await res.json();

    const rows = data.datasets || [];
    const generated = data.generated_at_utc || "";

    // Sort: failing first, then pass, then unknown; within each, newest validation first
    const rank = (s) => (s === "fail" ? 0 : s === "pass" ? 1 : 2);
    rows.sort((a, b) => {
      const sa = (a.validation?.status || "unknown").toLowerCase();
      const sb = (b.validation?.status || "unknown").toLowerCase();
      if (rank(sa) !== rank(sb)) return rank(sa) - rank(sb);
      const ta = Date.parse(a.validation?.timestamp_utc || "") || 0;
      const tb = Date.parse(b.validation?.timestamp_utc || "") || 0;
      return tb - ta;
    });

    const html = `
      <p class="meta">
        Registry updated: <code>${fmtIso(generated)}</code>
        · Datasets: <code>${rows.length}</code>
      </p>

      <table>
        <thead>
          <tr>
            <th>Repo</th>
            <th>Status</th>
            <th>Commit</th>
            <th>Validator</th>
            <th>Validated at</th>
            <th>Report</th>
          </tr>
        </thead>
        <tbody>
          ${rows.map(d => {
            const v = d.validation || {};
            const repo = d.repo || d.id || "";
            const repoUrl = d.repo ? `https://github.com/${d.repo}` : "";
            const shaFull = v.commit_sha || "";
            const shaShort = shaFull ? shaFull.slice(0, 7) : "";
            const reportUrl = d.validation_url || "";
            const validatedAt = v.timestamp_utc || d.fetched_at_utc || "";
            return `
              <tr>
                <td>${repoUrl ? `<a href="${repoUrl}"><code>${repo}</code></a>` : `<code>${repo}</code>`}</td>
                <td>${pill(v.status)}</td>
                <td>${shaShort ? `<code title="${shaFull}">${shaShort}</code>` : ""}</td>
                <td><code>${v.validator_version || ""}</code></td>
                <td><code>${fmtIso(validatedAt)}</code></td>
                <td>${reportUrl ? `<a href="${reportUrl}">validation.json</a>` : ""}</td>
              </tr>
            `;
          }).join("")}
        </tbody>
      </table>

      <p class="small">
        Missing a dataset? Add it to <code>datasets.yml</code> in the registry repo and wait for the next scheduled run.
      </p>
    `;

    document.getElementById("app").innerHTML = html;
  }

  main().catch(err => {
    document.getElementById("app").innerHTML =
      `<p style="color:#b42318;"><b>Failed to load registry</b><br/><code>${String(err)}</code></p>`;
  });
</script>

<style>
  .meta { color: #555; margin: 0 0 16px; }
  table { border-collapse: collapse; width: 100%; }
  th, td { padding: 10px 12px; border-bottom: 1px solid #e5e5e5; vertical-align: top; }
  th { text-align: left; font-weight: 600; }
  .pill { display: inline-block; padding: 2px 10px; border-radius: 999px; font-weight: 600; font-size: 12px; }
  .pass { background: #e7f7ef; color: #137a3a; }
  .fail { background: #fdecec; color: #b42318; }
  .unknown { background: #f2f2f2; color: #555; }
  code { font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, monospace; font-size: 0.95em; }
  .small { font-size: 12px; color: #666; margin-top: 12px; }
</style>