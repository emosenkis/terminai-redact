# Dependabot security review — 2026-05-25

Automated daily review for **Censgate** open-source repositories (Dependabot alerts API + reachability grep).

## Org scan summary

| Repository | Dependabot enabled | Open alerts |
|------------|-------------------|-------------|
| censgate/redact | Yes | **0** |
| censgate/openclaw-redact | Yes | **1** → fix PR opened |
| censgate/gate, docs, app, redact-go, platform, … | No / 403 | — |

## censgate/redact

- **Open alerts:** none (15 historically fixed, including recent `openssl` / `rand` work on `main`).
- **cargo audit:** not run in CI agent image (`cargo-audit` not installed); Dependabot is the authoritative source for this repo.
- **Container (`ghcr.io/censgate/redact`):** latest tagged release **v0.8.3** / `0.8.3-full` (2026-04-19). Matches GitHub release. No pinned image bump required in-repo.

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

Not high-risk by policy (EPSS < 0.1, CVSS < 7, dev-only path). Remediated anyway to clear the alert and keep lockfile hygenic.

### Reachability

- **Reachable in production plugin code:** **No**
- **Path:** `openclaw` (dev) → `express` → `body-parser` → `qs`
- **Published runtime deps:** `uuid`, `zod` only (`package.json` `dependencies`)
- **Source grep:** no `qs` / `require('qs')` in `src/`

### Remediation

- **PR:** https://github.com/censgate/openclaw-redact/pull/33 (branch `security-dependabot-cve-2026-8723-2026-05-25`)
- Override `"qs": "^6.15.2"` + lockfile refresh
- Local tests: `npm test` — 25 passed

### Container sync

Default Docker image remains floating tag `ghcr.io/censgate/redact:full` (tracks latest `full` release). Registry latest: **0.8.3-full**. No separate pin to bump.

## Action items

- [x] CI green on openclaw-redact [PR #33](https://github.com/censgate/openclaw-redact/pull/33) (Node 22 + 24, 2026-05-25)
- [ ] Merge PR #33 when ready (closes Dependabot alert #47)

## Exceptions

None today. All open alerts have a fix PR or are already resolved in `redact`.
