# Change Log
All notable changes to this project will be documented in this file.
The format is based on [Keep a Changelog](http://keepachangelog.com/) and this project adheres to [Semantic Versioning](http://semver.org/).


## v1.0.4 - 2023-05-19
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.3...v1.0.4 by @obervinov in https://github.com/obervinov/_templates/pull/10
#### 🚀 Features
* https://github.com/obervinov/_templates/issues/20
* https://github.com/obervinov/_templates/issues/19
#### 🐛 Bug Fixes
* https://github.com/obervinov/_templates/issues/16
* https://github.com/obervinov/_templates/issues/18
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
