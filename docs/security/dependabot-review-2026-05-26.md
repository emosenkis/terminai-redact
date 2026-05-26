# Dependabot security review — 2026-05-26

Automated daily review for **Censgate** open-source repositories (Dependabot alerts API + reachability grep + `cargo audit`).

## Org scan summary

| Repository | Dependabot enabled | Open alerts |
|------------|-------------------|-------------|
| censgate/redact | Yes | **0** |
| censgate/openclaw-redact | Yes | **1** → fix PR open |
| censgate/openclaw-redact-benchmark | Yes | **0** |
| censgate/gate, docs, app, redact-go, platform, … | No / private | — |

## censgate/redact

- **Open Dependabot alerts:** none.
- **cargo audit (Rust 1.93.0):** no vulnerabilities. One allowed warning: `paste` 1.0.15 unmaintained (RUSTSEC-2024-0436) via `tokenizers` → `redact-ner` — upstream transitive, no CVE, no patch available.
- **Container (`ghcr.io/censgate/redact`):** latest tagged release **v0.8.3** / `0.8.3-full` (2026-04-19). Matches GitHub release. No newer image published since last review.

## censgate/openclaw-redact — alert #47

| Field | Value |
|-------|-------|
| CVE | CVE-2026-8723 |
| Package | `qs` (npm, transitive) |
| Severity | Medium (CVSS 5.3) |
| EPSS | 0.00044 (0.04%) — **below 0.1 skip threshold** |
| Scope | `development` |
| Patched | 6.15.2 |

### Prioritization

Not high-risk by policy (EPSS < 0.1, CVSS < 7, dev-only path). Existing fix PR clears the alert when merged.

### Reachability

- **Reachable in production plugin code:** **No**
- **Path:** `openclaw` (dev) → `express` → `body-parser` → `qs`
- **Published runtime deps:** `uuid`, `zod` only (`package.json` `dependencies`)
- **Source grep:** no `qs` / `require('qs')` in `src/`; lockfile pins `qs@6.15.1` (transitive only)

### Remediation

- **PR:** https://github.com/censgate/openclaw-redact/pull/33 (branch `security-dependabot-cve-2026-8723-2026-05-25`)
- Override `"qs": "^6.15.2"` + lockfile refresh
- **CI:** green (Node 22 + 24, verified 2026-05-26)
- **Status:** open, mergeable — no duplicate PR created

### Container sync

Default Docker image remains floating tag `ghcr.io/censgate/redact:full` (tracks latest `full` release). Registry latest: **0.8.3-full**. No separate pin to bump.

## censgate/openclaw-redact-benchmark

- **Open Dependabot alerts:** none.

## Action items

- [x] Re-verify CI green on openclaw-redact [PR #33](https://github.com/censgate/openclaw-redact/pull/33) (2026-05-26)
- [ ] Merge PR #33 when ready (closes Dependabot alert #47)

## Exceptions

None today. All open alerts have an open fix PR with green CI or are already resolved in `redact`.
