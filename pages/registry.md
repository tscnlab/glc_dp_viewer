---
layout: default
title: Registry
nav_order: 50
---

# GLC Registry

<p>
  Source: <a href="https://tscnlab.github.io/glc-registry/registry.json">registry.json</a>
</p>

<div id="app">Loading…</div>

<script>
  function pill(status) {
    const s = (status || "unknown").toLowerCase();
    const cls = s === "pass" ? "pass" : s === "fail" ? "fail" : "unknown";
    return `<span class="pill ${cls}">${s}</span>`;
  }

  async function main() {
    const res = await fetch("https://tscnlab.github.io/glc-registry/registry.json", { cache: "no-store" });
    if (!res.ok) throw new Error(`HTTP ${res.status} fetching registry.json`);
    const data = await res.json();

    const rows = data.datasets || [];
    const updated = data.generated_at_utc || "";

    const html = `
      <p class="meta">Updated: <code>${updated}</code> · Datasets: <code>${rows.length}</code></p>
      <table>
        <thead>
          <tr>
            <th>Repo</th>
            <th>Status</th>
            <th>Commit</th>
            <th>Validator</th>
            <th>Report</th>
          </tr>
        </thead>
        <tbody>
          ${rows.map(d => {
            const v = d.validation || {};
            const sha = v.commit_sha ? v.commit_sha.slice(0, 7) : "";
            const repo = d.repo || d.id || "";
            const repoUrl = d.repo ? `https://github.com/${d.repo}` : "#";
            const reportUrl = d.validation_url || "";
            return `
              <tr>
                <td><a href="${repoUrl}"><code>${repo}</code></a></td>
                <td>${pill(v.status)}</td>
                <td><code>${sha}</code></td>
                <td><code>${v.validator_version || ""}</code></td>
                <td><a href="${reportUrl}">validation.json</a></td>
              </tr>
            `;
          }).join("")}
        </tbody>
      </table>
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
</style>