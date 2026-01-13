# HAIEC Scan Action

> **Version**: 1.0.0  
> **Repository**: `haiec/scan-action`  
> **License**: MIT

Deterministic AI security scanning with AST analysis, policy gates, and SARIF export.

---

## Security Notice

This action is designed for **auditor-safe, deterministic security scanning**:

- All inputs are validated before use
- Outputs are sanitized (no secrets logged)
- Results are reproducible (same input → same output)
- Content hashes provided for verification

---

## Versioning

| Tag | Description | Recommendation |
|-----|-------------|----------------|
| `@v1` | Latest stable v1.x | **Recommended** |
| `@v1.0.0` | Specific version | Auditor requirement |
| `@main` | Bleeding edge | Not for production |

**For auditors**: Always pin to a specific version (e.g., `@v1.0.0`) and verify the content hash in outputs.

---

## Quick Start

```yaml
- uses: haiec/scan-action@v1
  with:
    fail-on-critical: 'true'
```

---

## Full Example

```yaml
name: Security Scan

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - uses: haiec/scan-action@v1
        id: scan
        with:
          fail-on-critical: 'true'
          fail-on-high: 'false'
          upload-sarif: 'true'
          python-sidecar: 'true'
          go-sidecar: 'true'
          js-sidecar: 'true'
      
      - name: Check results
        run: |
          echo "Status: ${{ steps.scan.outputs.status }}"
          echo "Findings: ${{ steps.scan.outputs.findings-total }}"
          echo "Critical: ${{ steps.scan.outputs.findings-critical }}"
          echo "Hash: ${{ steps.scan.outputs.content-hash }}"
```

---

## Inputs

### Policy Configuration

| Input | Description | Default |
|-------|-------------|---------|
| `policy-file` | Path to custom policy JSON | `''` |
| `fail-on-critical` | Fail on critical findings | `'true'` |
| `fail-on-high` | Fail on high findings | `'false'` |
| `fail-on-findings` | Fail on any findings | `'false'` |

### Sidecar Configuration

| Input | Description | Default |
|-------|-------------|---------|
| `python-sidecar` | Enable Python AST analysis | `'true'` |
| `go-sidecar` | Enable Go AST analysis | `'true'` |
| `js-sidecar` | Enable JS/TS AST analysis | `'true'` |

### Output Configuration

| Input | Description | Default |
|-------|-------------|---------|
| `upload-sarif` | Upload to GitHub Code Scanning | `'true'` |
| `output-dir` | Directory for artifacts | `'haiec-results'` |

### Incremental Scanning

| Input | Description | Default |
|-------|-------------|---------|
| `incremental` | Enable incremental mode | `'auto'` |
| `base-ref` | Base ref for diff | Auto-detected |

### Advanced

| Input | Description | Default |
|-------|-------------|---------|
| `timeout-minutes` | Scan timeout | `'10'` |
| `working-directory` | Working directory | `'.'` |

---

## Outputs

| Output | Description |
|--------|-------------|
| `status` | Policy status: `PASS`, `WARN`, `FAIL` |
| `exit-code` | Exit code: `0` (pass), `1` (fail) |
| `findings-total` | Total findings count |
| `findings-critical` | Critical findings count |
| `findings-high` | High findings count |
| `sarif-file` | Path to SARIF file |
| `bundle-file` | Path to evidence bundle |
| `content-hash` | Deterministic content hash |

---

## Custom Policy

Create `.haiec/policy.json`:

```json
{
  "version": "1.0.0",
  "name": "My Policy",
  "gates": [
    {
      "id": "critical-findings",
      "name": "No Critical Findings",
      "blocking": true,
      "config": { "maxCritical": 0 }
    },
    {
      "id": "high-findings",
      "name": "High Findings Limit",
      "blocking": false,
      "config": { "maxHigh": 5 }
    }
  ]
}
```

### Available Gates

| Gate ID | Description | Config |
|---------|-------------|--------|
| `critical-findings` | Limit critical findings | `maxCritical` |
| `high-findings` | Limit high findings | `maxHigh` |
| `sidecar-success` | Sidecar success rate | `minSuccessRate` |
| `ast-coverage` | AST coverage % | `minAstPercentage` |
| `no-new-findings` | Block new findings | `maxNew` |

---

## Artifacts

The action produces:

| File | Description |
|------|-------------|
| `evidence-bundle.json` | Deterministic scan results |
| `haiec-scan.sarif` | SARIF v2.1.0 report |
| `result.json` | Policy evaluation result |

---

## Auditor Verification

For compliance audits, verify scan reproducibility:

```bash
# 1. Note the content hash from the action output
EXPECTED_HASH="sha256:abc123..."

# 2. Re-run scan locally
npx @haiec/scanner --output ./verify

# 3. Compare hashes
ACTUAL_HASH=$(jq -r '.content_hash' ./verify/evidence-bundle.json)

if [ "$EXPECTED_HASH" = "$ACTUAL_HASH" ]; then
  echo "✓ Scan is reproducible"
else
  echo "✗ Hash mismatch - investigate"
fi
```

---

## Security

### What This Action Does

- Analyzes source code using AST parsers
- Detects security vulnerabilities
- Generates deterministic evidence bundles
- Uploads SARIF to GitHub Code Scanning

### What This Action Does NOT Do

- Access external networks (except GitHub API)
- Store or transmit source code
- Use AI/ML for detection (deterministic rules only)
- Modify repository contents

### Permissions Required

```yaml
permissions:
  contents: read
  security-events: write  # For SARIF upload
```

---

## Support

- **Documentation**: https://docs.haiec.com/ci
- **Issues**: https://github.com/haiec/scan-action/issues
- **Security**: security@haiec.com

---

## License

MIT License - See [LICENSE](LICENSE)
