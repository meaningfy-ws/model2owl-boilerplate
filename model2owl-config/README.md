# model2owl-config — configuration template

This folder is a **template** for configuring one model2owl module. Copy it into your
module (`implementation/<your-module>/model2owl-config/`) and edit the values to match
your ontology.

> The CI pipeline reads the config of each **module** under `implementation/*/model2owl-config/`.
> This top-level copy is the canonical template to clone — it is not consumed by the build itself.

## Placeholders to replace

Values written as placeholders signal "put your own value here":

| Placeholder | Meaning |
|---|---|
| `http://example.org/ontology` | your ontology's base URI (terms become `…/ontology#YourClass`) |
| `myprefix` | the namespace prefix of your ontology's own concepts |
| `my-org/my-ontology`, `https://example.org/…` | your GitHub repo / project URLs |
| `My Ontology`, `My Organisation`, `My Model` | human-readable names shown in the artefacts/report |

Keep the empty (`""`) and `myprefix` entries in `namespaces.xml` aligned with
`base-ontology-uri` in `config-parameters.xsl`.

## The files

| File | What it configures |
|---|---|
| `config-parameters.xsl` | all transformation parameters (see groups below) |
| `metadata.json` | ontology header metadata + report/ReSpec presentation (see groups below) |
| `namespaces.xml` | prefix → namespace-URI mappings used to build and resolve term URIs |
| `imports.xml` | `owl:imports` added to the generated artefacts |
| `umlToXsdDataTypes.xml` | UML attribute type → XSD datatype mapping |
| `xsdAndRdfDataTypes.xml` | catalogue of XSD/RDF datatypes accepted in the output |

## `config-parameters.xsl` parameter groups

1. **Config-file references** — pointers to the sibling files above.
2. **Namespaces & URI construction** — base URIs, delimiter, `moduleReference`, derived artefact URIs.
3. **Scope / reused-concepts filtering** — `includedPrefixesList` (which concepts are "yours") and the `generateReusedConcepts*` toggles.
4. **Attribute → property typing** — which attribute types yield object vs datatype properties.
5. **Accepted UML stereotypes** — per element kind.
6. **Enumerations → SKOS** — whether enumeration items become `skos:Concept`/`ConceptScheme`.
7. **Tags, comments, references, status & `rdfs:isDefinedBy`** — tag keys for comments/notes/usage/status, ReSpec reference labels, status filtering, and the two `annotate…WithOntology` flags that switch `rdfs:isDefinedBy` on/off (OWL and SHACL).
8. **Output toggles & misc** — object/realisation generation, SHACL PlainLiteral handling, supported UML versions, issued date.

## `metadata.json` field groups

1. **Ontology identity & versioning** — titles/labels/descriptions, version, status, dates, preferred namespace, publisher, license.
2. **People** — `contributors`, `owners`.
3. **Links & resources** — see-also/changelog/feedback/repository links, `dependencies`, `localBiblio`, `projectLocalResources`.
4. **Convention report** — the `conventionReport*` fields shown on the convention report.
5. **Doc / ReSpec presentation** — `documentConfig`, `navigation`, `openGithubIssue`, `logo`.

All metadata fields are optional: a field left blank or removed is simply omitted from
the output (it does not fail the build).
