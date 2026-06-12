# Ontology Project Example

This repository is an example of how an ontology project can run the base pipeline from a public GHCR image.

## Pipeline Configuration

Create `ariadne.yml` in the repository root to configure paths and artifact output:

```yaml
inputs:
  datasources_dir: datasources
  ontology_dir: ontologies
  mapping_file: mappings/mapping.json.yaml

outputs:
  artifacts_dir: dist/artifacts
  graph_file: knowledgegraph/knowledge-graph.nt
```

The project needs:

- one folder for datasources;
- one mapping file;
- one folder for ontologies.

The paths can be changed in `ariadne.yml`.

## GitHub Actions Settings

The general analyzer is triggered manually from `Actions -> Analyzer -> Run workflow`.
The modular analyzer is triggered from `Actions -> Modular Analyzer -> Run workflow`.

Repository secrets:

```text
QASAR_KEY
```

Repository variables:

```text
PIPELINE_IMAGE=ghcr.io/OWNER/PRODUCER_REPOSITORY:sandbox
```

The pipeline image is public, so the workflows pull it without GHCR login.

Store:

- `QASAR_KEY`: repository secret with the QASAR API key.
- `PIPELINE_IMAGE`: repository variable with the full image name and tag.

The workflows read `outputs.artifacts_dir` and `outputs.graph_file` from `ariadne.yml`. The general analyzer uploads generated files as the `analyzer-artifacts` GitHub Actions artifact. The modular analyzer uploads the final merged bundle as `modular-analyzer-artifacts`.
When graph generation finishes successfully, the workflows copy `<outputs.artifacts_dir>/<outputs.graph_file>` into `<outputs.graph_file>` in the repository and commit it if it changed. If `outputs.graph_file` is omitted, the default graph path is `final_graph.nt`.

The modular analyzer workflow runs the same phases with `run-phase`, but splits them into jobs. Independent input phases run in parallel, and dependent phases download the phase artifacts produced earlier:

```text
datasource_validation + mapping_validation + ontology_analysis
  -> mapping_quality_analysis

ontology_analysis
  -> ontology_validation

mapping_quality_analysis + ontology_validation
  -> graph_generation
```

The duplicated Docker invocation lives in `.github/actions/run-analyzer-phase/action.yml`. Each phase job uploads `analyzer-phase-artifacts-<phase>`, excluding the aggregate `pipeline-context.json` so parallel jobs do not overwrite each other. Dependent jobs merge the `phase-context/*-context.json` files before running.
