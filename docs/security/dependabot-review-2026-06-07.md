# Dependabot Security Review — 2026-06-07

Automated daily review of open Dependabot alerts across Censgate open-source repositories.

## Repositories Scanned

| Repository | Dependabot Enabled | Open Alerts |
|---|---|---|
| [censgate/redact](https://github.com/censgate/redact) | Yes | **0** |
| [censgate/openclaw-redact](https://github.com/censgate/openclaw-redact) | Yes | 4 → **0** (remediated) |
| [censgate/openclaw-redact-benchmark](https://github.com/censgate/openclaw-redact-benchmark) | Yes | **0** |
| Other Censgate org repos (gate, docs, app, etc.) | No (403) | N/A |

## Alert Details

### CVE-2026-47673 – CVE-2026-47676 — `hono` → 4.12.21 (censgate/openclaw-redact)

Four medium-severity alerts (#51–#54) for the same package bump.

| CVE | GHSA | CVSS | EPSS | Summary |
|---|---|---|---|---|
| CVE-2026-47673 | [GHSA-f577-qrjj-4474](https://github.com/advisories/GHSA-f577-qrjj-4474) | 4.8 | 0.037% | JWT middleware accepts any Authorization scheme, not only Bearer |
| CVE-2026-47674 | [GHSA-xrhx-7g5j-rcj5](https://github.com/advisories/GHSA-xrhx-7g5j-rcj5) | 5.3 | 0.098% | IP Restriction bypasses static deny rules for non-canonical IPv6 |
| CVE-2026-47676 | [GHSA-2gcr-mfcq-wcc3](https://github.com/advisories/GHSA-2gcr-mfcq-wcc3) | 5.3 | 0.067% | app.mount() strips mount prefix using undecoded path |
| CVE-2026-47675 | [GHSA-3hrh-pfw6-9m5x](https://github.com/advisories/GHSA-3hrh-pfw6-9m5x) | 4.3 | 0.125% | Cookie helper does not sanitize sameSite/priority (Set-Cookie injection) |

| Field | Value |
|---|---|
| Severity | Medium |
| Scope | **development** (transitive via OpenClaw / MCP SDK) |
| Patched version | 4.12.21 |
| Manifest | `package-lock.json` |

#### Risk Prioritization

- CVSS 4.3–5.3 — below the 7.0 high-priority threshold.
- EPSS 0.037%–0.125% — all below the 0.1 (10%) threshold; low likelihood of in-the-wild exploitation.
- **Production path:** no — `hono` is a transitive **devDependency** enforced via npm overrides; published runtime deps are `uuid` + `zod` only.
- **Reachable vulnerable API:** **no** — no `hono` imports in `src/`; ripgrep for `hono`, `jwt`, `ipRestriction`, `app.mount`, `setCookie` in application code returned no matches.

```
Dependency chain:
  devDependencies → openclaw → @hono/node-server → hono 4.12.18
  overrides: hono ^4.12.18 → ^4.12.21 (fix)
```

#### Remediation

| Action | Status |
|---|---|
| Existing fix PR [#36](https://github.com/censgate/openclaw-redact/pull/36) | **Merged** 2026-06-07 (`6fb1311`) |
| Dependabot alerts #51–#54 | **Fixed** |
| CI on merge commit | All checks green (build Node 22 + 24) |

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
| CVE-2026-47673 (`hono` JWT) | Medium (CVSS 4.8) | No (dev-only transitive) | [#36](https://github.com/censgate/openclaw-redact/pull/36) merged | Green | **Fixed** |
| CVE-2026-47674 (`hono` IP restriction) | Medium (CVSS 5.3) | No (dev-only transitive) | [#36](https://github.com/censgate/openclaw-redact/pull/36) merged | Green | **Fixed** |
| CVE-2026-47676 (`hono` app.mount) | Medium (CVSS 5.3) | No (dev-only transitive) | [#36](https://github.com/censgate/openclaw-redact/pull/36) merged | Green | **Fixed** |
| CVE-2026-47675 (`hono` cookie) | Medium (CVSS 4.3) | No (dev-only transitive) | [#36](https://github.com/censgate/openclaw-redact/pull/36) merged | Green | **Fixed** |

**Open alerts remaining:** 0 across all Censgate repos with Dependabot enabled.

**Accepted-risk exceptions:** None.
