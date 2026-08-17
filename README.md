# _templates
[![Release](https://github.com/obervinov/_templates/actions/workflows/_release.yaml/badge.svg)](https://github.com/obervinov/_templates/actions/workflows/_release.yaml)
[![PR](https://github.com/obervinov/_templates/actions/workflows/_pr.yaml/badge.svg)](https://github.com/obervinov/_templates/actions/workflows/_pr.yaml)

![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/obervinov/_templates?style=for-the-badge)
![GitHub last commit](https://img.shields.io/github/last-commit/obervinov/_templates?style=for-the-badge)
![GitHub Release Date](https://img.shields.io/github/release-date/obervinov/_templates?style=for-the-badge)
![GitHub issues](https://img.shields.io/github/issues/obervinov/_templates?style=for-the-badge)
![GitHub repo size](https://img.shields.io/github/repo-size/obervinov/_templates?style=for-the-badge)

## <img src="https://github.com/obervinov/_templates/blob/main/icons/book.png" width="25" title="about"> About this project
This repository contains templates for creating standard repositories
- **Workflow templates for GitHub Actions**
  - docker
  - images (multi-image build, sign, attest and scan)
  - python
  - go
  - terraform
  - release
  - pull-request
  - yaml
  - helm
- **Icons for documentation other repositories**

## <img src="https://github.com/obervinov/_templates/blob/v1.0.5/icons/github-actions.png" width="25" title="github-actions"> GitHub Actions
| Name  | Version |
| ------------------------ | ----------- |
| GitHub Actions Templates | [main](https://github.com/obervinov/_templates/tree/main) |

| Workflow | Purpose |
| -------- | ------- |
| [`images.yaml`](.github/workflows/images.yaml) | Build, sign, attest and scan every image in a one-directory-per-image repository |
| [`docker.yaml`](.github/workflows/docker.yaml) | Build and scan a single image from the repository root |
| [`helm-charts.yaml`](.github/workflows/helm-charts.yaml) | Lint, package and publish helm charts |
| [`pyproject.yaml`](.github/workflows/pyproject.yaml) | Python lint, test and version checks |
| [`golang.yaml`](.github/workflows/golang.yaml) | Go fmt, vet, lint, test and build |
| [`terraform.yaml`](.github/workflows/terraform.yaml) | Terraform fmt and docs |
| [`pr.yaml`](.github/workflows/pr.yaml) / [`release.yaml`](.github/workflows/release.yaml) | Changelog-driven pull request and release automation |
| [`yamllint.yaml`](.github/workflows/yamllint.yaml) | Lint the workflows themselves |

## <img src="https://github.com/obervinov/_templates/blob/main/icons/docker.png" width="25" title="images"> images.yaml
A supply-chain pipeline for repositories that keep one image per directory (`docker/<name>/Dockerfile`).

One run: pick only the images whose directory actually changed, build every platform once, push the same index to one or more registries, execute the emulated result, sign the index with keyless [cosign](https://github.com/sigstore/cosign), attach an SBOM and a max-mode provenance attestation, and scan **both** child manifests by digest into per-arch code-scanning categories.

Every third-party action is pinned to an immutable commit SHA — a workflow whose purpose is provenance cannot itself depend on a mutable tag.

### Usage
```yaml
---
name: Build images

on:
  push:
  workflow_dispatch:
    inputs:
      image:
        description: "Build a single directory, e.g. docker/debug. Empty = all changed."
        required: false

jobs:
  images:
    # A reusable workflow can never hold more than the caller grants it.
    permissions:
      contents: read
      packages: write
      id-token: write
      security-events: write
    uses: obervinov/_templates/.github/workflows/images.yaml@v3.0.0
    with:
      images-path: docker
      image: ${{ inputs.image }}
      exclude: certbot, glab, gradle, trivy-ui, profile-header-generator
    secrets:
      registry-credentials: ${{ secrets.REGISTRY_CREDENTIALS }}
```

Nothing is required: called with no `with:` block at all, it builds every changed directory under `docker/` for `linux/amd64,linux/arm64` and publishes to `ghcr.io` using the job's own `GITHUB_TOKEN`.

### Inputs
| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| `runs-on` | string | `ubuntu-latest` | Runner label the jobs execute on. A self-hosted label works here too. |
| `images-path` | string | `docker` | Directory holding one sub-directory per image. |
| `dockerfile` | string | `Dockerfile` | Dockerfile name inside each image directory. |
| `version-arg` | string | `IMAGE_VERSION` | Dockerfile `ARG` that carries the image version, used as the image tag. |
| `image` | string | `''` | Build only this image (directory name or path). Empty = every changed image. |
| `exclude` | string | `''` | Image directories to never build, separated by commas or spaces. |
| `build-all` | boolean | `false` | Ignore change detection and build every discovered image. |
| `platforms` | string | `linux/amd64,linux/arm64` | Platforms passed to buildx. SARIF is uploaded for `linux/amd64` and `linux/arm64`. |
| `registries` | string | `ghcr.io` | Registry hosts to push to, separated by commas or newlines. The first one is primary. |
| `namespace` | string | `''` | Path between the registry host and the image name. Defaults to `owner/repo`. |
| `publish` | boolean | `true` | Allow publishing. Even when true, only the default branch and `workflow_dispatch` publish. |
| `latest-tag` | boolean | `true` | Also point `<image>:latest` at the published index. |
| `attestations` | boolean | `true` | Attach an SBOM and a max-mode provenance attestation to the pushed index. |
| `sign` | boolean | `true` | Sign the published index with keyless cosign. |
| `smoke-test` | boolean | `true` | Execute the published image on every built platform before signing it. |
| `smoke-test-entrypoint` | string | `uname` | Entrypoint override for the smoke test. Set empty to keep the image entrypoint. |
| `smoke-test-args` | string | `-m` | Arguments for the smoke test entrypoint. |
| `trivy-severity` | string | `HIGH,CRITICAL` | Severities Trivy reports. |
| `trivy-ignore-unfixed` | boolean | `true` | Report only vulnerabilities that have an upstream fix. |

### Secrets
| Name | Required | Description |
| ---- | -------- | ----------- |
| `registry-credentials` | no | Credentials for the registries, one line per host: `<host> <username> <password>`. `ghcr.io` needs no entry — the job's own `GITHUB_TOKEN` is used. A host with no line is skipped with a warning unless it is the primary one, which fails the run. |

GitHub Actions secret names are static, so a separate secret pair per registry cannot be declared for an arbitrary number of targets. One secret holding a line per host is what keeps the registry list configurable.

### Outputs
| Name | Description |
| ---- | ----------- |
| `images` | JSON array of the image directories this run selected. |
| `count` | Number of selected image directories. |
| `published` | `true` when the run published, signed and scanned; `false` when it was a build-only check. |

Per-image digests are deliberately not exposed: the publishing job is a matrix, and matrix legs share one output namespace, so the last leg to finish would overwrite the others.

### Permissions
The caller must grant the job at least the set below; a reusable workflow can never hold more than the caller gives it.

| Permission | Why |
| ---------- | --- |
| `contents: read` | Checkout and `git merge-base` change detection. |
| `packages: write` | Push to `ghcr.io`. |
| `id-token: write` | Keyless cosign exchanges this for an OIDC token from `token.actions.githubusercontent.com`; there is no key to store or rotate. |
| `security-events: write` | Upload the Trivy SARIF to code scanning. |

### Publishing to more than one registry
```yaml
    with:
      registries: |
        ghcr.io
        zot.homelab.lan
    secrets:
      registry-credentials: ${{ secrets.REGISTRY_CREDENTIALS }}
```
with `REGISTRY_CREDENTIALS` set to:
```text
zot.homelab.lan robot-publisher <token>
```

Both targets are `-t` names on a **single** `docker buildx build --push`, so arm64 emulation runs once no matter how many registries there are. The first registry is primary and fails the run; every other target is advisory — an unreachable homelab registry warns and is dropped rather than blocking a release to `ghcr.io`.

### Verifying a published image
```shell
cosign verify \
  --certificate-identity-regexp "^https://github.com/obervinov/_templates/.github/workflows/images.yaml@refs/tags/v3" \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com \
  ghcr.io/<owner>/<repo>/<image>:<tag>
```
Both `--certificate-*` flags are load-bearing. Without them `cosign verify` accepts a signature from any identity Sigstore will issue a certificate to — which proves that *someone* signed the image, not that this pipeline did. Because the workflow is reusable, the signing identity is the **template's** path, not the calling repository's.

```shell
docker buildx imagetools inspect <ref> --format '{{ json .SBOM }}'
docker buildx imagetools inspect <ref> --format '{{ json .Provenance }}'
```
