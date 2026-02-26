---
layout: default
title: GLC Registry
permalink: /registry/
---

# GLC Registry

<p class="small">
  Source:
  <a href="https://tscnlab.github.io/glc-registry/registry.json"><code>registry.json</code></a>
</p>

<div id="glc-registry">Loading…</div>

<script>
(async function () {
  const target = document.getElementById("glc-registry");

  function pill(status) {
    const s = (status || "unknown").toLowerCase();
    const cls =
      s === "pass" ? "pass" :
      s === "fail" ? "fail" : "unknown";
    return `<span class="pill ${cls}">${s}</span>`;
  }

  function fmtUtc(ts) {
    if (!ts) return "";
    // ts is already ISO with timezone; just make it readable
    // Example: 2026-02-26T18:12:33.055905+00:00 -> 2026-02-26 18:12 UTC
    const m = ts.match(/^(\d{4}-\d{2}-\d{2})T(\d{2}:\d{2})/);
    return m ? `${m[1]} ${m[2]} UTC` : ts;
  }

  try {
    const res = await fetch("https://tscnlab.github.io/glc-registry/registry.json", { cache: "no-store" });
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const data = await res.json();

    const rows = data.datasets || [];
    const updated = fmtUtc(data.generated_at_utc);

    const html = `
      <p class="small text-muted">
        Registry updated: <code>${updated}</code> · Datasets: <code>${rows.length}</code>
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
            const repoUrl = repo ? `https://github.com/${repo}` : "#";
            const sha = v.commit_sha ? v.commit_sha.slice(0, 7) : "";
            const commitUrl = (repo && v.commit_sha) ? `${repoUrl}/commit/${v.commit_sha}` : "#";
            const reportUrl = d.validation_url || "";
            const status = v.status || "unknown";
            const validatedAt = fmtUtc(v.timestamp_utc);

            return `
              <tr>
                <td><a href="${repoUrl}" target="_blank" rel="noopener"><code>${repo}</code></a></td>
                <td>${pill(status)}</td>
                <td>${sha ? `<a href="${commitUrl}" target="_blank" rel="noopener"><code>${sha}</code></a>` : ""}</td>
                <td><code>${v.validator_version || ""}</code></td>
                <td><code>${validatedAt}</code></td>
                <td>${reportUrl ? `<a href="${reportUrl}" target="_blank" rel="noopener">validation.json</a>` : ""}</td>
              </tr>
            `;
          }).join("")}
        </tbody>
      </table>

      <p class="small text-muted">
        Missing a dataset? Add it to <code>datasets.yml</code> in the registry repo.
      </p>
    `;

    target.innerHTML = html;
  } catch (err) {
    target.innerHTML = `<p style="color:#b42318;"><b>Failed to load registry</b><br/><code>${String(err)}</code></p>`;
  }
})();
</script>

<style>
/* Match your existing “pill” concept but keep it subtle and consistent */
.pill { display: inline-block; padding: 2px 10px; border-radius: 999px; font-weight: 600; font-size: 12px; }
.pass { background: rgba(25,135,84,0.12); color: rgb(25,135,84); }
.fail { background: rgba(220,53,69,0.12); color: rgb(220,53,69); }
.unknown { background: rgba(108,117,125,0.12); color: rgb(108,117,125); }
</style>