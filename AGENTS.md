# SUMO Specs Index

Docs/spec repo for SUMO's opinionated SUIT manifest model, feature mapping, references, and cross-implementation test vectors.

## Where to look

- `README.md` — ecosystem overview, two-level manifest model, security-version rationale.
- `specs/architecture.md` — system overview, module hierarchy, data flow.
- `specs/library-design.md` — API and design principles for implementations.
- `specs/feature-mapping.md` — gaps and feature mapping against libsum/SUIT needs.
- `refs/` — copied RFC/draft references used by the project.
- `test-vectors/` — shared fixtures for implementation validation.

## Essential commands

No component-local `mise` or build tool is present; this is a documentation/spec repo.

```bash
rg --files -g 'README*' -g 'specs/**' -g 'refs/**' -g 'test-vectors/**'
rg -n "security_version|command sequences|L1|L2|Open Questions|TODO|FIXME" README.md specs refs test-vectors
```

## Stack

- Markdown specifications and reference documents.
- JSON/YAML/test-vector data for cross-implementation checks.

## Guardrails

- Treat `specs/` as design intent for `sumo-rs`, `sumo-sovd`, and machine-manager consumers.
- Keep terminology aligned with SUIT: campaign/L1, image/L2, command sequences, security version.
- Do not update copied RFC/draft material in `refs/` unless intentionally refreshing references.

## Gotchas

- This repo is not executable by itself; validate claims against consuming code in sibling repos.
- `security_version` is a custom negative-label parameter and is not the same as sequence number.

## Missing docs/specs to watch

- No automated doc validation command is defined.
- Cross-implementation expected-output format for `test-vectors/` is not documented locally.
