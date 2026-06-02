# CDIF Discovery Profile — Implementation Guide

# Table of contents

- [Purpose and scope](#purpose-and-scope)
- [Conformance](#conformance)
  - [Validation](#validation)
- [Provenance of the artifacts](#provenance-of-the-artifacts)
- [Dataset Properties added by Discovery Profile](#dataset-properties-added-by-discovery-profile)
  - [dqv:QualityMeasurement](#dqvqualitymeasurement)
  - [GeoCoordinates](#geocoordinates)
  - [GeoShape](#geoshape)
  - [PropertyValueSpecification](#propertyvaluespecification)
  - [sf:SimpleFeature](#sfsimplefeature)
  - [time:Proper Interval](#timeproper-interval)
  - [time:TimePosition](#timetimeposition)

# Purpose and scope

[↑ Back to TOC](#table-of-contents)

The **CDIF Discovery profile module** (`cdifDiscovery`) defines optional properties measurement technique, spatial and temporal coverage, and quality measurements to extend the core profile with properties useful for describing a subset of resources, but are not generally applicable.

# Conformance

[↑ Back to TOC](#table-of-contents)

A resource conforms to the CDIF Discovery profile when its catalog record declares conformance the Discovery profile identifier. The catalog record is carried on `schema:subjectOf` as a `dcat:CatalogRecord`:

```json
"schema:subjectOf": {
  "@type": ["schema:CreativeWork", "dcat:CatalogRecord"],
  "dcterms:conformsTo": [
    "https://w3id.org/cdif/discovery/1.1"
  ]
}
```
Other properties added in the discovery profile are optional; conformance only requires that the constraints on the properties in the JSON schema and SHACL rules are satisfied.

## Validation

[↑ Back to TOC](#table-of-contents)

Two validators ship with this repository:
- **JSON Schema** — `cdifDiscoveryStructuredSchema.json` (Draft 2020-12), generated from the source register.
- **SHACL** — `discoveryRules.shacl`, a self-contained shapes graph merged from every composing building block plus the profile-level shapes, usable standalone.

```bash
# JSON Schema (frames first, then validates)
python FrameAndValidate.py examples/exampleCDIFDiscovery.json --validate \
  --schema cdifDiscoveryStructuredSchema.json --frame cdifDiscovery-frame.jsonld
```

Validation is **open-world**: properties not described by the profile are allowed and do not cause failures. This lets domain extensions and source-specific properties coexist with the CDIF core.

# Provenance of the artifacts

[↑ Back to TOC](#table-of-contents)

The schema and SHACL files in this repository are generated from the canonical source register, [metadataBuildingBlocks](https://github.com/Cross-Domain-Interoperability-Framework/metadataBuildingBlocks):

- `cdifDiscoveryStructuredSchema.json` ← `tools/resolve_schema.py cdifDiscovery`
- `discoveryRules.shacl` ← `tools/validate_shacl.py cdifDiscovery --emit-shapes`

Artefacts for the core profile are in this [Github repository](https://github.com/Cross-Domain-Interoperability-Framework/profile-discovery/tree/reviewRevision202606) (TBD--update link to release tag)

# Dataset Properties added by Discovery Profile

[↑ Back to TOC](#table-of-contents)

## dqv:QualityMeasurement

[↑ Back to TOC](#table-of-contents)

### @type

- **Cardinality:** Required -- \'dqv:QualityMeasurement\', repeatable

### dqv:ismeasurementOf

- **Cardinality:** Required
- **Content:** string, [object reference](#object-reference), or [DefinedTerm](#sec-definedterm)

### dqv:value

- **Cardinality:** Required
- **Content:** string or [DefinedTerm](#sec-definedterm)

## GeoCoordinates

[↑ Back to TOC](#table-of-contents)

- A point location specified with latitude and longitude in decimal degrees, using the WGS84 spatial reference system.

### @type

- Required --  [\'schema:GeoCoordinates'\] (string:uri)

### latitude

- **Cardinality:** Required
- **Content:** number
- **Description:** Decimal degrees, value \>=-90 and \<= 90.

### longitude

- **Cardinality:** Required
- **Content:** number
- **Description:** east-longitude coordinate in decimal degrees. Value must be \>= -180 and \<= 180.

## GeoShape

[↑ Back to TOC](#table-of-contents)

- CDIF limits schema:GeoShape to a box or line (schema.org includes other options). Point locations are tuples of {latitude east-longitude} (y x). (documentation from [Science on Schema.org](https://github.com/ESIPFed/science-on-schema.org/blob/develop/guides/Dataset.md#spatial-coverage) see details there)

### @type

- **Cardinality:** Required -- \'GeoShape\'
- **Content:** string:uri

### box

- **Cardinality:** Required if no line
- **Content:** string
- **Description:** A rectangular (in lat-long space) extent specified by two points, the first in the lower left (southwest) corner and the second in the upper right (northeast) corner. The schema.org [GeoShape](https://schema.org/GeoShape) documentation states *Either whitespace or commas can be used to separate latitude and longitude; whitespace should be used when writing a list of several such points*.\" Since the box is a list of points, a space should be used to separate the latitude and longitude values. The two corner coordinate points are separated by a space. \'East longitude\' means positive longitude values are east of the prime (Greenwich) meridian.

### line

- **Cardinality:** Required if no box
- **Content:** string
- **Description:** a series of two or more points. Use for extents like a ship track, flight path, or foot traverse.

## PropertyValueSpecification

[↑ Back to TOC](#table-of-contents)

- Description of the kind of value expected for a variable.

### @type

- **Cardinality:** Required -- \'PropertyValueSpecification\', repeatable

### valueName

- **Cardinality:** Required
- **Content:** string
- **Description:** This will be used to match the specification to parameters in a template string used to construct a query.

### description

- **Cardinality:** Required
- **Content:** string
- **Description:** Explanation of the purpose of the parameter, its range of values, datatype, etc.

### valueRequired

- **Cardinality:** optional
- **Content:** boolean
- **Description:** Default is true. False if the specified parameter is not required to fill the template.

### valuePattern

- **Cardinality:** optional
- **Content:** string
- **Description:** regular expression to validate values for template parameters.

## sf:SimpleFeature

[↑ Back to TOC](#table-of-contents)

### @type

- **Cardinality:** Required
- **Content:** string:uri
- **Description:** Must be MUST be sf:SimpleFeature geometry type (http://www.opengis.net/ont/sf#). See https://opengeospatial.github.io/ogc-geosparql/geosparql11/sf_geometries.ttl

### geosparql:asWKT

- **Cardinality:** Required, Repeatable
- **Content:** typed string
- **Description:** geosparql specifies that a well known text (WKT) geometry object has an \@value is a string, and an \@type \"geosparql:wktLiteral\"

### geosparql:crs

- **Cardinality:** Optional
- **Content:** [object reference](#object-reference)
- **Description:** specify the coordinate reference system for the coordinate numbers in the WKT location description.

## time:Proper Interval

[↑ Back to TOC](#table-of-contents)

- Intervals can be bounded by named ordinal eras (e.g. Jurassic, Tang dynasty, Paleolithic) identified by URI, or by numeric bounds that are time coordinates in a specified reference system (implemented by the TimePosition data type). This implementation is a simplified profile based on the [W3C OWL time specification](https://www.w3.org/TR/owl-time/), using the [http://www.w3.org/2006/time#](http://www.w3.org/2006/time) namespace, which is included in the default context for this profile.

### @type

- **Cardinality:** Required -- \'time:ProperInterval\', repeatable
- **Content:** string:uri

### description

- **Cardinality:** optional
- **Content:** string
- **Description:** Text description of the time interval. If defined by an ISO8601 time interval string, put that here.

Choice:

### time:startedBy

- **Cardinality:** Optional
- **Content:** string or [DefinedTerm](#sec-definedterm)
- **Description:** identifier for a named time ordinal era that is older bound of time interval, e.g. \'isc:LowerDevonian\'

### time:finishedBy

- **Cardinality:** Optional
- **Content:** string or [DefinedTerm](#sec-definedterm)
- **Description:** identifier for a named time ordinal era that is younger bound of time interval, e.g. \'isc:LowerDevonian\'

OR:

### time:hasBeginning

- **Cardinality:** Optional
- **Content:** [time:TimePosition](#timetimeposition)
- **Description:** Temporal position for the beginning (older bound) of the interval, located by a numeric value in a temporal reference system

### time:hasEnd

- **Cardinality:** Optional
- **Content:** [time:TimePosition](#timetimeposition)
- **Description:** Temporal position for the end (younger bound) of the interval, located by a numeric value in a temporal reference system

## time:TimePosition

[↑ Back to TOC](#table-of-contents)

### @type

- **Cardinality:** Required -- \'time:TimePosition\', repeatable
- **Content:** string:uri

### time:hasTRS

- **Cardinality:** Required
- **Content:** [object reference](#object-reference)
- **Description:** identifier for a temporal reference system; default is million years before prsent as a decimal number. Default is http://www.opengis.net/def/crs/OGC/0/ChronometricGeologicTime

### time:numericPosition

- **Cardinality:** Required
- **Content:** number
- **Description:** Number that locates a temporal position in the reference frame defined by the hasTRS property.
