# DepIntel Action

Automatically analyze dependency changes in your pull requests for **vulnerabilities**, **license risks**, and **breaking changes**.

Supports **npm** (package.json) and **Python** (requirements.txt, pyproject.toml).

Powered by the [DepIntel API](https://depintel-api.pingbase-api.workers.dev) — free dependency intelligence.

## What it does

When a PR modifies dependency files (`package.json`, `requirements.txt`, or `pyproject.toml`), this action:

1. Detects which dependencies were added or changed
2. Checks each dependency for known vulnerabilities (via [OSV.dev](https://osv.dev))
3. Analyzes license type and risk level
4. Posts a summary comment on the PR

## Supported Ecosystems

| Ecosystem | Files Detected | Registry |
|-----------|---------------|----------|
| **npm** | `package.json` | npmjs.org |
| **PyPI** | `requirements.txt`, `requirements-*.txt`, `pyproject.toml` | pypi.org |

## Usage

```yaml
name: Dependency Check
on:
  pull_request:
    paths:
      - '**/package.json'
      - '**/requirements*.txt'
      - '**/pyproject.toml'

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

| Package | Ecosystem | License | Risk | Vulnerabilities |
|---------|-----------|---------|------|-----------------|
| `express` | npm | MIT | :white_check_mark: low | :white_check_mark: 0 |
| `lodash` | npm | MIT | :white_check_mark: low | :warning: 2 |
| `requests` | PyPI | Apache-2.0 | :white_check_mark: low | :warning: 13 |

## Advanced: Fail on vulnerabilities

```yaml
- uses: lunacompsia-oss/depintel-action@v1
  with:
    fail-on-critical: 'true'
```

## Badge

Add a "Scanned by DepIntel" badge to your README:

```md
![DepIntel](https://depintel-api.pingbase-api.workers.dev/badge)
```

Status-specific badges are also available:

```md
![DepIntel Passing](https://depintel-api.pingbase-api.workers.dev/badge/passing)
![DepIntel Failing](https://depintel-api.pingbase-api.workers.dev/badge/failing)
![DepIntel Unknown](https://depintel-api.pingbase-api.workers.dev/badge/unknown)
```

## License

MIT
