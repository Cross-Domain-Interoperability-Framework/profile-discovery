# CDIF Discovery Profile — Implementation Guide

## 1. Purpose and scope

The **CDIF Discovery profile module** (`cdifDiscovery`) is the building-block module that carries discovery metadata for the Cross-Domain Interoperability Framework. Discovery metadata makes a digital resource — a dataset, document, software package, or service — findable, indexable, and cataloguable across domains.

This module is the register entry for discovery. In the current release it **composes `cdifCore`** without adding constraints of its own, and is reserved for discovery-specific extensions (measurement technique, spatial and temporal coverage, and quality measurements) as those are split out of the Core foundation.

The human-facing application profile that documents the full set of discovery content requirements — required, conditional, and recommended elements, with crosswalks to Dublin Core, schema.org, ISO 19115-1, DCAT, DDI-CDI, and FDO — is published in the composite **doc-corediscovery** repository. This guide documents the module itself; for content-requirement guidance, see that repository.

## 2. Conformance

A resource conforms to the CDIF Discovery profile when its catalog record declares conformance to both the Core and Discovery profile identifiers. The catalog record is carried on `schema:subjectOf` as a `dcat:CatalogRecord`:

```json
"schema:subjectOf": {
  "@type": ["schema:CreativeWork", "dcat:CatalogRecord"],
  "dcterms:conformsTo": [
    "https://w3id.org/cdif/core/1.0",
    "https://w3id.org/cdif/discovery/1.0"
  ]
}
```

Both identifiers are required: Discovery builds on the Core foundation, so a Discovery-conformant record is also Core-conformant.

## 3. Required and optional content

Because `cdifDiscovery` composes `cdifCore`, the required and optional properties are those of the Core profile. See the **[CDIF Core Implementation Guide](https://github.com/Cross-Domain-Interoperability-Framework/profile-core/blob/main/CDIFCoreImplementationGuide.md)** for the authoritative property-by-property documentation.

In brief, every conforming record must include:

- `@id`, `@type` (including `schema:Dataset`), and a JSON-LD `@context` with explicit prefixes;
- `schema:name` — a descriptive title;
- `schema:identifier` — the primary identifier (string or `schema:PropertyValue`);
- `schema:dateModified` — last-update date (ISO 8601);
- `schema:license` **or** `schema:conditionsOfAccess`;
- `schema:url` **or** `schema:distribution`;
- `schema:subjectOf` — the catalog record carrying the conformance declaration above.

## 4. Validation

Two validators ship with this repository:

- **JSON Schema** — `cdifDiscoveryStructuredSchema.json` (Draft 2020-12), generated from the source register.
- **SHACL** — `discoveryRules.shacl`, a self-contained shapes graph merged from every composing building block plus the profile-level shapes, usable standalone.

```bash
# JSON Schema (frames first, then validates)
python FrameAndValidate.py examples/exampleCDIFDiscovery.json --validate \
  --schema cdifDiscoveryStructuredSchema.json --frame cdifDiscovery-frame.jsonld
```

Validation is **open-world**: properties not described by the profile are allowed and do not cause failures. This lets domain extensions and source-specific properties coexist with the CDIF core.

## 5. Provenance of the artifacts

The schema and SHACL files in this repository are generated from the canonical source register, [metadataBuildingBlocks](https://github.com/Cross-Domain-Interoperability-Framework/metadataBuildingBlocks):

- `cdifDiscoveryStructuredSchema.json` ← `tools/resolve_schema.py cdifDiscovery`
- `discoveryRules.shacl` ← `tools/validate_shacl.py cdifDiscovery --emit-shapes`

Source profile directory: `_sources/profiles/cdifProfile/cdifDiscovery/`. Re-sync these artifacts whenever the source register changes.
