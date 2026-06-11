# Dependabot Security Review — 2026-06-11

Automated daily review of open Dependabot alerts across Censgate open-source repositories.

## Repositories Scanned

| Repository | Dependabot Enabled | Open Alerts |
|---|---|---|
| [censgate/redact](https://github.com/censgate/redact) | Yes (security alerts) | **0** |
| [censgate/openclaw-redact](https://github.com/censgate/openclaw-redact) | No `dependabot.yml` | **0** |
| [censgate/openclaw-redact-benchmark](https://github.com/censgate/openclaw-redact-benchmark) | No `dependabot.yml` | **0** |
| Other Censgate org repos (gate, docs, app, etc.) | No (403) | N/A |

## Supplemental Scans

| Tool | Target | Result |
|---|---|---|
| `cargo audit` (Rust 1.93) | censgate/redact | 0 vulnerabilities (1 allowed unmaintained: `paste` RUSTSEC-2024-0436) |
| `npm audit` | censgate/openclaw-redact | 0 vulnerabilities |
| `npm audit` | censgate/openclaw-redact-benchmark (main) | 2 vulnerabilities — fix in open PR [#2](https://github.com/censgate/openclaw-redact-benchmark/pull/2) |
| `npm audit` | censgate/openclaw-redact-benchmark (PR branch) | 0 vulnerabilities |

## Alert Details

### CVE-2026-47429 — `vitest` → 4.1.8 (censgate/openclaw-redact-benchmark)

Discovered via `npm audit` on main; not surfaced as a Dependabot alert (no `dependabot.yml` on this repo).

| Field | Value |
|---|---|
| GHSA | [GHSA-5xrq-8626-4rwp](https://github.com/advisories/GHSA-5xrq-8626-4rwp) |
| Severity | Critical |
| CVSS | **9.8** |
| EPSS | Not yet published (FIRST API empty) |
| Scope | **development** (devDependency) |
| Patched version | 4.1.0+ |

**Advisory summary:** When the Vitest UI server is listening, an attacker can read and execute arbitrary files.

#### Risk Prioritization

- CVSS 9.8 — above the 7.0 high-priority threshold.
- EPSS unavailable; exploit requires Vitest UI/API server exposed on a network interface.
- **Production path:** no — `vitest` is a devDependency; published runtime is `@censgate/openclaw-redact` only.
- **Reachable vulnerable API:** **no** — configs use `vitest run` only; no `@vitest/ui` or browser mode in project configs. Ripgrep for `vitest/ui`, `@vitest/ui`, `browser` in `*.ts` / `*.json` configs: no matches (lockfile optional peer deps only).

```
Dependency chain:
  devDependencies → vitest 3.2.4 (< 4.1.0)
```

#### Remediation

| Action | Status |
|---|---|
| Fix PR [#2](https://github.com/censgate/openclaw-redact-benchmark/pull/2) | **Open** — bump vitest to ^4.1.8, vitest v4 config migration |
| CI on PR | All checks green (verify + verify-openclaw-e2e) |
| Merge attempt | Blocked by base-branch protection (requires manual approval) |

### CVE-2026-45149 — `brace-expansion` (censgate/openclaw-redact-benchmark)

Moderate-severity transitive devDependency via `@typescript-eslint/typescript-estree`.

| Field | Value |
|---|---|
| GHSA | [GHSA-jxxr-4gwj-5jf2](https://github.com/advisories/GHSA-jxxr-4gwj-5jf2) |
| Severity | Medium |
| CVSS | **6.5** |
| EPSS | **0.00041** (below 0.1 threshold) |
| Scope | **development** (transitive devDependency) |
| **Reachable vulnerable API:** **no** |

Resolved in the same PR [#2](https://github.com/censgate/openclaw-redact-benchmark/pull/2) via lockfile refresh.

## Container Version Sync

| Source | Tag |
|---|---|
| GHCR `ghcr.io/censgate/redact` (latest full) | `0.8.3-full` / `full` (2026-04-19) |
| GHCR `ghcr.io/censgate/redact` (latest slim) | `0.8.3` / `latest` (2026-04-19) |
| Latest GitHub release | `v0.8.3` |
| openclaw-redact default (`src/config.ts`) | `ghcr.io/censgate/redact:full` |

**Result:** No container bump needed — openclaw-redact `:full` tag resolves to the current GHCR release.

## Summary

| Alert | Risk | Reachable | Fix PR | CI | Status |
|---|---|---|---|---|---|
| CVE-2026-47429 (`vitest`) | Critical (CVSS 9.8) | No (dev-only; UI not used) | [#2](https://github.com/censgate/openclaw-redact-benchmark/pull/2) open | Green | **Ready to merge** (branch protection) |
| CVE-2026-45149 (`brace-expansion`) | Medium (CVSS 6.5) | No (dev transitive) | [#2](https://github.com/censgate/openclaw-redact-benchmark/pull/2) open | Green | **Ready to merge** (branch protection) |

**Open Dependabot alerts:** 0 across all Censgate repos with Dependabot security alerts enabled.

**Accepted-risk exceptions:** None.

**Action required:** Approve and merge [openclaw-redact-benchmark#2](https://github.com/censgate/openclaw-redact-benchmark/pull/2) to clear remaining `npm audit` findings on main.
