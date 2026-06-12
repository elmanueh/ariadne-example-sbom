# ariadne-example-sbom

Minimal SBOM case study for **Ariadne**.

This example shows how Ariadne turns a CycloneDX SBOM into RDF.

## Contents

| Path              | Description                     |
| ----------------- | ------------------------------- |
| `datasources/`    | CycloneDX JSON input data.      |
| `knowledgegraph/` | Generated RDF graph.            |
| `mappings/`       | YARRRML mapping.                |
| `ontologies/`     | Cybersecurity/SBOM ontology.    |
| `ariadne.yml`     | Ariadne pipeline configuration. |

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

For CI, set `QASAR_KEY` as a secret and `PIPELINE_IMAGE` as a repository
variable.
