# AGENTS.md — AI Agent Guidance for CDIF Discovery (profile module)

## Project context

This repository publishes the **CDIF Discovery profile module** (`cdifDiscovery`). It is a thin module that composes `cdifCore` and is reserved for discovery-specific extensions. The richer discovery application profile lives in the composite `doc-corediscovery` repo. Do not duplicate that content here.

## Key files

- `CDIFDiscoveryImplementationGuide.md` — module documentation
- `cdifDiscoveryStructuredSchema.json` — JSON Schema (generated)
- `discoveryRules.shacl` — merged SHACL shapes (generated)
- `cdifDiscovery-frame.jsonld` — JSON-LD frame used by `FrameAndValidate.py`
- `examples/` — validated JSON-LD examples
- `FrameAndValidate.py` — frame + JSON Schema validation

## Synced files (manual sync from metadataBuildingBlocks)

These are generated from the source register and must be re-synced when the source changes:

- `cdifDiscoveryStructuredSchema.json` ← `python tools/resolve_schema.py cdifDiscovery -o <file>`
- `discoveryRules.shacl` ← `python tools/validate_shacl.py cdifDiscovery --emit-shapes <file>`

Source profile dir: `metadataBuildingBlocks/_sources/profiles/cdifProfile/cdifDiscovery/`.

## Example conventions

1. `@context` declares explicit prefixes (`schema`, `dcterms`, `dcat`, `prov`) — never `@vocab`.
2. `schema:` prefix on all schema.org property names; namespace is `http://schema.org/` (never `https://`).
3. `@type` as arrays (e.g. `["schema:Dataset"]`).
4. `schema:subjectOf` carries a `dcat:CatalogRecord` whose `dcterms:conformsTo` includes both `https://w3id.org/cdif/core/1.1` and `https://w3id.org/cdif/discovery/1.1`.
5. Never strip unknown properties — validation is open-world.

## Validation

```bash
python FrameAndValidate.py examples/<file>.json --validate \
  --schema cdifDiscoveryStructuredSchema.json --frame cdifDiscovery-frame.jsonld
```

## Implementation Guide is self-contained

`CDIFDiscoveryImplementationGuide.md` includes its own **Common data types**
section at the end (Object reference, Defined Term). These foundational
types are documented in the Core IG, but each profile IG ships its own
copy so it stands alone — cross-repo links to `profile-core` would break
when branches diverge. When updating the Core definitions, mirror the
change here. Same convention is followed in other profile IGs (codelist,
conceptscheme, etc.) — see the `## Object Reference` / `## Defined Term`
sections in each.
