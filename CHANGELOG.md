# Change Log
All notable changes to this project will be documented in this file.
The format is based on [Keep a Changelog](http://keepachangelog.com/) and this project adheres to [Semantic Versioning](http://semver.org/).



## v1.1.0 - 2023-04-06
### What's Changed
**Full Changelog**: https://github.com/obervinov/_templates/compare/v1.0.2...v1.1.0 by @obervinov in https://github.com/obervinov/_templates/pull/8
#### 🚀 Features
* enabled a local `GitHub Action` to automatically create releases
* added a `.md` files for the correct design of the repository (`CHANGELOG.md`, `ISSUE_TEMPLATE`, `CODEOWNERS`, `pull_request_template.md` and `SECURITY.md`)
* added a new workflow `package.yml` to test the correct installation of my python modules
* added a new workflow `yamllint.yml` for checking yaml files
* added a new stage in the wokflow `version.yml` to check the correctness of the date in `CHANGELOG.md`



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
