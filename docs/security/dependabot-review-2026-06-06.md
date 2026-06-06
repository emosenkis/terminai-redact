# Dependabot Security Review — 2026-06-06

Automated daily review of open Dependabot alerts across Censgate open-source repositories.

## Repositories Scanned

| Repository | Open Alerts | Dependabot Alerts API |
|---|---|---|
| [censgate/redact](https://github.com/censgate/redact) | 0 | Enabled |
| [censgate/openclaw-redact](https://github.com/censgate/openclaw-redact) | 4 (`hono`; vitest fixed) | Enabled |
| [censgate/openclaw-redact-benchmark](https://github.com/censgate/openclaw-redact-benchmark) | 0 | Enabled |
| 20 other public repos | — | **Disabled** (API 403) |

Repos with Dependabot alerts disabled: seemath, cloud-infrastructure, docent, platform, docs, clara-config, nats-chat-bridge, paperclip, skills, phome-ios, memoria, gate, fabrica, ideator, app, redact-go, private-ai-android, safe-chat, flutter-app, censgate-policy-redacted-chat.

## Alert Details — censgate/openclaw-redact

### CVE-2026-47429 — `vitest` < 4.1.0 (alerts #49, #50)

| Field | Value |
|---|---|
| GHSA | [GHSA-5xrq-8626-4rwp](https://github.com/advisories/GHSA-5xrq-8626-4rwp) |
| Severity | Critical |
| CVSS v3.1 | **9.8** |
| EPSS | Not yet published (FIRST.org empty) |
| Scope | development (direct devDependency) |
| Patched version | 4.1.0 |

**Advisory summary:** When the Vitest UI server is listening, an attacker can read and execute arbitrary files.

#### Risk Prioritization

- CVSS 9.8 — **high priority** (above 7.0 threshold).
- EPSS unavailable (new CVE).
- **Production path:** no — published npm package ships `uuid` + `zod` only.
- **Reachable:** yes in dev — `vitest.config.ts`, `tests/*.test.ts`; scripts use `vitest run` (no UI server in CI).

#### Remediation

| Action | Status |
|---|---|
| Fix PR [#34](https://github.com/censgate/openclaw-redact/pull/34) | **Merged** 2026-06-06 |
| Bump | vitest ^3.2.4 → ^4.1.8 |
| Dependabot alerts #49, #50 | **Fixed** |

---

### CVE-2026-47673 – CVE-2026-47676 — `hono` < 4.12.21 (alerts #51–#54)

| Field | Value |
|---|---|
| GHSA | GHSA-f577-qrjj-4474, GHSA-xrhx-7g5j-rcj5, GHSA-3hrh-pfw6-9m5x, GHSA-2gcr-mfcq-wcc3 |
| Severity | Medium |
| CVSS v3.1 | 4.3–5.3 |
| EPSS | 0.037%–0.125% (all **below 0.1%** threshold) |
| Scope | development (transitive via OpenClaw / MCP SDK overrides) |
| Patched version | 4.12.21 |

**Advisory summary:** JWT middleware accepts non-Bearer Authorization schemes; additional medium-severity auth/routing issues.

#### Risk Prioritization

- CVSS below 7.0; EPSS below 0.1%.
- **Production path:** no — `hono` not imported in `src/`; override pins transitive dev dependency only.
- **Reachable:** **no** in application code (ripgrep: no `hono` imports under `src/`).

Per triage rules: low EPSS + non-production path → lower priority, but fix is trivial via npm override.

#### Remediation

| Action | Status |
|---|---|
| Fix PR [#36](https://github.com/censgate/openclaw-redact/pull/36) | Open, CI green, mergeable (branch policy requires review) |
| Bump | hono override ^4.12.18 → ^4.12.21 |

## censgate/redact

No open Dependabot alerts. Previous openssl alert (CVE-2026-45784) remediated 2026-05-30 via PR #76.

## Container Version Sync

| Source | Tag |
|---|---|
| GHCR `ghcr.io/censgate/redact` (latest full) | `0.8.3-full` / `full` (2026-04-19) |
| GHCR `ghcr.io/censgate/redact` (latest slim) | `0.8.3` / `latest` (2026-04-19) |
| Latest GitHub release | `v0.8.3` |
| openclaw-redact default (`src/config.ts`) | `ghcr.io/censgate/redact:full` |

**Result:** No container bump needed — `:full` floating tag resolves to current GHCR release.

## Summary

| Alert | Risk | Reachable | Fix PR | CI | Status |
|---|---|---|---|---|---|
| CVE-2026-47429 (`vitest`) | Critical (CVSS 9.8) | Dev/tests only | [#34](https://github.com/censgate/openclaw-redact/pull/34) | Green | **Merged** |
| CVE-2026-47673–47676 (`hono`) | Medium (CVSS 4.3–5.3, EPSS < 0.1%) | No | [#36](https://github.com/censgate/openclaw-redact/pull/36) | Green | **PR open — awaiting review/merge** |

**Open alerts remaining:** 4 (`hono` in openclaw-redact; fix PR #36 open with green CI).

**Infrastructure note:** Dependabot vulnerability alerts are disabled on 20/23 public repos. Consider enabling org-wide for complete coverage.
