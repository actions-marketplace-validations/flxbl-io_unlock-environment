# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## 1.0.0 (2026-01-12)


### Features

* add manual trigger to CI workflow ([1dfaef5](https://github.com/flxbl-io/unlock-environment/commit/1dfaef58035f62a6575a16e51fafe98eacd59742))
* initial release of unlock-environment action ([067eb0f](https://github.com/flxbl-io/unlock-environment/commit/067eb0fa9ac7f58c2b10a4944fb53da6ba94088a))


### Bug Fixes

* use explicit flags for sfp server unlock command ([9b0796a](https://github.com/flxbl-io/unlock-environment/commit/9b0796adf85e1a603bb761d4b7bb0808e2df8eef))
* use GHA_TOKEN for release-please ([8d4aa86](https://github.com/flxbl-io/unlock-environment/commit/8d4aa86e9cdc5b949f169d9aa3174f0c6c648010))

## [Unreleased]

## [1.0.0] - 2025-01-10

### Added

- Initial release as standalone Marketplace action
- Unlock environment via SFP Server
- Non-blocking design - never fails the pipeline
- Aligned header with sfp-pro CLI style

### Features

- **Safe Cleanup**: Designed to never fail, safe for `if: always()` steps
- **SFP Server Integration**: Uses `sfp server environment unlock` command
- **Repository Support**: Supports custom repository for multi-repo setups

[Unreleased]: https://github.com/flxbl-io/unlock-environment/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/flxbl-io/unlock-environment/releases/tag/v1.0.0
