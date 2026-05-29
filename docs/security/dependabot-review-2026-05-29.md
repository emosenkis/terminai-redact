# Dependabot security review — 2026-05-29

Automated daily review for **Censgate** open-source repositories (Dependabot alerts API, EPSS, reachability grep, `cargo audit`).

## Org scan summary

| Repository | Dependabot enabled | Open alerts |
|------------|-------------------|-------------|
| censgate/redact | Yes | **1** → fix PR exists ([#76](https://github.com/censgate/redact/pull/76)) |
| censgate/openclaw-redact | Yes | **0** |
| censgate/openclaw-redact-benchmark | Yes | **0** |
| censgate/gate, docs, app, redact-go, platform, memoria, … | Disabled | — |

## censgate/redact — alert #18

| Field | Value |
|-------|-------|
| CVE | CVE-2026-45784 |
| GHSA | GHSA-phqj-4mhp-q6mq |
| Package | `openssl` 0.10.79 (rust crate, **runtime** in `Cargo.lock`) |
| Severity | Medium (GitHub); CVSS score not published on advisory |
| EPSS | **Not in FIRST EPSS dataset** (2026-05-29) — treat as unknown / likely low for new CVE |
| Patched | `openssl` **0.10.80** |

### Prioritization

- **Production path:** Yes — transitive via `ort` / `ort-sys` → `ureq` → `native-tls` → `openssl` (ONNX model fetch stack in `redact-ner` / `redact-api`).
- **EPSS:** No score; does not meet EPSS > 0.5 escalation. Medium severity, CVSS not > 7.
- **Defense in depth:** Bump still recommended; alert is open until `Cargo.lock` updates on `main`.

### Reachability (vulnerable code path)

Advisory: out-of-bounds write in `CipherCtxRef::cipher_update_inplace` for **AES-KW-PAD** ciphers only.

- **Direct use in Redact sources:** **No** — no `openssl` imports; app crypto uses `aes-gcm` / `pbkdf2` (`crates/redact-core/src/anonymizers/encrypt.rs`).
- **Transitive use:** **TLS only** — `ureq` + `native-tls` for HTTPS; does not exercise AES-KW-PAD key-wrap APIs.
- **Reachable (vulnerable primitive):** **No** (practical)
- **Path:** `redact-api` → `redact-ner` → `ort` → `ureq` → `native-tls` → `openssl`

```text
openssl v0.10.79
└── native-tls v0.2.18
    └── ureq v3.3.0
        └── ort / ort-sys → redact-ner → redact-api
```

### Remediation

- **Existing PR (skip duplicate):** https://github.com/censgate/redact/pull/76 — `openssl` 0.10.79 → 0.10.80 (`dependabot/cargo/cargo-b5bfc02d2b`)
- **CI (2026-05-29):** All required checks **SUCCESS** (Test Suite ubuntu/macos/windows, Coverage, Benchmarks, Security Audit, MSRV). CodeQL **NEUTRAL**.
- **Local `cargo audit` (Rust 1.93):** No vulnerabilities in lockfile (allowed `paste` unmaintained warning only).

### CHANGELOG (on merge of #76)

`Security: bump openssl to 0.10.80 (CVE-2026-45784)`

## censgate/openclaw-redact

- **Open alerts:** none (prior `qs` / CVE-2026-8723 fixed via merged [PR #33](https://github.com/censgate/openclaw-redact/pull/33)).
- **Container sync:** Default image `ghcr.io/censgate/redact:full` (floating). GHCR latest **`0.8.3-full`** (2026-04-19) matches GitHub release **v0.8.3**. No pinned tag bump PR needed.

## Action items

- [x] Fix PR open with green CI: [redact#76](https://github.com/censgate/redact/pull/76)
- [ ] **Merge [PR #76](https://github.com/censgate/redact/pull/76)** to close Dependabot alert #18
- [x] openclaw-redact: no open alerts; container tag strategy unchanged

## Exceptions

None. No “accept risk” waivers — medium transitive finding has an actionable bump PR awaiting merge.
