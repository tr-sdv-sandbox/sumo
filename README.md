# SUMO — Software Update Manifest, Opinionated

An opinionated implementation of the [SUIT (Software Updates for IoT)](https://datatracker.ietf.org/doc/rfc9019/) standard for secure firmware updates on constrained devices.

## Projects

| Project | Language | Description |
|---------|----------|-------------|
| [libsumo](../libsumo/) | C99 / C++17 | C/C++ implementation — C99 onboard (device), C++17 offboard (server) |
| [sumo-rs](../sumo-rs/) | Rust | Rust implementation — `no_std` onboard, full-featured offboard + CLI |

## Architecture

SUMO implements a **two-level manifest architecture** on top of SUIT:

- **L1 Campaign Manifest** — orchestrates multi-ECU updates, references L2 image manifests via SUIT dependencies
- **L2 Image Manifest** — describes a single firmware image with encryption, compression, and integrity verification

Both implementations provide:
- SUIT manifest validation with COSE_Sign1 / COSE_Mac0
- Streaming AES-128-GCM decryption with tail-buffering
- ECDH-ES+A128KW and A128KW key wrapping
- zstd compression / decompression
- Anti-rollback and key revocation enforcement
- Platform abstraction for ESP32, Zephyr, and Linux

## Specifications

Design documents and protocol choices are in [`specs/`](specs/):

- [Architecture](specs/architecture.md) — system overview, module hierarchy, data flow
- [Library Design](specs/library-design.md) — design principles, API design, migration from libsum
- [Feature Mapping](specs/feature-mapping.md) — libsum to SUIT feature gap analysis

## Test Vectors

Shared test vectors for cross-implementation validation are in [`test-vectors/`](test-vectors/).

## References

IETF RFCs and drafts used by SUMO are in [`refs/`](refs/).
