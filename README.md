# Ontology Project Example

This repository is an example of how an ontology project can run the base pipeline from a public GHCR image.

## Pipeline Configuration

Create `pipeline-config.yml` in the repository root to configure paths and artifact output:

```yaml
artifacts_dir: dist/artifacts

inputs:
  datasources: datasources
  mappings: mappings
  ontologies: ontologies

outputs:
  graph: generated/final_graph.nt
```

The project needs:

- one folder for datasources;
- one folder for mappings;
- one folder for ontologies.

The folder names can be changed in `pipeline-config.yml`.

## GitHub Actions Settings

The general analyzer is triggered manually from `Actions -> Analyzer -> Run workflow`.
The modular analyzer is triggered from `Actions -> Modular Analyzer -> Run workflow`.

Repository secrets:

```text
QASAR_KEY
```

Repository variable:

```text
PIPELINE_IMAGE=ghcr.io/OWNER/PRODUCER_REPOSITORY:sandbox
```

The pipeline image is public, so the workflows pull it without GHCR login.

Store:

- `QASAR_KEY`: repository secret with the QASAR API key.
- `PIPELINE_IMAGE`: repository variable with the full image name and tag.

The workflows read `artifacts_dir` from `pipeline-config.yml`. The general analyzer uploads generated files as the `analyzer-artifacts` GitHub Actions artifact. The modular analyzer uploads the final merged bundle as `modular-analyzer-artifacts`.
When graph generation finishes successfully, the workflows copy `<artifacts_dir>/final_graph.nt` into the repository and commit it if `outputs.graph` is configured in `pipeline-config.yml`. If `outputs.graph` is omitted or empty, the graph is only uploaded as a workflow artifact and the repository is not updated.

The modular analyzer workflow runs the same phases with `run-phase`, but splits them into jobs. Independent input phases run in parallel, and dependent phases download the phase artifacts produced earlier:

```text
datasource_validation + mapping_validation + ontology_analysis
  -> mapping_quality_analysis

ontology_analysis
  -> qasar_analysis

mapping_quality_analysis + qasar_analysis
  -> graph_generation
```

The duplicated Docker invocation lives in `.github/actions/run-analyzer-phase/action.yml`. Each phase job uploads `analyzer-phase-artifacts-<phase>`, excluding the aggregate `pipeline-context.json` so parallel jobs do not overwrite each other. Dependent jobs merge the `phase-context/*-context.json` files before running.
