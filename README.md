# ariadne-example-sbom

Minimal SBOM case study for **Ariadne**.

This example shows how Ariadne turns a CycloneDX SBOM into RDF.

## Contents

| Path                          | Description                                |
| ----------------------------- | ------------------------------------------ |
| `datasources/`                | CycloneDX JSON input data.                 |
| `knowledgegraph/`             | Generated RDF graph.                       |
| `mappings/`                   | YARRRML mapping.                           |
| `ontologies/`                 | Cybersecurity/SBOM ontology.               |
| `ariadne.yml`                 | Ariadne pipeline configuration.            |
| `ontology_metrics_policy.yml` | Ontology quality rules used by Ariadne.    |

## Case Study

The source data is a CycloneDX SBOM for Python packages. It contains BOM
metadata, generator tools, software components, versions, licenses and external
references.

The mapping creates RDF resources for the BOM, tools such as `CaPyCLI` and
`standard-bom`, components such as `numpy`, `pandas` and `matplotlib`, licenses
and external references.

## Use

Ariadne reads `ariadne.yml`, loads the input files and writes the RDF graph to
`knowledgegraph/knowledge-graph.nt`.

This example also validates ontology quality. The `ontology_metrics_policy.yml`
file defines which QASAR quality checks are expected to pass and the accepted
thresholds for this project.

For CI, set `QASAR_KEY` as a secret and `PIPELINE_IMAGE` as a repository
variable.
