# _templates
[![Release](https://github.com/obervinov/_templates/actions/workflows/_release.yaml/badge.svg)](https://github.com/obervinov/_templates/actions/workflows/_release.yaml)
[![PR](https://github.com/obervinov/_templates/actions/workflows/_pr.yaml/badge.svg)](https://github.com/obervinov/_templates/actions/workflows/_pr.yaml)

![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/obervinov/_templates?style=for-the-badge)
![GitHub last commit](https://img.shields.io/github/last-commit/obervinov/_templates?style=for-the-badge)
![GitHub Release Date](https://img.shields.io/github/release-date/obervinov/_templates?style=for-the-badge)
![GitHub issues](https://img.shields.io/github/issues/obervinov/_templates?style=for-the-badge)
![GitHub repo size](https://img.shields.io/github/repo-size/obervinov/_templates?style=for-the-badge)

## <img src="https://github.com/obervinov/_templates/blob/main/icons/book.png" width="25" title="about"> About this project
Reusable GitHub Actions workflows shared across my repositories, plus the icons used in
their documentation.

Every workflow here is `workflow_call` only — it is called by a repository, never run on
its own. The two prefixed with an underscore are the exception: they are this
repository's own CI, not templates.

## <img src="https://github.com/obervinov/_templates/blob/v1.0.5/icons/github-actions.png" width="25" title="github-actions"> The workflows

### Language and ecosystem checks
| Workflow | Purpose | Inputs |
| -------- | ------- | ------ |
| [`golang.yaml`](.github/workflows/golang.yaml) | `gofmt`, `go vet`, `golangci-lint`, `go test -race -cover`, `go build`. Takes the toolchain from `go.mod` | none |
| [`golang-binaries.yaml`](.github/workflows/golang-binaries.yaml) | Cross-compiles every main package under `./cmd/*` for `linux/amd64` and `linux/arm64` and attaches them with a `SHA256SUMS` file to the release | none |
| [`nodejs.yaml`](.github/workflows/nodejs.yaml) | `node --check` over every JS file, then the built-in test runner. For repositories that carry JavaScript without being JavaScript projects | none |
| [`pyproject.yaml`](.github/workflows/pyproject.yaml) | Python lint, test and version checks | none |
| [`yamllint.yaml`](.github/workflows/yamllint.yaml) | Lints YAML, including the workflows themselves | none |
| [`terraform.yaml`](.github/workflows/terraform.yaml) | `terraform fmt` and generated docs | yes |

### Building and publishing
| Workflow | Purpose | Inputs |
| -------- | ------- | ------ |
| [`images.yaml`](.github/workflows/images.yaml) | Build, sign, attest and scan every image in a one-directory-per-image repository. Documented in full [below](#-imagesyaml) | yes |
| [`docker.yaml`](.github/workflows/docker.yaml) | Build and scan a single image from the repository root | none |
| [`helm-charts.yaml`](.github/workflows/helm-charts.yaml) | Lint, package and publish helm charts | none |

### Release automation
| Workflow | Purpose | Inputs |
| -------- | ------- | ------ |
| [`pr.yaml`](.github/workflows/pr.yaml) | Opens or updates the pull request from `CHANGELOG.md`, and fails when the version or date at the top of it was not bumped | none |
| [`release.yaml`](.github/workflows/release.yaml) | Creates the release and tag from the top `CHANGELOG.md` entry, using that section as the release body | none |

`CHANGELOG.md` is the source of truth for both: the version and date at the top of the
file decide the tag, the release body and the pull request body. The date is compared
against the runner's clock, which is **UTC**.

### This repository's own CI
| Workflow | Purpose |
| -------- | ------- |
| [`_pr.yaml`](.github/workflows/_pr.yaml) | Runs on a push to a branch here |
| [`_release.yaml`](.github/workflows/_release.yaml) | Runs on a push to `main` here |

### Consumer-specific
| Workflow | Purpose |
| -------- | ------- |
| [`instagrapi.yaml`](.github/workflows/instagrapi.yaml) | Checks that the pinned `instagrapi` version still resolves. Only `pyinstabot-downloader` calls it |

## <img src="https://github.com/obervinov/_templates/blob/main/icons/github-actions.png" width="25" title="usage"> Calling a workflow
Most of these take no inputs at all — a caller only needs to name them. A Go project
using every relevant one:

```yaml
---
name: PR

on:
  push:
    branches: ['*', '*/*', '**', '!main']

# A reusable workflow can never hold more than the caller grants it.
permissions:
  contents: write
  pull-requests: write
  security-events: write
  actions: write
  checks: read

jobs:
  pr:
    uses: obervinov/_templates/.github/workflows/pr.yaml@v3.9.0

  golang:
    uses: obervinov/_templates/.github/workflows/golang.yaml@v3.9.0

  nodejs:
    uses: obervinov/_templates/.github/workflows/nodejs.yaml@v3.9.0
```

and its release side:

```yaml
---
name: Release

on:
  pull_request:
    branches: [main]
    types: [closed]

permissions:
  contents: write
  pull-requests: write
  actions: write
  checks: read

jobs:
  create-release:
    if: github.event.pull_request.merged == true
    uses: obervinov/_templates/.github/workflows/release.yaml@v3.9.0

  attach-binaries:
    if: github.event.pull_request.merged == true
    uses: obervinov/_templates/.github/workflows/golang-binaries.yaml@v3.9.0
    needs: [create-release]
```

Always pin a tag rather than `main`: these are shared, and a change here reaches every
consumer at once.

## <img src="https://github.com/obervinov/_templates/blob/main/icons/docker.png" width="25" title="images"> images.yaml
The only workflow here with a surface worth documenting at length; everything below
this heading is about `images.yaml` alone.

A supply-chain pipeline for repositories that keep one image per directory (`docker/<name>/Dockerfile`).

One run: pick only the images whose directory actually changed, build every platform once, push the same index to one or more registries, execute the emulated result, sign the index with keyless [cosign](https://github.com/sigstore/cosign), attach an SBOM and a max-mode provenance attestation, and scan **both** child manifests by digest into per-arch code-scanning categories.

Every third-party action is pinned to an immutable commit SHA — a workflow whose purpose is provenance cannot itself depend on a mutable tag.

### images.yaml — usage
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
      actions: read
    uses: obervinov/_templates/.github/workflows/images.yaml@v3.0.0
    with:
      images-path: docker
      image: ${{ inputs.image }}
      exclude: certbot, glab, gradle, trivy-ui, profile-header-generator
    secrets:
      registry-credentials: ${{ secrets.REGISTRY_CREDENTIALS }}
```

Nothing is required: called with no `with:` block at all, it builds every changed directory under `docker/` for `linux/amd64,linux/arm64` and publishes to `ghcr.io` using the job's own `GITHUB_TOKEN`.

### images.yaml — inputs
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
| `code-scanning` | boolean | `true` | Upload Trivy results to GitHub code scanning. A private repository without Advanced Security cannot accept them — set this to `false` and findings are printed in the job log instead. |
| `buildkit-endpoint` | string | `''` | Address of a BuildKit instance to build on, e.g. `tcp://buildkitd.ci-cd.svc.cluster.local:1234`. Set it and the job drives that instance instead of starting a builder locally — which is what lets a runner with no Docker daemon build images. QEMU becomes the remote instance's concern, and the smoke test is skipped with a warning, since running an image needs a local daemon. |
| `buildkit-append` | string | `''` | Further BuildKit instances to join to the builder, as the YAML list `docker/setup-buildx-action` takes — an `endpoint` and the `platforms` it serves, per entry. One builder per architecture means every platform is built natively, and nothing has to emulate anything. Ignored unless `buildkit-endpoint` is set. |
| `skip-marker` | string | `.no-build` | A file that marks an image directory as not to be built. The directory is skipped unless it is asked for by name through `image`, so a retired image carries its own reason next to it instead of being listed in a caller's `exclude` string. Empty disables it. |

Each image's `org.opencontainers.image.description` LABEL is lifted out of its Dockerfile and re-attached as an annotation on the published index, together with `title`, `source`, `version` and `revision`. A registry reads a multi-platform image's description from the index annotations, not from the layer config a LABEL produces — without this a package page shows *No description provided* however well the Dockerfile is labelled. An image with no description LABEL builds normally and logs a warning.

### images.yaml — secrets
| Name | Required | Description |
| ---- | -------- | ----------- |
| `registry-credentials` | no | Credentials for the registries, one line per host: `<host> <username> <password>`. `ghcr.io` needs no entry — the job's own `GITHUB_TOKEN` is used. A host with no line is skipped with a warning unless it is the primary one, which fails the run. |

GitHub Actions secret names are static, so a separate secret pair per registry cannot be declared for an arbitrary number of targets. One secret holding a line per host is what keeps the registry list configurable.

### images.yaml — outputs
| Name | Description |
| ---- | ----------- |
| `images` | JSON array of the image directories this run selected. |
| `count` | Number of selected image directories. |
| `published` | `true` when the run published, signed and scanned; `false` when it was a build-only check. |

Per-image digests are deliberately not exposed: the publishing job is a matrix, and matrix legs share one output namespace, so the last leg to finish would overwrite the others.

### images.yaml — permissions
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
