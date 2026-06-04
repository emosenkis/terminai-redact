# Dependabot Security Review — 2026-06-04

Automated daily review of open Dependabot alerts across Censgate open-source repositories.

## Repositories Scanned

| Repository | Open Alerts | Notes |
|---|---|---|
| [censgate/redact](https://github.com/censgate/redact) | **0** | `cargo audit`: no vulnerabilities (1 allowed unmaintained: `paste` via `tokenizers`) |
| [censgate/openclaw-redact](https://github.com/censgate/openclaw-redact) | **2** | Duplicate alerts (#49, #50) for same CVE; fix PR open |
| [censgate/openclaw-redact-benchmark](https://github.com/censgate/openclaw-redact-benchmark) | 0 | |
| Other org repos | N/A | Dependabot alerts API disabled or inaccessible for most private/legacy repos |

Org-level Dependabot alerts endpoint returns 404 (not enabled for `censgate` org); per-repo API used instead.

## Alert Details

### CVE-2026-47429 — `vitest` &lt; 4.1.0 (censgate/openclaw-redact)

| Field | Value |
|---|---|
| Dependabot alerts | [#49](https://github.com/censgate/openclaw-redact/security/dependabot/49), [#50](https://github.com/censgate/openclaw-redact/security/dependabot/50) |
| GHSA | [GHSA-5xrq-8626-4rwp](https://github.com/advisories/GHSA-5xrq-8626-4rwp) |
| Severity | Critical |
| CVSS v3 | **9.8** |
| EPSS | Not yet published (FIRST.org returned no score for CVE-2026-47429) |
| Scope | **development** (`devDependencies`; `vitest run` in CI) |
| Patched version | 4.1.0+ |

**Advisory summary:** Arbitrary file read / script execution when the Vitest UI or Browser Mode API is exposed to the network (especially on Windows). Mitigations in 4.1.0+ gate `allowWrite` / `allowExec` when not bound to localhost.

#### Risk Prioritization

- CVSS 9.8 — exceeds 7.0 high-priority threshold.
- EPSS unavailable; treat as elevated until published.
- **Production path:** **no** — `vitest` is a direct devDependency only (`package.json`); runtime deps are `uuid` and `zod`.
- **Reachable vulnerable API:** **no** in this deployment — CI runs `vitest run` on Linux without `--api.host` or Vitest UI; advisory impact requires Windows and/or explicitly networked Vitest UI.

```
Usage path:
  package.json devDependencies → vitest ^3.2.4 (main)
  vitest.config.ts → standard test runner (no api.host exposure)
```

#### Remediation

| Action | Status |
|---|---|
| Fix PR [#34](https://github.com/censgate/openclaw-redact/pull/34) (`security/dependabot-CVE-2026-47429-2026-06-02`) | **Open** — bumps `vitest` to ^4.1.8, CHANGELOG entry |
| CI on PR #34 | **Green** (`build (22)`, `build (24)` — 2026-06-02) |
| Duplicate work | Skipped — PR already exists |

**Recommended next step:** Merge [openclaw-redact#34](https://github.com/censgate/openclaw-redact/pull/34) to close alerts #49 and #50.

## censgate/redact — Supplemental `cargo audit`

| Finding | Risk | Reachable | Action |
|---|---|---|---|
| RUSTSEC-2024-0436 (`paste` unmaintained) | Low (unmaintained, not CVE) | Transitive via `tokenizers` → `redact-ner` | Accepted; upstream `tokenizers` dependency |

No open GitHub Dependabot alerts. No new fix PR required.

## Container Version Sync

| Source | Tag / version |
|---|---|
| GHCR `ghcr.io/censgate/redact` (latest full) | `0.8.3-full`, `full`, `0.8-full` (published 2026-04-19) |
| GHCR `ghcr.io/censgate/redact` (latest slim) | `0.8.3`, `latest`, `0.8` |
| Latest GitHub release | `v0.8.3` |
| openclaw-redact default | `ghcr.io/censgate/redact:full` (floating tag → current release) |

**Result:** No container bump PR needed — floating `:full` resolves to the current GHCR release (`0.8.3-full`).

## Summary

| Alert | Effective risk | Reachable (prod) | Fix PR | CI | Status |
|---|---|---|---|---|---|
| CVE-2026-47429 (`vitest`) | High CVSS; low practical (dev-only, CI, Linux) | No | [openclaw-redact#34](https://github.com/censgate/openclaw-redact/pull/34) | Green | **Awaiting merge** |

**Open Dependabot alerts:** 2 (both `openclaw-redact`, same CVE).

**Exceptions:** None new. Prior accepted risk: `paste` unmaintained (RustSec advisory, transitive).
