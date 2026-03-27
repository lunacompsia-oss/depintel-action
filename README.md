# DepIntel Action

Automatically analyze dependency changes in your pull requests for **vulnerabilities**, **license risks**, and **breaking changes**.

Powered by the [DepIntel API](https://depintel-api.pingbase-api.workers.dev) — free dependency intelligence.

## What it does

When a PR modifies `package.json`, this action:

1. Detects which dependencies were added or changed
2. Checks each dependency for known vulnerabilities (via [OSV.dev](https://osv.dev))
3. Analyzes license type and risk level
4. Posts a summary comment on the PR

## Usage

```yaml
name: Dependency Check
on:
  pull_request:
    paths:
      - '**/package.json'

jobs:
  depintel:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
      contents: read
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # needed for diff
      - uses: lunacompsia-oss/depintel-action@v1
```

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `github-token` | Token for PR comments | `${{ github.token }}` |
| `api-url` | DepIntel API URL | `https://depintel-api.pingbase-api.workers.dev` |
| `fail-on-critical` | Fail if vulnerabilities found | `false` |

## Outputs

| Output | Description |
|--------|-------------|
| `vulnerabilities-found` | Number of vulnerabilities across changed deps |
| `report` | Path to full JSON report |

## Example PR Comment

| Metric | Value |
|--------|-------|
| Dependencies analyzed | **3** |
| Vulnerabilities found | **2** |
| High-risk licenses | **0** |

| Package | License | Risk | Vulnerabilities |
|---------|---------|------|-----------------|
| `express` | MIT | :white_check_mark: low | :white_check_mark: 0 |
| `lodash` | MIT | :white_check_mark: low | :warning: 2 |

## Advanced: Fail on vulnerabilities

```yaml
- uses: lunacompsia-oss/depintel-action@v1
  with:
    fail-on-critical: 'true'
```

## License

MIT
