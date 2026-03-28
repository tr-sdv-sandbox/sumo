# SUMO Shared Test Vectors

Machine-readable test vectors for validating both libsumo (C/C++) and sumo-rs (Rust) implementations against the same expected behavior.

## Format

Test vectors are CBOR-encoded files with companion JSON metadata describing:
- Input parameters (keys, firmware, manifest fields)
- Expected outputs (envelope bytes, ciphertext, digests)
- Expected error conditions

## Status

Test vectors are planned but not yet generated. Both implementations currently use their own test fixtures.
