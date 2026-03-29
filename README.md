# SUMO — Software Update Manifest, Opinionated

An opinionated implementation of the [SUIT (Software Updates for IoT)](https://datatracker.ietf.org/doc/rfc9019/) standard for secure firmware updates in automotive and embedded systems.

## Ecosystem

```
Fleet Backend
     ↓ L1 campaign manifest + L2 image manifests
     ↓
sumo-sovd (campaign orchestrator)
     ↓ per-ECU via SOVD REST API
     ↓
┌─────────────┐  ┌─────────────┐  ┌─────────────┐
│   vm-mgr    │  │   SOVDd     │  │   other     │
│  (SUIT+SOVD)│  │ (UDS+SOVD)  │  │   SOVD      │
│  sumo-onboard  │  │             │  │   server    │
└─────────────┘  └─────────────┘  └─────────────┘
```

## Projects

| Project | Language | Repository | Description |
|---------|----------|------------|-------------|
| **sumo** (this) | Docs | [tr-sdv-sandbox/sumo](https://github.com/tr-sdv-sandbox/sumo) | Specifications, feature mapping, test vectors, RFCs |
| **sumo-rs** | Rust | [tr-sdv-sandbox/sumo-rs](https://github.com/tr-sdv-sandbox/sumo-rs) | SUIT library: codec, crypto, onboard, offboard, processor, CLI |
| **libsumo** | C99/C++17 | [tr-sdv-sandbox/libsumo](https://github.com/tr-sdv-sandbox/libsumo) | C/C++ implementation: C99 onboard, C++17 offboard |
| **sumo-sovd** | Rust | [sdv-playground/sumo-sovd](https://github.com/sdv-playground/sumo-sovd) | Campaign orchestrator over SOVD (multi-ECU updates) |
| **vm-mgr** | Rust | [sdv-playground/vm-mgr](https://github.com/sdv-playground/vm-mgr) | VM lifecycle manager with SUIT manifest integration |

## Architecture

### Two-Level Manifest Model

- **L1 Campaign Manifest** — orchestrates multi-ECU updates via SUIT dependencies. Command sequences declare install order, verification, and activation. Signed by fleet operator.
- **L2 Image Manifest** — per-ECU firmware with digest, encryption, security_version. Signed by firmware author.

### Security Version (Custom Parameter -257)

Separates build ordering (`sequence_number`) from anti-rollback policy (`security_version`):

```
v1.0.0 (seq=1, secver=1) ←→ v1.1.0 (seq=2, secver=1)   # A/B fleet testing
v1.2.0 (seq=3, secver=2)                                   # security-critical fix
CRL manifest (secver=2, no payload)                         # blocks < secver 2
```

This enables:
- **A/B fleet testing**: deploy different versions to different trucks, freely switch
- **CRL manifests**: raise the security floor without installing firmware
- **Re-signing**: same firmware binary, new manifest with bumped security_version
- **Content-addressable firmware**: manifests are ~500 bytes, firmware stored by SHA-256

### SUIT Command Sequences

Manifests declare what the device does via programmable command sequences:

| Sequence | Purpose | Example |
|----------|---------|---------|
| shared | Set parameters, check identity | vendor/class ID, security_version |
| dependency_resolution | Fetch L2 manifests | Campaign dependencies |
| payload_fetch | Download firmware | directive-fetch from URI |
| install | Write to storage | directive-copy, directive-write |
| validate | Verify integrity | condition-image-match |
| invoke | Boot/execute | directive-invoke |

Different manifests express different flows without code changes:
- **Firmware**: install + validate + invoke
- **CRL/policy**: shared only (no install/invoke)
- **Config update**: install + validate (no invoke/reboot)
- **Multi-ECU staged**: install all → validate all → invoke all

### Encryption

- AES-128-GCM content encryption (one key per release build)
- ECDH-ES+A128KW per-device key wrapping (CEK encrypted for each device)
- zstd compression before encryption
- Streaming decryption on device

## Specifications

Design documents and protocol choices are in [`specs/`](specs/):

- [Architecture](specs/architecture.md) — system overview, module hierarchy, data flow
- [Library Design](specs/library-design.md) — design principles, API design
- [Feature Mapping](specs/feature-mapping.md) — libsum to SUIT gap analysis (security_version, command sequences, encryption, multi-component)

## Test Vectors

Shared test vectors for cross-implementation validation are in [`test-vectors/`](test-vectors/).

## References

IETF RFCs and drafts used by SUMO are in [`refs/`](refs/):

- RFC 9019 — SUIT Architecture
- RFC 9052/9053 — COSE
- RFC 9124 — SUIT Information Model
- draft-ietf-suit-manifest-34 — SUIT Manifest Format
- draft-ietf-suit-firmware-encryption — Firmware Encryption
- draft-ietf-suit-update-management — Version Matching Extensions
- draft-ietf-suit-trust-domains — Trust Domains and Delegation
