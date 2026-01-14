# Security Policy

## Supported Versions

| Version | Supported          |
| ------- | ------------------ |
| 1.x.x   | :white_check_mark: |
| < 1.0   | :x:                |

## Reporting a Vulnerability

If you discover a security vulnerability in the HAIEC Scan Action, please report it responsibly:

1. **DO NOT** open a public GitHub issue
2. Email security@haiec.com with:
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact
   - Any suggested fixes

We will respond within 48 hours and work with you to:
- Confirm the vulnerability
- Develop a fix
- Coordinate disclosure

## Security Design Principles

### Determinism

All scans produce deterministic results:
- Same input → Same output
- Content hashes verify reproducibility
- No AI/ML probabilistic detection

### Minimal Permissions

The action requires only:
- `contents: read` - Read source code
- `security-events: write` - Upload SARIF (optional)

### No Data Exfiltration

- Source code is never transmitted externally
- Results stay within GitHub Actions
- No telemetry or analytics

### Input Validation

All inputs are validated:
- Policy files checked for valid JSON
- Paths sanitized
- Timeouts bounded

### Audit Trail

Every scan produces:
- Evidence bundle with content hash
- SARIF report for GitHub
- Timestamped artifacts

## Verification

Auditors can verify scan integrity:

```bash
# Compare content hashes
jq -r '.content_hash' evidence-bundle.json
```

## Contact

- Security issues: security@haiec.com
- General support: support@haiec.com
