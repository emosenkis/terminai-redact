# Dependabot Security Review — 2026-05-31

Automated daily review of open Dependabot alerts across Censgate open-source repositories.

## Repositories Scanned

| Repository | Open Alerts | Notes |
|---|---|---|
| [censgate/redact](https://github.com/censgate/redact) | **0** | Dependabot API + `cargo audit` |
| [censgate/openclaw-redact](https://github.com/censgate/openclaw-redact) | **0** | Dependabot API + `npm audit` |
| [censgate/openclaw-redact-benchmark](https://github.com/censgate/openclaw-redact-benchmark) | **0** | Dependabot API |

Other Censgate org repos either have Dependabot alerts disabled or are out of scope for this daily OSS pass.

## Open Alert Triage

**No open Dependabot alerts** across the scanned repositories.

Most recently fixed on `censgate/redact`:

| Alert | GHSA | Fixed |
|---|---|---|
| [#18](https://github.com/censgate/redact/security/dependabot/18) | [GHSA-phqj-4mhp-q6mq](https://github.com/advisories/GHSA-phqj-4mhp-q6mq) (`openssl` CVE-2026-45784) | 2026-05-30 via [#76](https://github.com/censgate/redact/pull/76) |

`Cargo.lock` confirms `openssl` **0.10.80** (patched).

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

**Decision:** Accept risk until `tokenizers` upstream drops or replaces `paste`. No version bump available on our side; not actionable for a security PR today.

`cargo audit` (Rust 1.93.0): **0 vulnerabilities**, 1 allowed unmaintained warning (`paste`).

### openclaw-redact — `npm audit`

`npm audit` on current `package-lock.json`: **0** vulnerabilities (info/low/moderate/high/critical all zero).

## Container Version Sync

| Source | Tag / version |
|---|---|
| GHCR `ghcr.io/censgate/redact` (full) | `0.8.3-full` / `full` (published 2026-04-19) |
| GHCR `ghcr.io/censgate/redact` (slim) | `0.8.3` / `latest` |
| Latest GitHub release | `v0.8.3` (2026-04-19) |
| openclaw-redact default (`src/config.ts`) | `ghcr.io/censgate/redact:full` |

**Result:** No container bump PR needed — floating `:full` resolves to the current GHCR release; no newer image published since last sync.

## Existing Security / Dependency PRs

| Repo | PR | Status |
|---|---|---|
| censgate/redact | [#72](https://github.com/censgate/redact/pull/72), [#73](https://github.com/censgate/redact/pull/73) | Draft review docs (2026-05-25/26); superseded by [#78](https://github.com/censgate/redact/pull/78) |

No open `security/dependabot-*` fix branches required today.

## Summary

| Finding | Risk | Reachable | Fix PR | Status |
|---|---|---|---|---|
| (none open) | — | — | — | **Clear** |
| `paste` unmaintained | Low | Yes (transitive) | N/A | **Accept risk** (documented) |

**Open Dependabot alerts remaining:** 0 across scanned Censgate public repos.

**Remediation PRs opened today:** none (nothing actionable).
