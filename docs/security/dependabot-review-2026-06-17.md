# Dependabot Security Review — 2026-06-17

Automated daily review of open Dependabot alerts across Censgate open-source repositories.

## Repositories Scanned

| Repository | Open Alerts |
|---|---|
| [censgate/redact](https://github.com/censgate/redact) | 0 |
| [censgate/openclaw-redact](https://github.com/censgate/openclaw-redact) | 0 |
| [censgate/openclaw-redact-benchmark](https://github.com/censgate/openclaw-redact-benchmark) | 0 |

## Supplemental Audits

| Repo | Tool | Result |
|---|---|---|
| censgate/redact | `cargo audit` (Rust 1.93) | 0 vulnerabilities (1 allowed unmaintained `paste`) |
| censgate/openclaw-redact | `npm audit --omit=dev` | **0** production vulnerabilities |
| censgate/openclaw-redact | `npm audit` (full) | 19 dev-only (see below) |
| censgate/openclaw-redact-benchmark | `npm audit` | 6 dev-only (fix PRs open) |

## Alert Details

No open GitHub Dependabot alerts across scanned public repos.

### Supplemental: `hono` <4.12.25 — openclaw-redact (devDependency)

| Field | Value |
|---|---|
| GHSA | [GHSA-88fw-hqm2-52qc](https://github.com/advisories/GHSA-88fw-hqm2-52qc) (+ 4 related hono advisories fixed in 4.12.25) |
| Severity | High |
| CVSS v3.1 | **7.1** |
| EPSS | Not yet published |
| Scope | devDependency (transitive via OpenClaw / MCP SDK overrides) |
| Patched version | 4.12.25 |

**Advisory summary:** CORS middleware reflects any Origin with credentials when `origin` defaults to the wildcard.

#### Risk Prioritization

- CVSS 7.1 — above the 7.0 high-priority threshold.
- EPSS unavailable; no known in-the-wild exploitation data.
- **Production path:** no — published runtime deps are `uuid` and `zod` only; `hono` is not imported in `src/`.
- **Reachable vulnerable API:** no in production; dev/test stack only.

#### Remediation

| Action | Status |
|---|---|
| Bump `hono` override `^4.12.21` → `^4.12.25` in `package.json` | Prepared locally; **push denied** for automation token on `openclaw-redact` |
| Recommended branch | `security/dependabot-GHSA-88fw-hqm2-52qc-2026-06-17` |
| Verification (local) | `npm audit`: hono cleared; 19 dev-only remain; `npm test`: 25/25 passed |

### Supplemental: protobufjs / tar / ws — openclaw-redact (devDependency)

| Advisory | Severity | Reachable | Fix PR | CI |
|---|---|---|---|---|
| GHSA-f38q-mgvj-vph7 / GHSA-wcpc-wj8m-hjx6 (`protobufjs`) | High | No (dev) | [#37](https://github.com/censgate/openclaw-redact/pull/37) | Green |
| GHSA-vmf3-w455-68vh (`tar`) | Moderate | No (dev) | [#37](https://github.com/censgate/openclaw-redact/pull/37) | Green |
| GHSA-96hv-2xvq-fx4p (`ws`) | High | No (dev) | [#37](https://github.com/censgate/openclaw-redact/pull/37) | Green |

### Supplemental: vitest / tsx — openclaw-redact-benchmark (devDependency)

| Advisory | Severity | Reachable | Fix PR | CI |
|---|---|---|---|---|
| CVE-2026-47429 (`vitest`) | Critical | No (dev test runner) | [#2](https://github.com/censgate/openclaw-redact-benchmark/pull/2) | Green |
| GHSA-gv7w-rqvm-qjhr (`tsx` / `esbuild`) | High | No (dev benchmark script) | [#3](https://github.com/censgate/openclaw-redact-benchmark/pull/3) | Green |

### Remaining dev-only findings (low action)

| Package | Severity | Path | Notes |
|---|---|---|---|
| `@changesets/*`, `js-yaml`, `read-yaml-file` | Moderate | devDependency | Release tooling only |
| `markdown-it`, `openclaw` | Moderate | devDependency | OpenClaw test stack |
| `@mariozechner/pi-coding-agent` | Low | devDependency | XSS in HTML session exports (CVSS 2.5) |

Per workflow: EPSS unavailable / <0.1 and not on production path — **accept risk** for dev-only moderate/low findings until upstream or override bumps land.

## Container Version Sync

| Source | Tag |
|---|---|
| GHCR `ghcr.io/censgate/redact` (latest full) | `0.8.3-full` / `full` (2026-04-19) |
| GHCR `ghcr.io/censgate/redact` (latest slim) | `0.8.3` / `latest` (2026-04-19) |
| Latest GitHub release | `v0.8.3` |
| openclaw-redact default (`src/config.ts`) | `ghcr.io/censgate/redact:full` |

**Result:** No container bump needed — openclaw-redact `:full` tag resolves to the current GHCR release.

## Summary

| Finding | Risk | Reachable | Fix PR | CI | Status |
|---|---|---|---|---|---|
| Dependabot alerts (all repos) | — | — | — | — | **0 open** |
| `cargo audit` (`paste` unmaintained) | Info | N/A | N/A | N/A | **Allowed** |
| `hono` GHSA-88fw-hqm2-52qc | High (CVSS 7.1) | No (dev) | Branch prepared; push blocked | — | **Action required** |
| `protobufjs` / `tar` / `ws` | High/Moderate | No (dev) | [openclaw-redact#37](https://github.com/censgate/openclaw-redact/pull/37) | Green | **Merge pending** |
| `vitest` CVE-2026-47429 | Critical | No (dev) | [benchmark#2](https://github.com/censgate/openclaw-redact-benchmark/pull/2) | Green | **Merge pending** |
| `tsx` GHSA-gv7w-rqvm-qjhr | High | No (dev) | [benchmark#3](https://github.com/censgate/openclaw-redact-benchmark/pull/3) | Green | **Merge pending** |

**Open Dependabot alerts remaining:** 0 across all Censgate public repos.

### Recommended Actions

1. **Merge** [openclaw-redact-benchmark#2](https://github.com/censgate/openclaw-redact-benchmark/pull/2) (vitest CVE-2026-47429), then [#3](https://github.com/censgate/openclaw-redact-benchmark/pull/3) (tsx/esbuild).
2. **Merge** [openclaw-redact#37](https://github.com/censgate/openclaw-redact/pull/37) (protobufjs, tar, ws overrides).
3. **Open and merge** hono `^4.12.25` bump on `security/dependabot-GHSA-88fw-hqm2-52qc-2026-06-17` (automation token lacks write access to `openclaw-redact`).
