# Ontology Project Example

This repository is an example of how an ontology project can run the base pipeline from a private GHCR image.

## Pipeline Configuration

You can optionally create `pipeline-config.yml` in the repository root to configure paths and artifact output:

```yaml
artifacts_dir: dist/artifacts

inputs:
  datasources: datasources
  mappings: mappings
  ontologies: ontologies
```

The project needs:

- one folder for datasources;
- one folder for mappings;
- one folder for ontologies.

The folder names can be changed in `pipeline-config.yml`.

## GitHub Actions Settings

The pipeline is triggered manually from `Actions -> Pipeline -> Run workflow`.

Repository secrets:

```text
GHCR_USER
GHCR_TOKEN
QASAR_KEY
```

Repository variable:

```text
PIPELINE_IMAGE=ghcr.io/OWNER/PRODUCER_REPOSITORY:sandbox
```

## GHCR Token Setup

The GitHub user stored in `GHCR_USER` must have `Read` access to the private GHCR package.

Create `GHCR_TOKEN` from that user account:

1. Open GitHub user settings.
2. Go to `Developer settings -> Personal access tokens -> Tokens (classic)`.
3. Click `Generate new token -> Generate new token (classic)`.
4. Select only `read:packages`.
5. Generate and copy the token.
6. Store it as repository secret `GHCR_TOKEN`.

Also store:

- `GHCR_USER`: repository secret with the GitHub username that created the token.
- `QASAR_KEY`: repository secret with the QASAR API key.
- `PIPELINE_IMAGE`: repository variable with the full image name and tag.

Generated files are uploaded as the `pipeline-artifacts` GitHub Actions artifact.
