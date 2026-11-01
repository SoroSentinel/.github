<div align="center">

![SoroSentinel Banner](https://raw.githubusercontent.com/SoroSentinel/Sorosentinel-intel/main/media/sorosentinel-intel.jpg)

# SoroSentinel

**Defensive security tooling for the Stellar ecosystem.**

[![Documentation](https://img.shields.io/badge/Docs-GitHub_Pages-blue.svg)](https://sorosentinel.github.io/Sorosentinel-intel/)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Stellar](https://img.shields.io/badge/Stellar-Soroban-black)](https://stellar.org)
[![Intel](https://img.shields.io/badge/Intel-Go_1.27-00ADD8?logo=go)](https://github.com/SoroSentinel/Sorosentinel-intel)
[![Guard](https://img.shields.io/badge/Guard-TypeScript_Strict-3178C6?logo=typescript)](https://github.com/SoroSentinel/Sorosentinel-guard)
[![Scan](https://img.shields.io/badge/Scan-Rust_1.98-DEA584?logo=rust)](https://github.com/SoroSentinel/Sorosentinel-scan)

</div>

---

SoroSentinel provides open-source, pre-signing transaction effect simulation and decentralized threat intelligence for the Stellar and Soroban network. 

The product **advises**: it never controls the network, never touches private keys, never claims an entity is "safe", and fails loudly (`partial` or `unanalyzable`) when an operation or variant cannot be analyzed.

---

## The Three Components & Data Pipeline

```
[ Soroban Contract WASM ]
           │
           ▼
[ sorosentinel-scan ] (Rust)
           │  ScanReport JSON
           ▼
[ sorosentinel-intel ] (Go) ◀── Stellar RPC Ledgers & SEP-1 Domains
           │  Ed25519 Signed Snapshot & Read API
           ▼
[ sorosentinel-guard ] (TypeScript SDK)
           ▲
           │  Raw XDR Envelope (Pre-Signing)
   [ Stellar Wallet / User ]
```

| Component | Language | Role | Repository |
|---|---|---|---|
| **[Guard](https://github.com/SoroSentinel/Sorosentinel-guard)** | TypeScript | Pre-signing SDK for wallets. Intercepts transaction envelope XDR, executes Soroban RPC simulation, derives worst-case net balance deltas per affected account, and verifies snapshots offline. | [`SoroSentinel/Sorosentinel-guard`](https://github.com/SoroSentinel/Sorosentinel-guard) |
| **[Intel](https://github.com/SoroSentinel/Sorosentinel-intel)** | Go | Ingestion backbone and threat intelligence engine. Reads ledgers directly via Stellar RPC, runs rule-based detectors, stores write-once evidence, and publishes Ed25519-signed snapshots and a read API. | [`SoroSentinel/Sorosentinel-intel`](https://github.com/SoroSentinel/Sorosentinel-intel) |
| **[Scan](https://github.com/SoroSentinel/Sorosentinel-scan)** | Rust | Static analysis library and CLI. Inspects deployed Soroban contract WASM bytecode in a zero-network sandbox to validate SEP-41 token interfaces, resource limits, and environment metadata without executing code. | [`SoroSentinel/Sorosentinel-scan`](https://github.com/SoroSentinel/Sorosentinel-scan) |

Data flows strictly in one direction: **Scan → evidence → Intel → signed snapshot → Guard.** Repositories communicate exclusively through versioned schemas owned by Intel.

---

## Watch It Work

[![SoroSentinel Demo Flow](https://raw.githubusercontent.com/SoroSentinel/Sorosentinel-intel/main/media/demo-flow.jpg)](https://github.com/SoroSentinel/Sorosentinel-intel/blob/main/media/demo-flow.mp4)

*An 88-second end-to-end recording on Stellar testnet: look-alike asset issued → evidence stored → `caution` signal raised → Ed25519 snapshot published → Guard flags `findings_present` with `G-TRUST-FLAGGED`.*

---

## Core Invariants

1. **Never output "safe":** Allowed Guard statuses are `findings_present`, `no_issues_found`, `partial`, and `unanalyzable`. Absence of data is never reassurance.
2. **Fail loud, never fail clean:** Timeouts, unhandled operations, or unknown networks return `partial` or `unanalyzable`.
3. **No status without stored evidence:** Signals derive from reproducible rule tables over `(evidence, as_of, ruleset_version)` — never arbitrary numeric scores.
4. **Guard never touches private keys:** Operates strictly on serialized transaction envelopes before signing, without telemetry or privacy leakage by default.
5. **Money is integers:** All currency amounts are strictly 7-decimal fixed-point integers (stroops). Never floats.
6. **Public text via reviewed templates:** Public wording comes exclusively from an approved template registry rendered with verifiable observations.

---

## Quick Links

- 📖 **Documentation Site:** [https://sorosentinel.github.io/Sorosentinel-intel/](https://sorosentinel.github.io/Sorosentinel-intel/)
- 🔍 **Guard Findings Reference:** [What Guard reports](https://sorosentinel.github.io/Sorosentinel-intel/reference/guard-findings.html)
- ⚙️ **Scan Indicators Reference:** [What Scan reports](https://sorosentinel.github.io/Sorosentinel-intel/reference/scan-indicators.html)
- 📊 **Intel Signals Reference:** [What Intel publishes](https://sorosentinel.github.io/Sorosentinel-intel/reference/intel-signals.html)
- 🔐 **Snapshot Verification Guide:** [Verifying snapshots offline](https://sorosentinel.github.io/Sorosentinel-intel/verifying-snapshots.html)
- ⏱️ **Live Status Matrix:** [Protocol 30 schedule & component readiness](https://sorosentinel.github.io/Sorosentinel-intel/status.html)

---

## Maintainer & Security

- **Maintainer:** Allison ([@Teescom](https://github.com/Teescom))
- **Security Inquiries:** Coordinated vulnerability disclosure via the [Security tab](https://github.com/SoroSentinel/Sorosentinel-intel/security/advisories/new) or by email at `adebisiallison2018@gmail.com`.
