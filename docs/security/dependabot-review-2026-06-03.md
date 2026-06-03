# Dependabot Security Review — 2026-06-03

Automated daily review of open Dependabot alerts across Censgate open-source repositories.

## Repositories Scanned

| Repository | Dependabot API | Open Alerts |
|---|---|---|
| [censgate/redact](https://github.com/censgate/redact) | Enabled | **0** |
| [censgate/openclaw-redact](https://github.com/censgate/openclaw-redact) | Enabled | **2** (same CVE; fix PR open) |
| [censgate/openclaw-redact-benchmark](https://github.com/censgate/openclaw-redact-benchmark) | Enabled | **0** |
| Other `censgate/*` repos (20) | Alerts disabled or private | N/A |

Org-level Dependabot alerts endpoint returns 404; per-repo API used.

## Supplementary: `cargo audit` (censgate/redact)

Run with Rust **1.93.0** and `cargo-audit` 0.22.1 on `main` (`123e1a9`):

- **0 vulnerabilities** in 407 locked crates
- 1 allowed warning: `paste` unmaintained (RUSTSEC-2024-0436, transitive via `tokenizers` → `redact-ner`)

## Alert Details

### CVE-2026-47429 — `vitest` &lt; 4.1.0 (censgate/openclaw-redact)

| Field | Value |
|---|---|
| Dependabot alerts | [#49](https://github.com/censgate/openclaw-redact/security/dependabot/49) (lockfile), [#50](https://github.com/censgate/openclaw-redact/security/dependabot/50) (package.json) |
| GHSA | [GHSA-5xrq-8626-4rwp](https://github.com/advisories/GHSA-5xrq-8626-4rwp) |
| Severity | Critical |
| CVSS v3 | **9.8** |
| EPSS | Not yet published (FIRST.org API empty for CVE-2026-47429) |
| Scope | **development** (direct devDependency) |
| Patched version | ≥ 4.1.0 |

**Advisory summary:** Vitest UI/API server can allow arbitrary file read (Windows path bypass) and, when exposed to the network, effectively script execution via test rerun / file write APIs.

#### Risk Prioritization

- CVSS 9.8 — above 7.0 high-priority threshold.
- EPSS unavailable; treat as high severity on paper, **lower effective risk** for this repo (see reachability).
- **Production path:** **no** — published plugin runtime depends on `uuid` and `zod` only; `vitest` is dev-only.
- **Reachable vulnerable API:** **yes in dev** — `vitest` used in `tests/*.test.ts` via `npm test` / `vitest run`; **not** in production bundle or Docker plugin image.

```
Usage path:
  package.json (devDependencies) → vitest → tests/*.test.ts
  NOT in src/ runtime or npm publish files
```

Ripgrep on `openclaw-redact` (via PR branch): vitest imports only under `tests/`; no `api.host`, `--ui`, or browser mode in CI scripts.

#### Remediation

| Action | Status |
|---|---|
| Fix PR [#34](https://github.com/censgate/openclaw-redact/pull/34) (`security/dependabot-CVE-2026-47429-2026-06-02`) | **Open** — bumps vitest to ^4.1.8 |
| CI on PR #34 | **Green** (Node 22 & 24 build jobs, 2026-06-02) |
| Duplicate PR work | **Skipped** — existing security PR covers both alerts |

**Recommended next step:** Merge PR #34 when maintainers are ready (alerts remain open until merge).

## Container Version Sync

| Source | Tag / version |
|---|---|
| GHCR `ghcr.io/censgate/redact` (latest full) | `0.8.3-full`, `full` (2026-04-19) |
| GitHub release | [v0.8.3](https://github.com/censgate/redact/releases/tag/v0.8.3) (2026-04-19) |
| openclaw-redact default (`src/config.ts`) | `ghcr.io/censgate/redact:full` (floating tag) |

**Result:** No container bump PR needed — `:full` resolves to current GHCR release; no newer image published since v0.8.3.

## Summary

| Alert | Repo | Risk (effective) | Reachable (prod) | Fix PR | CI | Status |
|---|---|---|---|---|---|---|
| CVE-2026-47429 (`vitest`) | openclaw-redact | Critical CVSS; dev-only | No (tests only) | [#34](https://github.com/censgate/openclaw-redact/pull/34) | Green | **Awaiting merge** |
| — | redact | — | — | — | — | **0 open alerts** |

**Open Dependabot alerts:** 2 (both vitest, one CVE) on openclaw-redact, covered by open PR with green CI.

**Accept-risk exceptions:** None documented today.
