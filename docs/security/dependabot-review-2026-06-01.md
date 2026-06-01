# Dependabot Security Review — 2026-06-01

Automated daily review of open Dependabot alerts across Censgate open-source repositories.

## Repositories Scanned

| Repository | Open Alerts | Notes |
|---|---|---|
| [censgate/redact](https://github.com/censgate/redact) | **0** | Dependabot REST API + GraphQL `vulnerabilityAlerts` |
| [censgate/openclaw-redact](https://github.com/censgate/openclaw-redact) | **0** | Dependabot REST API |
| [censgate/openclaw-redact-benchmark](https://github.com/censgate/openclaw-redact-benchmark) | **0** | Dependabot REST API |

Org-wide GraphQL scan (30 most recently updated public repos): **0** open `vulnerabilityAlerts`.

Other Censgate org repos (`redact-go`, `gate`, `docs`, `app`, etc.) return HTTP 403 — Dependabot alerts disabled or insufficient token scope; out of scope for this daily OSS pass unless enabled.

## Open Alert Triage

**No open Dependabot alerts** on the scanned repositories.

Recently fixed on `censgate/redact` (unchanged since 2026-05-30):

| Alert | GHSA / CVE | Fixed |
|---|---|---|
| [#18](https://github.com/censgate/redact/security/dependabot/18) | [GHSA-phqj-4mhp-q6mq](https://github.com/advisories/GHSA-phqj-4mhp-q6mq) (CVE-2026-45784, `openssl`) | 2026-05-30 via [#76](https://github.com/censgate/redact/pull/76) |

`Cargo.lock` confirms `openssl` **0.10.80** (patched). No new CVEs surfaced in Dependabot since last review.

## Supplementary Audits (no Dependabot alert)

### RUSTSEC-2024-0436 — `paste` 1.0.15 (unmaintained, censgate/redact)

| Field | Value |
|---|---|
| Severity | Warning (unmaintained crate, not a CVE) |
| CVSS / EPSS | N/A |
| Scope | Runtime transitive via `tokenizers` → `redact-ner` |
| Reachable | **yes** (compile-time macro crate in NER stack) |
| Risk | **Low** — maintenance status only; no known vulnerability |

```
Dependency chain:
  redact-ner → tokenizers 0.22.2 → paste 1.0.15
```

**Decision:** Accept risk until `tokenizers` upstream drops or replaces `paste`. Not actionable for a security bump PR today.

`cargo audit` (Rust 1.93.0, advisory-db 2026-06-01): **0 vulnerabilities**, 1 allowed unmaintained warning (`paste`).

### openclaw-redact — `npm audit`

`npm audit` on current `package-lock.json`: **0** vulnerabilities.

## Container Version Sync

| Source | Tag / version |
|---|---|
| GHCR `ghcr.io/censgate/redact` (full) | `0.8.3-full` / `full` (published 2026-04-19) |
| GHCR `ghcr.io/censgate/redact` (slim) | `0.8.3` / `latest` |
| Latest GitHub release | `v0.8.3` (2026-04-19) |
| Workspace `Cargo.toml` version | `0.8.3` |
| openclaw-redact default (`src/config.ts`) | `ghcr.io/censgate/redact:full` |

**Result:** No container bump PR needed — no newer GHCR image since 2026-04-19; floating `:full` matches current release.

## Existing Security / Dependency PRs

| Repo | PR | Status |
|---|---|---|
| censgate/redact | [#79](https://github.com/censgate/redact/pull/79) | Draft — 2026-05-31 review doc (superseded by this PR for 2026-06-01) |
| censgate/redact | [#72](https://github.com/censgate/redact/pull/72), [#73](https://github.com/censgate/redact/pull/73) | Draft — older review docs |

No open `dependabot/*` or `security/dependabot-*` fix branches. No remediation PRs required today.

## Summary

| Finding | Risk | Reachable | Fix PR | Status |
|---|---|---|---|---|
| (none open) | — | — | — | **Clear** |
| `paste` unmaintained | Low | Yes (transitive) | N/A | **Accept risk** (documented) |

**Open Dependabot alerts remaining:** 0 across scanned Censgate public repos.

**Remediation PRs opened today:** none (nothing actionable).
