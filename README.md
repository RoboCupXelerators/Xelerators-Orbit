# Xelerators Orbit

Public release control plane for Xelerators runtime containers.

This repository intentionally contains no application, simulator, or training
source code. GitHub Actions temporarily checks out source repositories,
builds immutable runner images and packages, publishes them, and updates the
OTA manifest consumed by target-host managers.

## Sources

- `RoboCupXelerators/Project-Exterior`
- `RoboCupXelerators/TreeDiagramDataset`

The source repositories are configured in `.github/workflows/publish.yml`.
Change the workflow inputs or repository variables when the source layout
changes.

## Outputs

- `ghcr.io/robocupxelerators/project-exterior-runner:<tag>`
- GitHub Release asset `project-exterior-runner.zip`
- `manifest.json` with image digest, source revisions, and release URL

Target managers should use the raw manifest URL:

```text
https://raw.githubusercontent.com/RoboCupXelerators/Xelerators-Orbit/main/manifest.json
```

The image must be pinned to the digest from the manifest in production. The
`stable` tag is only a discovery alias.

## Publishing

Run the `Publish runtime` workflow manually, or trigger it from a source
repository with `repository_dispatch` event `runtime-source-updated`. The
workflow requires package write permission and contents write permission.
The source repositories remain private: add a fine-grained
`SOURCE_REPO_TOKEN` secret with read-only access to both source repositories.
Add a separate `PACKAGE_ADMIN_TOKEN` secret with package visibility
administration permission so the workflow can make the compiled GHCR package
public. The workflow fails before creating a Release if anonymous GHCR pull is
not possible.

The container image is the primary OTA artifact. The Release ZIP is a fallback
for native/offline installation; neither artifact contains the private source
repositories.
