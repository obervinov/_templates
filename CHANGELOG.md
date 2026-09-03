# Change Log
All notable changes to this project will be documented in this file.
The format is based on [Keep a Changelog](http://keepachangelog.com/) and this project adheres to [Semantic Versioning](http://semver.org/).


## v3.9.1 - 2026-09-03
### What's Changed
#### 🐛 Bug Fixes
* `_release.yaml`: release on a change to `CHANGELOG.md` or `README.md`, not only to `.github/workflows/**`. The narrow path filter contradicted `pr.yaml`, which fails a pull request whose CHANGELOG version was not bumped — so a documentation change was forced to declare a version that then never became a tag, and every later version drifted one ahead of the releases.

#### 📚 Documentation
* `README.md`: cover every workflow instead of two thirds of the file documenting only `images.yaml`. The "About this project" list had drifted from the directory — `nodejs.yaml` and `golang-binaries.yaml` were missing and several entries named a language rather than a file — and the generic `### Usage` / `### Inputs` / `### Secrets` headings read as if the repository held one workflow. The tables now group the twelve by what they are for, mark which take inputs, separate the two underscore-prefixed workflows that are this repository's own CI from the templates, and say outright that `CHANGELOG.md` drives the release automation and that its date is compared in UTC. The `images.yaml` deep-dive is unchanged but scoped under its own name.

## v3.9.0 - 2026-09-03
### What's Changed
#### 🚀 Features
* `nodejs.yaml`: new template running `node --check` over every JavaScript file and then the built-in test runner. For repositories that carry JavaScript without being JavaScript projects — a Cloudflare Worker beside a Go binary, where `golang.yaml` never looks at the `.js` files at all. Needs no `package.json`, lockfile or dependencies. A repository with no test files gets a warning rather than a failure, since `node --test` exits non-zero when nothing matches.

## v3.8.0 - 2026-09-03
### What's Changed
#### 🐛 Bug Fixes
* `golang.yaml`: take the Go version from `go.mod` instead of always installing `stable`. `golangci-lint` type-checks with the toolchain it was built against, so once the runners moved to Go 1.27 every Go project failed with `panic: file requires newer Go version go1.27 (application built with go1.26)` before a single linter ran. Following the module also keeps CI on the version the project declares, rather than silently moving under it.
* `pr.yaml`: do not fail on a repository that has no tags yet. `action-get-latest-tag` exits with `fatal: No names found, cannot describe anything`, which made the template unusable for the first release of a new project. The step is now allowed to fail and the version comparison runs against an empty tag — correct, since nothing has been released for a CHANGELOG version to collide with.

## v3.7.0 - 2026-09-03
### What's Changed
#### 🚀 Features
* `golang-binaries.yaml`: new template attaching statically linked binaries plus a `SHA256SUMS` file to the release that `release.yaml` created. `docker.yaml` covers projects shipped as an image; this covers projects installed as a binary on a host, where the consumer downloads the artifact and needs a checksum to verify it against. Follows the existing convention of taking no inputs: the version comes from `CHANGELOG.md` and every main package under `./cmd/*` is built for `linux/amd64` and `linux/arm64`, named after its directory. Builds on any branch, uploads only on the default branch.

## v3.6.0 - 2026-08-25
### What's Changed
#### 🐛 Bug Fixes
* `images.yaml`: stop the `registry-credentials` secret from erasing credentials the runner already provided. The login step assigned the secret straight to `REGISTRY_CREDENTIALS`, so a caller that passed none set that variable to an empty string — overwriting whatever the environment held, which is exactly the case where a self-hosted runner had exported it. The input now lands in `REGISTRY_CREDENTIALS_INPUT` and wins when set; otherwise the environment is used. Callers that pass the secret see no change, and a cluster that keeps its secrets in Vault can now configure nothing on the GitHub side at all.

## v3.5.0 - 2026-08-21
### What's Changed
#### 🚀 Features
* `images.yaml`: add `buildkit-append`, passing further BuildKit instances through to the builder. With one instance per architecture each platform is built on hardware that already speaks it, which removes the reason to emulate — and emulation is what needs binfmt handlers, which are kernel-wide state only a privileged container can register. A cluster that owns a node of each architecture can therefore build multi-platform images with no privileged container anywhere. Ignored unless `buildkit-endpoint` is set, so nothing changes for callers that do not use it.

## v3.4.0 - 2026-08-21
### What's Changed
#### 🚀 Features
* `images.yaml`: add `buildkit-endpoint`. Pointed at a BuildKit instance, the job drives that instead of starting a builder on the runner — which is what lets a runner with no Docker daemon build images at all, and is the difference between needing a privileged container and not. QEMU setup is skipped, because emulation belongs wherever the build runs and registering binfmt handlers on a daemonless runner is a privileged no-op. The smoke test is skipped too, with a warning rather than in silence: running an image needs a local daemon, and a signature on an image nobody executed is worth less — that belongs in the log instead of being inferred from an absence. Unset, every path behaves exactly as before.

## v3.3.0 - 2026-08-20
### What's Changed
#### 🚀 Features
* `images.yaml`: add `skip-marker` (default `.no-build`). A directory holding that file is left out of automatic selection, so an image that is retired or published elsewhere carries the reason in a file next to it rather than as one more name in a caller's `exclude` string — a list that grows, is edited far from the thing it describes, and does not disappear when the directory does. Asking for a directory by name through `image` still builds it: a marker governs automatic selection, an explicit request is deliberate. `exclude` keeps working and is unchanged.
#### 🐛 Bug Fixes
* `images.yaml`: fail with `no registries configured` when `registries` resolves to nothing. Previously the login loop simply never ran, no target was recorded, and the build died much later inside buildx complaining about a missing tag — an unhelpful error a long way from its cause. This is reachable in practice: pointing `registries` at a repository variable that has not been set yet produces exactly that.

## v3.2.0 - 2026-08-19
### What's Changed
#### 🚀 Features
* `images.yaml`: annotate the published index with `org.opencontainers.image.description`, `title`, `source`, `version` and `revision`. A registry reads a multi-platform image's description from the index annotations; a `LABEL` only reaches the layer config, which the package page of such an image never looks at — so every image showed *No description provided* despite all of them carrying a description LABEL. The description is lifted out of the Dockerfile at build time, so it stays declared in one place, and both `LABEL key "value"` and `LABEL key="value"` are recognised. An image without the LABEL builds as before and logs a warning.

## v3.1.0 - 2026-08-19
### What's Changed
#### 🚀 Features
* `images.yaml`: add `code-scanning` (default `true`). Uploading SARIF needs code scanning to be available on the repository, and a private repository on a personal account cannot have it — enabling it answers `Advanced security has not been purchased`, so the upload failed at the last step of an otherwise complete run. With the input set to `false` the upload is skipped, Trivy switches from SARIF to table output, and a new step prints the findings in the job log. The scan gates the build in neither mode: `exit-code` stays `0`, because a base-image CVE is not a reason to withhold an image that is otherwise ready.

## v3.0.3 - 2026-08-19
### What's Changed
#### 🐛 Bug Fixes
* `images.yaml`: grant `actions: read` to the publishing job. `upload-sarif` reads the workflow run it is uploading results for, and on a **private** repository that read is not implied by `security-events: write` — the step fails with `Resource not accessible by integration` after the image has already been built, scanned and signed. The permission set was trimmed in v3.0.0 on the reasoning that `actions` was unused, which held while the source repository was public and stopped holding when it went private. Callers must add `actions: read` to their own `permissions` block as well, since a called workflow cannot exceed what the caller grants.

## v3.0.2 - 2026-08-19
### What's Changed
#### 🐛 Bug Fixes
* `images.yaml`: a shell comment added in v3.0.1 contained an empty `${{ }}`, and GitHub interpolates expressions everywhere in a workflow file — inside `run:` bodies and comments included. An empty expression does not parse, so the whole file became unloadable: pushing it produced a run with zero jobs, and every repository calling the workflow failed to load with "This run likely failed because of a workflow file issue". The comment now describes the expression context without writing one. Valid YAML the whole time, which is why a YAML parser passed it — `actionlint` reports it as `unexpected end of input while parsing variable access` and is the check that catches this class.

## v3.0.1 - 2026-08-19
### What's Changed
#### 🐛 Bug Fixes
* `images.yaml`: the smoke test ran `docker run --platform` against the **index** digest, and Docker refuses that — the single-platform image it resolves to carries a different digest than the reference, so it fails with `cannot overwrite digest` and takes the build down *after* the image has already been pushed, unsigned and unscanned. It now runs each platform's own child manifest, which the digest step already resolves; those digests are carried across as one `platform=digest` output because a step cannot build an output name like `linux_amd64` dynamically when reading it back. A platform with no resolved child now fails with a message instead of falling through to the index.

## v3.0.0 - 2026-08-14
### What's Changed
#### 💥 Breaking Changes
* Rewrite `images.yaml` as a full supply-chain pipeline. It is now three jobs (`prepare-images-matrix`, `verify-images-template`, `publish-images-template`) instead of one, takes 19 inputs, one optional secret and returns three outputs. Callers must grant `contents: read`, `packages: write`, `id-token: write` and `security-events: write` — a reusable workflow can never hold more than the caller gives it.
* `images.yaml` no longer publishes from every branch. Only the default branch and a manual `workflow_dispatch` publish; everything else is a build-only check.
#### 🚀 Features
* Sign the published image index with keyless [cosign](https://github.com/sigstore/cosign) and attach an SBOM plus a max-mode provenance attestation, both addressed by digest rather than by a movable tag.
* Push one build to several registries. Every target is a `-t` name on a single `docker buildx build --push`, so arm64 emulation runs once regardless of the number of registries. The first registry is primary and fails the run; the rest are advisory and are dropped with a warning when credentials are missing or the host is unreachable.
* Replace the hardcoded matrix with a `prepare-images-matrix` job that emits JSON consumed through `fromJSON`, so an unchanged image never starts a runner. Previously twelve runners started, each cloning the repository and installing QEMU, and eleven of them then did nothing.
* Change detection survives force-pushes: when `github.event.before` is unreachable the fork point from the default branch is used instead, and when no comparison point can be established at all the build runs rather than risking a skipped change.
* Restore the arm64 smoke test dropped from the per-image pipeline. Every built platform is executed before the signature goes on it, and the default `uname -m` probe is asserted against the expected architecture — signing an image nobody ever ran only proves it was built.
* Scan both child manifests by digest into separate SARIF categories. Trivy resolves a multi-arch tag to the runner's own platform, so a single scan left `linux/arm64` unscanned.
* Expose `runs-on` as an input so the same workflow can target self-hosted runners.
* Add `image`, `exclude`, `build-all`, `platforms`, `namespace`, `latest-tag`, `smoke-test*` and `trivy-*` inputs, and attach OCI source/revision/version labels to every published image.
#### 🔒 Security
* Pin every third-party action in `images.yaml` to an immutable commit SHA with the version in a trailing comment: `actions/checkout` `v6.1.0`, `docker/setup-qemu-action` `v4.2.0`, `docker/setup-buildx-action` `v4.2.0`, `sigstore/cosign-installer` `v4.1.2`, `aquasecurity/trivy-action` `v0.36.0`, `github/codeql-action/upload-sarif` `v4.37.7`. A workflow whose purpose is provenance cannot depend on mutable tags.
* Cut the permission set to the minimum. `contents: write`, `actions: write` and `checks: read` were granted and never used.
* No `run:` block interpolates a `${{ }}` expression any more — every value arrives through `env:`, which closes the script-injection path and makes the shell independently checkable.
#### 🐛 Bug Fixes
* Drop the `apt-key add` Trivy install from `images.yaml`. `apt-key` has been deprecated since Debian 11, and `trivy-action` was already in use elsewhere in the repository.
* Replace the unquoted `find . -name Dockerfile` loop and the `awk -F '/' '{print $3}'` path-depth assumption with a quoted directory walk that works at any depth and with any repository layout.
* Collapse ten repetitions of `if: steps.check_changes.outputs.build_needed == 'true' && env.PUBLISH == 'true'` into job-level conditions with `needs`. A new step that copied only half of that condition would silently have run on pull requests.

## v2.2.3 - 2026-07-31
### What's Changed
#### 🐛 Bug Fixes
* Fix `aquasecurity/trivy-action` reference to `v0.36.0` (the tags are `v`-prefixed; `0.36.0` does not resolve).

## v2.2.2 - 2026-07-30
### What's Changed
#### 🔒 Security
* Bump `aquasecurity/trivy-action` to `0.36.0`, fixing the critical supply-chain advisory affecting versions `< 0.35.0`.
#### ⬆️ Dependencies
* Bump `actions/checkout` to `v6`, `actions/setup-python` to `v6`, and `github/codeql-action` to `v4`.
#### 🐛 Bug Fixes
* Fix a stray double quote in `pr.yaml`'s "create pull request" step that broke the shell (`unexpected EOF`) when the branch had no PR yet.

## v2.2.1 - 2026-07-30
### What's Changed
#### 🐛 Bug Fixes
* Bump `golang.yaml` to `golangci-lint-action@v8` pinned to golangci-lint `v2.12.2`. The v6 action pinned an older golangci-lint built with an older Go that refuses to lint Go 1.25 modules (`the Go language version used to build golangci-lint is lower than the targeted Go version`); v2.12.2 is built with a current Go and lints them correctly.

## v2.2.0 - 2026-07-02
### What's Changed
#### 🚀 Features
* Add `golang.yaml` reusable workflow: gofmt, go vet, golangci-lint, `go test -race -cover` and build for Go projects.

## v2.1.1 - 2025-01-29
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v2.1.0...v2.1.1 by @obervinov in https://github.com/obervinov/_templates/pull/104
#### 🐛 Bug Fixes
* fix docker workflow mistakes


## v2.1.0 - 2025-01-23
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v2.0.2...v2.1.0 by @obervinov in https://github.com/obervinov/_templates/pull/103
#### 🚀 Features
* add support arm64 architecture for docker builds
* bump dependency versions


## v2.0.2 - 2024-10-23
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v2.0.1...v2.0.2 by @obervinov in https://github.com/obervinov/_templates/pull/98
#### 🚀 Features
* bump dependency versions
#### 🐛 Bug Fixes
* set trivy job as not necessary (for fix `TOOMANYREQUESTS` error)


## v2.0.1 - 2024-10-10
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v2.0.0...v2.0.1 by @obervinov in https://github.com/obervinov/_templates/pull/95
#### 🚀 Features
* [Bump aquasecurity/trivy-action from 0.25.0 to 0.26.0 in /.github/workflows](https://github.com/obervinov/_templates/pull/95)


## v2.0.0 - 2024-10-10
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.2.9...v2.0.0 by @obervinov in https://github.com/obervinov/_templates/pull/94
#### 💥 Breaking Changes
* [Feature request: Bump python version to `3.12`](https://github.com/obervinov/_templates/issues/93)
* [Merge all workflows into group files (details at the link)](https://github.com/obervinov/_templates/issues/72)
* bump `terraform` version to `1.9.2`
#### 🚀 Features
* [Feature request: automatic scheduled cleaning of untagged images in ghcr](https://github.com/obervinov/_templates/issues/86)
* [Feature request: Add full SemVer support for python projects](https://github.com/obervinov/_templates/issues/92)


## v1.2.9 - 2024-09-05
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.2.8...v1.2.9 by @obervinov in https://github.com/obervinov/_templates/pull/90
#### 🐛 Bug Fixes
* Remove the matrix strategy from the python workflow
#### 💥 Breaking Changes
* Remove the matrix strategy from the python workflow (because it's causing conflicts with the github actions services)
* Let's use only one version of python in the python workflow (`3.10`)
#### 🚀 Features
* Bump poetry version to `1.8.3` in the python workflow
* Bump vault-server image version to `1.17.2` in the python workflows
* Add debug log level for the vault services in the python workflows


## v1.2.8 - 2024-08-14
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.2.7...v1.2.8 by @obervinov in https://github.com/obervinov/_templates/pull/85
#### 🐛 Bug Fixes
* hotfix: missconfig for docker build and trivy scan in the docker workflow


## v1.2.7 - 2024-08-12
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.2.6...v1.2.7 by @obervinov in https://github.com/obervinov/_templates/pull/84
#### 🚀 Features
* Bump trivy-action to `0.24.0` in the docker workflow
* [Feature request: Docker workflow: auto create `latest` tag in PR](https://github.com/obervinov/_templates/issues/83)
* Change the new image version detection selector from `LABEL org.opencontainers.image.version` to `ARG IMAGE_VERSION=` in `images` workflow  
#### 💥 Breaking Changes
* Change the new image version detection selector from `LABEL org.opencontainers.image.version` to `ARG IMAGE_VERSION=` in `images` workflow


## v1.2.6 - 2024-06-09
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.2.5...v1.2.6 by @obervinov in https://github.com/obervinov/_templates/pull/84
#### 🚀 Features
* [Add support for `PostgreSQL` in the pytest workflow](https://github.com/obervinov/_templates/pull/84)
* [Bump trivy-action to `0.22.0` in the docker workflow](https://github.com/obervinov/_templates/pull/78)


## v1.2.5 - 2024-05-29
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.2.4...v1.2.5 by @obervinov in https://github.com/obervinov/_templates/pull/82
#### 🐛 Bug Fixes
* [Docker workflow: fix error when to extract tag value](https://github.com/obervinov/_templates/pull/82)


## v1.2.4 - 2024-05-29
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.2.3...v1.2.4 by @obervinov in https://github.com/obervinov/_templates/pull/81
#### 🐛 Bug Fixes
* [Docker workflow: fix tag format for building images](https://github.com/obervinov/_templates/pull/81)


## v1.2.3 - 2024-05-29
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.2.2...v1.2.3 by @obervinov in https://github.com/obervinov/_templates/pull/80
#### 🐛 Bug Fixes
* [Docker workflow: fix the workflow on `main` branch](https://github.com/obervinov/_templates/pull/80)


## v1.2.2 - 2024-04-28
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.2.1...v1.2.2 by @obervinov in https://github.com/obervinov/_templates/pull/76
#### 🐛 Bug Fixes
* Change image repository name in the pytest workflow `ghcr.io/obervinov/tools/vault:1.13.2` -> `ghcr.io/obervinov/images/vault:1.13.2`
* Fix `create PR` feature in the pr workflow


## v1.2.1 - 2024-04-28
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.2.0...v1.2.1 by @obervinov in https://github.com/obervinov/_templates/pull/75
#### 🐛 Bug Fixes
* [Workflow readme: Not working comparing version in workflow as expected](https://github.com/obervinov/_templates/issues/74)
* Change image repository name in the pytest workflow `ghcr.io/obervinov/tools/vault:1.13.2` -> `ghcr.io/obervinov/images/vault:1.13.2`
* [Workflow local _release: failed run changelog verify](https://github.com/obervinov/_templates/issues/73)


## v1.2.0 - 2024-04-28
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.1.1...v1.2.0 by @obervinov in https://github.com/obervinov/_templates/pull/71
#### 🚀 Features
* [Add a workflow to automatically create and update the PR body](https://github.com/obervinov/_templates/issues/55)
* [Close the milestone after the PR merger](https://github.com/obervinov/_templates/issues/56)
#### 📚 Documentation
* Updated all issue and pull request templates
#### 🐛 Bug Fixes
* [Verify README.md template: not working with helm mono repository](https://github.com/obervinov/_templates/issues/69)
* [Helm charts bundle template: Not working helm workflow for helm mono repository](https://github.com/obervinov/_templates/issues/68)


## v1.1.1 - 2024-04-16
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.1.0...v1.1.1 by @obervinov in https://github.com/obervinov/_templates/pull/70
#### 🚀 Features
* [Bump actions/checkout from 2 to 4 in /.github/workflows](https://github.com/obervinov/_templates/pull/70)


## v1.1.0 - 2024-04-08
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.17...v1.1.0 by @obervinov in https://github.com/obervinov/_templates/pull/66
#### 🚀 Features
* [Automatic version check of workflows in README.md](https://github.com/obervinov/_templates/issues/58)
* [Helm Template Workflow: add support OCI registry for helm workflow](https://github.com/obervinov/_templates/issues/62)
* [Docker Workflow: add workflow for mono repositories](https://github.com/obervinov/_templates/issues/64)


## v1.0.17 - 2024-04-08
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.16...v1.0.17 by @obervinov in https://github.com/obervinov/_templates/pull/65
#### 🚀 Features
* [Helm Template Workflow: helm template with values-test.yaml](https://github.com/obervinov/_templates/issues/63)
#### 🐛 Bug Fixes
* [Merge steps in wokrflow `pylint.yaml`](https://github.com/obervinov/_templates/issues/54)


## v1.0.16 - 2024-04-08
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.15...v1.0.16 by @obervinov in https://github.com/obervinov/_templates/pull/52
#### 🚀 Features
* [Bump actions/setup-python from 4 to 5 in /.github/workflows](https://github.com/obervinov/_templates/pull/52)


## v1.0.15 - 2024-04-08
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.14...v1.0.15 by @obervinov in https://github.com/obervinov/_templates/pull/53
#### 🚀 Features
* [Bump abatilo/actions-poetry from 2 to 3 in /.github/workflows](https://github.com/obervinov/_templates/pull/53)


## v1.0.14 - 2024-04-08
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.13...v1.0.14 by @obervinov in https://github.com/obervinov/_templates/pull/61
#### 🚀 Features
* [Bump aquasecurity/trivy-action from 0.16.1 to 0.19.0 in /.github/workflows](https://github.com/obervinov/_templates/pull/61)


## v1.0.13 - 2024-02-02
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.12...v1.0.13 by @obervinov in https://github.com/obervinov/_templates/pull/57
#### 🐛 Bug Fixes
* Add support for `python 3.9` to matrix builds


## v1.0.12 - 2024-01-19
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.11...v1.0.12 by @obervinov in https://github.com/obervinov/_templates/pull/47
#### 🐛 Bug Fixes
* [Fix PROJECT_DESCRIPTION variable in docker workflow #45](https://github.com/obervinov/_templates/issues/45)
* [Fix image reference `TAG` for Trivy job](https://github.com/obervinov/_templates/issues/46)
#### 🚀 Features
* [Add support environment variables `PROJECT_NAME` and `PROJECT_VERSION` for docker build](https://github.com/obervinov/_templates/issues/48)
#### 💥 Breaking Changes
* [Add poetry support for python repositories](https://github.com/obervinov/_templates/issues/49)


## v1.0.11 - 2024-01-08
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.10...v1.0.11 by @obervinov in https://github.com/obervinov/_templates/pull/37
#### 🚀 Features
* [Bump actions/checkout from 3 to 4 in /.github/workflows](https://github.com/obervinov/_templates/pull/37)


## v1.0.10 - 2024-01-08
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.9...v1.0.10 by @obervinov in https://github.com/obervinov/_templates/pull/38
#### 🚀 Features
* [Bump github/codeql-action from 2 to 3 in /.github/workflows](https://github.com/obervinov/_templates/pull/38)


## v1.0.9 - 2024-01-08
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.8...v1.0.9 by @obervinov in https://github.com/obervinov/_templates/pull/39
#### 🚀 Features
* [Bump aquasecurity/trivy-action from 0.5.0 to 0.16.1 in /.github/workflows](https://github.com/obervinov/_templates/pull/39)


## v1.0.8 - 2024-01-08
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.7...v1.0.8 by @obervinov in https://github.com/obervinov/_templates/pull/40
#### 🚀 Features
* [Bump actions/setup-python from 3 to 5 in /.github/workflows](https://github.com/obervinov/_templates/pull/40)


## v1.0.7 - 2024-01-08
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.6...v1.0.7 by @obervinov in https://github.com/obervinov/_templates/pull/42
#### 🐛 Bug Fixes
* [Fix small errors and typos](https://github.com/obervinov/_templates/issues/41)


## v1.0.7 - 2024-01-08
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.6...v1.0.7 by @obervinov in https://github.com/obervinov/_templates/pull/42
#### 🐛 Bug Fixes
* [Fix small errors and typos](https://github.com/obervinov/_templates/issues/41)


## v1.0.6 - 2024-01-08
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.5...v1.0.6 by @obervinov in https://github.com/obervinov/_templates/pull/32
#### 🐛 Bug Fixes
* [Fix typos in templates](https://github.com/obervinov/_templates/issues/27)
* [Update the outdated save state in GitHub Actions](https://github.com/obervinov/_templates/issues/34)
#### 📚 Documentation
* [Update repository map](https://github.com/obervinov/_templates/issues/30)
#### 💥 Breaking Changes
* **Renamed all workflow files**
#### 🚀 Features
* [Add support EXTRA_ARGS and PROJECT_VERSION in docker build command](https://github.com/obervinov/_templates/issues/26)
* [Terraform-docs markdown for automatic creation and updating of documents](https://github.com/obervinov/_templates/issues/29)
* [Add workflow for helm charts repository](https://github.com/obervinov/_templates/issues/31)
* [Add Dependabot for GitHub Actions](https://github.com/obervinov/_templates/issues/33)


## v1.0.5 - 2023-08-22
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.4...v1.0.5 by @obervinov in https://github.com/obervinov/_templates/pull/1
#### 📚 Documentation
* [Added mega ico to templates](https://github.com/obervinov/_templates/issues/22)
#### 🚀 Features
* [Changed ${{ github.sha }} to extract tag (or branch) from repository](https://github.com/obervinov/_templates/issues/24)
* [Added jobs for terraform modules](https://github.com/obervinov/_templates/issues/21)



## v1.0.4 - 2023-05-19
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.3...v1.0.4 by @obervinov in https://github.com/obervinov/_templates/pull/10
#### 🚀 Features
* [Add dependencies: pytest-order and pytest-ordering](https://github.com/obervinov/_templates/issues/20)
* [Add a workflow for pytest with a storage service dependency](https://github.com/obervinov/_templates/issues/19)
#### 🐛 Bug Fixes
* [Fix: typos in workflow obervinov/_templates/.github/workflows/verify.package.yml@v1.0.3](https://github.com/obervinov/_templates/issues/16)
* [Fix: add the pylint module to install in the test.pylint.yml task](https://github.com/obervinov/_templates/issues/18)
#### 💥 Breaking Changes
* changed strategy.matrix `python-version: ["3.9", "3.10"]` -> `python-version: ["3.10"]` in all workflows


## v1.0.3 - 2023-04-14
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.2...v1.1.0 by @obervinov in https://github.com/obervinov/_templates/pull/8
#### 💥 Breaking Changes
* renamed all workflow files
* renamed all job titles
* merged `test.flake8.yml` and `test.pylint.yml` in one workflow `test.pylint.yml`
#### 🚀 Features
* enabled a local `GitHub Action` to automatically `create releases`
* added a `.md` files for the correct design of the repository (`CHANGELOG.md`, `ISSUE_TEMPLATE`, `CODEOWNERS`, `pull_request_template.md` and `SECURITY.md`)
* added a new workflow `verify.package.yml` to check the package metadata and verify that my python package is installed correctly
* added a new workflow `test.yamllint.yml` for checking yaml files
* added a new workflow `verify.chnagelog.yml` for checking `CHANGELOG.md`
#### 🐛 Bug Fixes
* the entire workflow code has been redesigned



## v1.0.2 - 2023-03-24
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.1...v1.0.2
#### 🚀 Features
* added new GitHub Actions - `docker build`
* added new GitHub Actions - `trivy scan`
* added new GitHub Actions - `pylint`
by @obervinov in https://github.com/obervinov/_templates/pull/6
#### 🐛 Bug Fixes
* fixed `docker login` to ghcr
by @obervinov in https://github.com/obervinov/_templates/pull/7



## v1.0.1 - 2023-03-01
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.0...v1.0.1
#### 🐛 Bug Fixes
* updated a version mask for creating release by @obervinov in https://github.com/obervinov/_templates/pull/5



## v1.0.0 - 2023-02-24
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/commits/v1.0.0
### New Contributors
* @obervinov made their first contribution in https://github.com/obervinov/_templates/pull/1
#### 🚀 Features
* creating first templates for workflow by @obervinov in https://github.com/obervinov/_templates/pull/1
* added new actions in release.yml by @obervinov in https://github.com/obervinov/_templates/pull/2
* added version template by @obervinov in https://github.com/obervinov/_templates/pull/3
* fixed version error in .github/workflows/release.yml by @obervinov in https://github.com/obervinov/_templates/pull/4
