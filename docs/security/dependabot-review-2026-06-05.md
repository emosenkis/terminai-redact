# Dependabot Security Review — 2026-06-05

Automated daily review of open Dependabot alerts across Censgate open-source repositories.

## Repositories Scanned

| Repository | Open Alerts | Notes |
|---|---|---|
| [censgate/redact](https://github.com/censgate/redact) | **0** | `cargo audit` clean (1 allowed unmaintained `paste` warning) |
| [censgate/openclaw-redact](https://github.com/censgate/openclaw-redact) | **6** (4 unique) | vitest + hono transitive devDependencies |
| [censgate/openclaw-redact-benchmark](https://github.com/censgate/openclaw-redact-benchmark) | **0** | — |
| Other org repos | N/A | Dependabot alerts API disabled or inaccessible |

## Alert Details

### CVE-2026-47429 — `vitest` < 4.1.0 (censgate/openclaw-redact)

| Field | Value |
|---|---|
| GHSA | [GHSA-5xrq-8626-4rwp](https://github.com/advisories/GHSA-5xrq-8626-4rwp) |
| Severity | Critical |
| CVSS | **9.8** |
| EPSS | Not yet published |
| Scope | development |
| Patched version | 4.1.0 |

**Advisory summary:** When the Vitest UI/API server is listening, arbitrary files can be read and executed.

#### Risk Prioritization

- CVSS 9.8 — **high priority** by severity.
- **Production path:** no — `vitest` is a devDependency; published runtime is `uuid` and `zod` only.
- **Reachable:** yes in `tests/*.test.ts` and `vitest.config.ts`; repo uses `vitest run` only (no UI server in CI or published artifacts).
- **Exploitability:** requires exposing the Vitest UI server; not enabled in this project's test scripts.

#### Remediation

| Action | Status |
|---|---|
| Fix PR [#34](https://github.com/censgate/openclaw-redact/pull/34) (`security/dependabot-CVE-2026-47429-2026-06-02`) | **Open** — CI green |
| Dependabot alerts [#49](https://github.com/censgate/openclaw-redact/security/dependabot/49), [#50](https://github.com/censgate/openclaw-redact/security/dependabot/50) | Open until PR merges |

---

### CVE-2026-47673 / CVE-2026-47674 / CVE-2026-47675 / CVE-2026-47676 — `hono` < 4.12.21 (censgate/openclaw-redact)

| CVE | GHSA | CVSS | EPSS |
|---|---|---|---|
| CVE-2026-47673 | [GHSA-f577-qrjj-4474](https://github.com/advisories/GHSA-f577-qrjj-4474) | 4.8 | 0.037% |
| CVE-2026-47674 | [GHSA-xrhx-7g5j-rcj5](https://github.com/advisories/GHSA-xrhx-7g5j-rcj5) | 5.3 | 0.098% |
| CVE-2026-47675 | [GHSA-3hrh-pfw6-9m5x](https://github.com/advisories/GHSA-3hrh-pfw6-9m5x) | 4.3 | 0.125% |
| CVE-2026-47676 | [GHSA-2gcr-mfcq-wcc3](https://github.com/advisories/GHSA-2gcr-mfcq-wcc3) | 5.3 | 0.067% |

| Field | Value |
|---|---|
| Severity | Medium |
| Scope | development |
| Patched version | 4.12.21 |
| Current override | `^4.12.18` (lockfile resolves to 4.12.18) |

**Advisory summary:** JWT middleware auth-scheme bypass, IPv6 IP restriction bypass, Set-Cookie injection via cookie helper, and `app.mount()` percent-encoding routing issues.

#### Risk Prioritization

- All CVSS scores **below 7.0**; all EPSS scores **below 0.1** (0.1% exploitation likelihood threshold).
- **Production path:** no — transitive devDependency via `openclaw` / `@google/genai` → `@modelcontextprotocol/sdk` → `hono`.
- **Reachable in plugin source:** **no** — ripgrep finds no `hono` imports under `src/`; only npm override pins the transitive version.

```
Dependency chain:
  devDependency openclaw / @google/genai → @modelcontextprotocol/sdk → hono 4.12.18
```

#### Remediation

| Action | Status |
|---|---|
| Fix PR [#35](https://github.com/censgate/openclaw-redact/pull/35) (`security/dependabot-CVE-2026-47675-2026-06-05`) | **Open** — bump `hono` override to `^4.12.21` |
| Dependabot alerts [#51](https://github.com/censgate/openclaw-redact/security/dependabot/51)–[#54](https://github.com/censgate/openclaw-redact/security/dependabot/54) | Open until PR merges |

## censgate/redact — cargo audit

`cargo audit` (Rust 1.93.0, cargo-audit 0.22.1): **no vulnerabilities**.

| Advisory | Severity | Reachable | Action |
|---|---|---|---|
| RUSTSEC-2024-0436 (`paste` unmaintained) | Warning | Transitive via `tokenizers` | Allowed; no security impact |

GitHub Dependabot alerts: **0 open**.

## Container Version Sync

| Source | Tag / Version |
|---|---|
| GHCR `ghcr.io/censgate/redact` (full) | `0.8.3-full` / `full` (2026-04-19) |
| GHCR `ghcr.io/censgate/redact` (slim) | `0.8.3` / `latest` (2026-04-19) |
| Latest GitHub release | `v0.8.3` |
| openclaw-redact default (`src/config.ts`) | `ghcr.io/censgate/redact:full` (floating tag) |

**Result:** No container bump needed — `:full` resolves to the current GHCR release; no newer image published since v0.8.3.

## Summary

| Alert | Risk | Reachable (prod) | Fix PR | CI | Status |
|---|---|---|---|---|---|
| CVE-2026-47429 (`vitest`) | Critical (CVSS 9.8) | No (dev/test only) | [#34](https://github.com/censgate/openclaw-redact/pull/34) | Green | **Fix PR open** |
| CVE-2026-47673–47676 (`hono`) | Medium (CVSS 4.3–5.3, EPSS < 0.1%) | No (transitive devDep) | [#35](https://github.com/censgate/openclaw-redact/pull/35) | Pending | **Fix PR open** |

**High-risk reachable alerts:** vitest has an open fix PR with green CI (dev-only exposure).

**Open Dependabot alerts remaining:** 6 on openclaw-redact until PRs #34 and #35 merge; 0 on censgate/redact.
