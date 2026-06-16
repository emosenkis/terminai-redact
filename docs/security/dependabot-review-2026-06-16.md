# Dependabot Security Review — 2026-06-16

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
| `cargo audit` (Rust 1.83 / cargo-audit 0.21–0.22) | censgate/redact | **Blocked** — RustSec advisory DB entry RUSTSEC-2026-0038 uses CVSS 4.0, unsupported by current cargo-audit; no open Dependabot alerts on redact |
| `npm audit` | censgate/openclaw-redact (main) | 18 vulnerabilities (2 high, 16 moderate) → fix PR [#37](https://github.com/censgate/openclaw-redact/pull/37) reduces to 15 moderate dev-only |
| `npm audit` | censgate/openclaw-redact-benchmark (main) | 6 vulnerabilities — vitest/esbuild chain; fixes in open PRs [#2](https://github.com/censgate/openclaw-redact-benchmark/pull/2), [#3](https://github.com/censgate/openclaw-redact-benchmark/pull/3) |
| `npm audit` | openclaw-redact-benchmark PR #2 branch | 3 (1 moderate, 2 high esbuild/tsx) |
| `npm audit` | openclaw-redact-benchmark PR #3 branch | 1 moderate (js-yaml dev transitive) |
| `npm audit` | openclaw-redact PR #37 branch | 0 high/critical; 15 moderate dev-only |

## Alert Details

### GHSA-f38q-mgvj-vph7 / GHSA-wcpc-wj8m-hjx6 — `protobufjs` (censgate/openclaw-redact)

Discovered via `npm audit` on main; not surfaced as a Dependabot alert (no `dependabot.yml` on this repo).

| Field | Value |
|---|---|
| GHSA | [GHSA-f38q-mgvj-vph7](https://github.com/advisories/GHSA-f38q-mgvj-vph7), [GHSA-wcpc-wj8m-hjx6](https://github.com/advisories/GHSA-wcpc-wj8m-hjx6) |
| Severity | High |
| CVSS | **7.5** / **7.5** |
| EPSS | Not yet published |
| Scope | **development** (transitive via `@google/genai` / OpenClaw test stack) |
| Patched version | > 7.6.2 |

**Advisory summary:** Schema-derived property shadowing and unbounded `Any` expansion DoS in protobufjs JSON conversion.

#### Risk Prioritization

- CVSS 7.5 — above the 7.0 high-priority threshold.
- EPSS unavailable; transitive devDependency only.
- **Production path:** no — published package runtime is `uuid` and `zod` only.
- **Reachable vulnerable API:** **no** — protobufjs used only in dev/test agent stacks, not in `src/`.

```
Dependency chain:
  devDependencies → @google/genai / openclaw → protobufjs 7.5.8 (override)
```

#### Remediation

| Action | Status |
|---|---|
| Fix PR [#37](https://github.com/censgate/openclaw-redact/pull/37) | **Open** — bump override to `^7.6.3` |
| Local `npm test` | 25/25 passed |
| CI | Green (build Node 22 + 24) |

### GHSA-96hv-2xvq-fx4p — `ws` (censgate/openclaw-redact)

| Field | Value |
|---|---|
| GHSA | [GHSA-96hv-2xvq-fx4p](https://github.com/advisories/GHSA-96hv-2xvq-fx4p) |
| Severity | High |
| CVSS | **7.5** |
| EPSS | Not yet published |
| Scope | **development** (transitive via OpenAI/GenAI WebSocket clients) |
| Patched version | >= 8.21.0 |
| **Reachable vulnerable API:** **no** — not in published runtime |

#### Remediation

| Action | Status |
|---|---|
| Fix PR [#37](https://github.com/censgate/openclaw-redact/pull/37) | **Open** — add `ws` override `^8.21.0` |

### GHSA-vmf3-w455-68vh — `tar` (censgate/openclaw-redact)

| Field | Value |
|---|---|
| GHSA | [GHSA-vmf3-w455-68vh](https://github.com/advisories/GHSA-vmf3-w455-68vh) |
| Severity | Moderate |
| CVSS | **6.1** |
| EPSS | Not yet published |
| Scope | **development** (transitive via `openclaw`) |
| Patched version | > 7.5.15 |
| **Reachable vulnerable API:** **no** |

#### Remediation

| Action | Status |
|---|---|
| Fix PR [#37](https://github.com/censgate/openclaw-redact/pull/37) | **Open** — bump override to `^7.5.16` |

### GHSA-h67p-54hq-rp68 — `js-yaml` (censgate/openclaw-redact, openclaw-redact-benchmark)

| Field | Value |
|---|---|
| GHSA | [GHSA-h67p-54hq-rp68](https://github.com/advisories/GHSA-h67p-54hq-rp68) |
| Severity | Moderate |
| CVSS | **5.3** |
| EPSS | Not yet published (below 0.1 threshold assumed) |
| Scope | **development** (`@changesets/cli` / eslint tooling) |
| **Reachable vulnerable API:** **no** |

**Decision:** Skip — dev-only, CVSS < 7.0, EPSS < 0.1. Remains after PR #37 (15 moderate on branch).

### CVE-2026-47429 — `vitest` → 4.1.8 (censgate/openclaw-redact-benchmark)

Discovered via `npm audit` on main; not surfaced as a Dependabot alert (no `dependabot.yml` on this repo).

| Field | Value |
|---|---|
| GHSA | [GHSA-5xrq-8626-4rwp](https://github.com/advisories/GHSA-5xrq-8626-4rwp) |
| Severity | Critical |
| CVSS | **9.8** |
| EPSS | Not yet published on FIRST.org |
| Scope | **development** (devDependency) |
| Patched version | 4.1.0+ |

**Advisory summary:** When the Vitest UI server is listening, an attacker can read and execute arbitrary files.

#### Risk Prioritization

- CVSS 9.8 — above the 7.0 high-priority threshold.
- **Production path:** no — `vitest` is a devDependency.
- **Reachable vulnerable API:** **no** — configs use `vitest run` only; no `@vitest/ui` or browser mode.

#### Remediation

| Action | Status |
|---|---|
| Fix PR [#2](https://github.com/censgate/openclaw-redact-benchmark/pull/2) | **Open** — bump vitest to ^4.1.8 |
| CI on PR | All checks green (verify + verify-openclaw-e2e) |
| Merge | Blocked by base-branch protection |

### GHSA-gv7w-rqvm-qjhr — `esbuild` via `tsx` (censgate/openclaw-redact-benchmark)

| Field | Value |
|---|---|
| GHSA | [GHSA-gv7w-rqvm-qjhr](https://github.com/advisories/GHSA-gv7w-rqvm-qjhr) |
| Severity | High |
| CVSS | **8.1** |
| EPSS | Not yet published |
| Scope | **development** (devDependency) |
| Patched version | esbuild >= 0.28.1 |
| **Reachable vulnerable API:** **no** |

#### Remediation

| Action | Status |
|---|---|
| Fix PR [#3](https://github.com/censgate/openclaw-redact-benchmark/pull/3) | **Open** — bump `tsx` to ^4.22.4 |
| CI on PR | All checks green |
| `npm audit` on PR branch | 1 moderate (js-yaml) remaining |
| Merge | Blocked by base-branch protection (merge #2 first) |

### RUSTSEC-2024-0436 — `paste` unmaintained (censgate/redact)

Informational only — not a vulnerability. Transitive via `tokenizers` / `ort` dependency chain. No patched replacement available; monitor upstream.

## Container Version Sync

| Source | Tag |
|---|---|
| GHCR `ghcr.io/censgate/redact` (latest full) | `0.8.3-full` / `full` (2026-04-19) |
| GHCR `ghcr.io/censgate/redact` (latest slim) | `0.8.3` / `latest` (2026-04-19) |
| Latest GitHub release | `v0.8.3` |
| openclaw-redact default (`src/config.ts`) | `ghcr.io/censgate/redact:full` |
| openclaw-redact-benchmark (`docker-compose.*.yml`) | `ghcr.io/censgate/redact:full` |

**Result:** No container bump needed — openclaw-redact `:full` tag resolves to the current GHCR release.

## Summary

| Alert | Risk | Reachable | Fix PR | CI | Status |
|---|---|---|---|---|---|
| GHSA-f38q-mgvj-vph7 / GHSA-wcpc-wj8m-hjx6 (`protobufjs`) | High (CVSS 7.5) | No (dev transitive) | [#37](https://github.com/censgate/openclaw-redact/pull/37) open | Green | **Ready to merge** |
| GHSA-96hv-2xvq-fx4p (`ws`) | High (CVSS 7.5) | No (dev transitive) | [#37](https://github.com/censgate/openclaw-redact/pull/37) open | Green | **Ready to merge** |
| GHSA-vmf3-w455-68vh (`tar`) | Moderate (CVSS 6.1) | No (dev transitive) | [#37](https://github.com/censgate/openclaw-redact/pull/37) open | Green | **Ready to merge** |
| CVE-2026-47429 (`vitest`) | Critical (CVSS 9.8) | No (dev-only; UI not used) | [#2](https://github.com/censgate/openclaw-redact-benchmark/pull/2) open | Green | **Ready to merge** |
| GHSA-gv7w-rqvm-qjhr (`esbuild`/`tsx`) | High (CVSS 8.1) | No (dev install path) | [#3](https://github.com/censgate/openclaw-redact-benchmark/pull/3) open | Green | **Ready to merge** (merge #2 first) |
| GHSA-h67p-54hq-rp68 (`js-yaml`) | Moderate (CVSS 5.3) | No (dev transitive) | — | — | **Accepted risk** (dev-only, EPSS < 0.1) |

**Open Dependabot alerts:** 0 across all Censgate repos with Dependabot security alerts enabled.

**Accepted-risk exceptions:** `js-yaml` / `markdown-it` moderate dev-only advisories in openclaw-redact (changesets + openclaw test stack); EPSS < 0.1, not on production code path.

**Action required:**

1. Merge [openclaw-redact#37](https://github.com/censgate/openclaw-redact/pull/37) (CI green).
2. Merge [openclaw-redact-benchmark#2](https://github.com/censgate/openclaw-redact-benchmark/pull/2), then [#3](https://github.com/censgate/openclaw-redact-benchmark/pull/3).
