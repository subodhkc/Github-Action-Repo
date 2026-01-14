# Changelog

All notable changes to the HAIEC Scan Action will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-01-14

### Added

- Initial release of HAIEC Scan Action
- **Zero-dependency architecture** - No NPM required
- Multi-language AST analysis (Python, Go, JavaScript/TypeScript)
- Policy gates with configurable thresholds
- SARIF v2.1.0 export for GitHub Code Scanning
- Deterministic evidence bundle generation
- Incremental scanning for pull requests
- Content hash for audit verification
- API-based scanning fallback when binary unavailable

### Security

- Input validation for all parameters
- No external network access (except GitHub API and HAIEC API)
- No source code transmission
- Minimal permissions required
- Pre-built binaries with checksum verification

## [Unreleased]

### Planned

- Java sidecar support
- Rust sidecar support
- Custom rule definitions
- Baseline management
