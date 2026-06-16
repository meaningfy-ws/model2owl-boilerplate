# Changelog

All notable changes to this boilerplate are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added
- ReSpec documentation generation (`respec_resources/` inputs + CI generation).
- RDF diff-reports generation (`.github/workflows/diff-combined.yml`, `diff-config.env`).
- AsciiDoc glossary generation alongside the HTML glossary.
- JSON-LD context generation per module.
- Per-module ontology import directives (`model2owl-config/imports.xml`).
- Externalised ontology metadata (`model2owl-config/metadata.json`), replacing the
  inline metadata variables previously held in `config-parameters.xsl`.

### Changed
- Adopted the upstream `transform_with_model2owl.yml` workflow (module auto-detection,
  suffix-agnostic XMI handling, GitHub Pages publishing); the model2owl engine is cloned
  from `meaningfy-ws/model2owl@develop`.
- Refreshed `model2owl-config` (`config-parameters.xsl`, `namespaces.xml`) with the new
  feature toggles while preserving project-specific choices
  (`enableGenerationOfConceptSchemes`, the notice module's `moduleReference`).
- Dropped the `_CM` suffix from UML model export file names to align with the upstream
  naming convention.
