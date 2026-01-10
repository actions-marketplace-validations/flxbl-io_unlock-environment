# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
