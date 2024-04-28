# Change Log
All notable changes to this project will be documented in this file.
The format is based on [Keep a Changelog](http://keepachangelog.com/) and this project adheres to [Semantic Versioning](http://semver.org/).


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
