# CDIF Discovery (profile module)

This repository holds the published artifacts for the **CDIF Discovery profile** — the `cdifDiscovery` building block from the [metadataBuildingBlocks](https://github.com/Cross-Domain-Interoperability-Framework/metadataBuildingBlocks) source register.

> **Scope.** `cdifDiscovery` is a profile *module*. It includes onlty the discovery-specific extensions (e.g. measurement technique, spatial/temporal coverage, and quality measurements once those are split out of the Core foundation). The full discovery *document specification* — the human-facing content requirements for making resources findable — is published in the composite [doc-corediscovery](https://github.com/Cross-Domain-Interoperability-Framework/doc-corediscovery) repository.

## Specification

- **[CDIFDiscoveryImplementationGuide.md](CDIFDiscoveryImplementationGuide.md)** — Documentation for the Discovery profile module.
- **[cdifDiscoveryStructuredSchema.json](cdifDiscoveryStructuredSchema.json)** — JSON Schema (Draft 2020-12), generated from the source register with `tools/resolve_schema.py`.
- **[discoveryRules.shacl](discoveryRules.shacl)** — Self-contained SHACL shapes, merged from every composing building block plus the profile-level shapes.

## Conformance

A conforming instance declares, in its `dcterms:conformsTo`, both:

- `https://w3id.org/cdif/discovery/1.1`

## Examples

`examples/` holds JSON-LD examples that conform to the Discovery profile. Validate one with:

```bash
python FrameAndValidate.py examples/exampleCDIFDiscovery.json --validate \
  --schema cdifDiscoveryStructuredSchema.json --frame cdifDiscovery-frame.jsonld
```

`FrameAndValidate.py` frames the document against `cdifDiscovery-frame.jsonld`, array-wraps the multi-valued properties, then validates against the JSON Schema. Validation is open-world: unknown properties pass.

## Synced from metadataBuildingBlocks

These artifacts are generated and must be re-synced when the source register changes (the sync is manual):

| file | source |
|---|---|
| `cdifDiscoveryStructuredSchema.json` | `python tools/resolve_schema.py cdifDiscovery -o cdifDiscoveryStructuredSchema.json` |
| `discoveryRules.shacl` | `python tools/validate_shacl.py cdifDiscovery --emit-shapes discoveryRules.shacl` |

Source profile: `_sources/profiles/cdifProfile/cdifDiscovery/`.

## Changelog — reviewRevision202606 (updates since branched from `main`)

This release-review branch has diverged from `main` with the following updates,
synced from the CDIF **metadataBuildingBlocks** source (see
`git log main..reviewRevision202606` for the full per-commit history):

- **Populated from metadataBuildingBlocks** — `*StructuredSchema.json`, merged SHACL,
  JSON-LD frame, examples, and the normative `FrameAndValidate.py` generated from the
  building-block source; `Examples/` renamed to `examples/`.
- **CDIF v1.1** — profile conformance URIs migrated `/1.0` → `/1.1`.
- **License** standardized on CC-BY-4.0.
- **`@id`-reference tightening** — bare `{@id}` reference slots sealed
  (`additionalProperties: false` + `required: ['@id']`); a canonical `objectReference`
  building block introduced as the strict node reference.
- **`prov:used` wrapper reconciliation** — the base `generatedBy.prov:used` accepts
  role-keyed wrappers (`schema:instrument` / `bios:computationalTool` / `prov:reagent`)
  alongside string / `{@id}` / inline `prov:Entity`; profiles pin a wrapper's shape via
  a constraint-only `if/then` (never a narrowed `anyOf`).
- **`skos:notation` → single string** at concept level (consistent with the codelist
  single-notation design).
- **`FrameAndValidate.py`** (normative, drift-checked against
  `Cross-Domain-Interoperability-Framework/validation`) — two-frame root-`@type`
  selection, context-aware `schema:about`, `--conformance` detection, `cdif:`-`@id`
  re-expansion, and (2026-08) reference-collapse on all document types + blank-node
  dedupe + agent `schema:identifier` unwrap, so `@embed:@always`-framed documents
  validate against the tightened schemas.
- **Examples** conformed to the tightened schemas throughout (PrimaryKey →
  `cdi:ComponentPosition`, reference slots → `{@id}`, CVE `hasIntendedDataType` →
  string, `skos:notation` → string, `schema:additionalType` URI → `{@id}`).


## Development branch

Active work for the 2026-06 review revision is on the `reviewRevision202606` branch. `main` reflects the prior release state. New changes should target the review branch; it is merged to main on release.


## License

This work is licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](LICENSE).
