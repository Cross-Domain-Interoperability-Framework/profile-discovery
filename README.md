# CDIF Discovery (profile module)

This repository holds the published artifacts for the **CDIF Discovery profile module** — the `cdifDiscovery` building block from the [metadataBuildingBlocks](https://github.com/Cross-Domain-Interoperability-Framework/metadataBuildingBlocks) source register.

> **Scope.** `cdifDiscovery` is a profile *module*. It currently composes `cdifCore` and is reserved for discovery-specific extensions (e.g. measurement technique, spatial/temporal coverage, and quality measurements once those are split out of the Core foundation). The full discovery *application profile* — the human-facing content requirements for making resources findable — is published in the composite [doc-corediscovery](https://github.com/Cross-Domain-Interoperability-Framework/doc-corediscovery) repository.

## Specification

- **[CDIFDiscoveryImplementationGuide.md](CDIFDiscoveryImplementationGuide.md)** — Documentation for the Discovery profile module.
- **[cdifDiscoveryStructuredSchema.json](cdifDiscoveryStructuredSchema.json)** — JSON Schema (Draft 2020-12), generated from the source register with `tools/resolve_schema.py`.
- **[discoveryRules.shacl](discoveryRules.shacl)** — Self-contained SHACL shapes, merged from every composing building block plus the profile-level shapes.

## Conformance

A conforming instance declares, in its `dcterms:conformsTo`, both:

- `https://w3id.org/cdif/core/1.0`
- `https://w3id.org/cdif/discovery/1.0`

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

## License

This work is dedicated to the public domain under [CC0 1.0 Universal](LICENSE).
