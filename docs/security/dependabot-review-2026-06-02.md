# Dependabot Security Review — 2026-06-02

Automated daily review of open Dependabot alerts across Censgate open-source repositories.

## Repositories Scanned

| Repository | Open Alerts | Notes |
|---|---|---|
| [censgate/redact](https://github.com/censgate/redact) | **0** | `cargo audit` (Rust 1.93): no vulnerabilities |
| [censgate/openclaw-redact](https://github.com/censgate/openclaw-redact) | **2** → fix PR open | Same CVE on `package.json` + lockfile manifests |
| Other org repos (22) | N/A | Dependabot alerts disabled or API 403 |

Supplemental scan: `cargo audit` on `censgate/redact` (allowed warning: unmaintained `paste` via `tokenizers`).

## Alert Details

### CVE-2026-47429 — `vitest` < 4.1.0 (censgate/openclaw-redact)

| Field | Value |
|---|---|
| GHSA | [GHSA-5xrq-8626-4rwp](https://github.com/advisories/GHSA-5xrq-8626-4rwp) |
| Dependabot | [#49](https://github.com/censgate/openclaw-redact/security/dependabot/49), [#50](https://github.com/censgate/openclaw-redact/security/dependabot/50) |
| Severity | Critical |
| CVSS v3 | **9.8** |
| EPSS | Not yet published (FIRST.org empty for CVE-2026-47429) |
| Scope | `development` — direct devDependency |
| Patched version | 4.1.0 |

**Advisory summary:** Arbitrary file read / script execution when Vitest UI or Browser Mode API is exposed (especially on Windows or with `--api.host` bound beyond localhost).

#### Risk Prioritization

- CVSS 9.8 exceeds the 7.0 high-priority threshold.
- EPSS unavailable (new advisory); practical exploit path requires exposing Vitest UI/API — **not** how this repo runs tests (`vitest run` only).
- **Production path:** no — published package runtime is `uuid` + `zod` only.
- **Reachable in repo:** yes — `tests/*.test.ts` import `vitest`; path is dev/CI only.

```
Usage:
  package.json → devDependencies.vitest ^3.2.4
  tests/*.test.ts → import from "vitest"
  scripts: "test": "vitest run" (no UI server)
```

#### Remediation

| Action | Status |
|---|---|
| Fix PR [#34](https://github.com/censgate/openclaw-redact/pull/34) | Open — bump `vitest` to `^4.1.8`, lockfile refresh |
| Local verification | `npm test` 25 passed; `npm audit` 0 vulnerabilities |
| CI on PR #34 | **Green** (Node 22 + 24 build jobs) |

Alerts will close when PR #34 merges.

## censgate/redact — cargo audit

| Finding | Severity | Reachable | Action |
|---|---|---|---|
| RUSTSEC-2024-0436 (`paste` unmaintained) | Warning (allowed) | Transitive via `tokenizers` → `redact-ner` | No bump required; upstream dependency |

No open GitHub Dependabot alerts. No fix PR needed.

## Container Version Sync

| Source | Tag |
|---|---|
| Latest GitHub release | `v0.8.3` (2026-04-19) |
| openclaw-redact default (`src/config.ts`) | `ghcr.io/censgate/redact:full` (floating) |

**Result:** No container bump PR — plugin uses the floating `:full` tag; no pinned digest/version lag behind a newer release.

## Summary

| Alert | Risk | Reachable | Fix PR | CI | Status |
|---|---|---|---|---|---|
| CVE-2026-47429 (`vitest`) | Critical (CVSS 9.8), dev-only | Yes (tests); not production | [#34](https://github.com/censgate/openclaw-redact/pull/34) | Green | **Awaiting merge** |

**Open Dependabot alerts (API):** 2 on `openclaw-redact` until [#34](https://github.com/censgate/openclaw-redact/pull/34) merges; 0 on `redact`.
