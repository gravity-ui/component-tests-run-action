# component-tests-run-action

A GitHub action that runs Playwright component tests and stores report into workflow artifacts.

## ⚠️ Important: Playwright Container Required

**This action must be run inside a Playwright Docker container.** The container provides the necessary browser binaries and dependencies for running Playwright tests.

The version of the container **must** match Playwright version used in project

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `pr` | PR id used for creating unique URL | No | Auto-detected from workflow event |
| `node-version` | Node.js version used for running tests | No | - |
| `node-version-file` | Path to Node.js version file for determining the version to use | No | - |
| `command` | Command to run tests | No | `npx --no-install playwright test` |
| `report-path` | Path to Playwright report directory | No | `playwright-report` |

Note: node version determinition rules are same with setup-node action. See [setup-node docs](https://github.com/actions/setup-node?tab=readme-ov-file#usage) for more info

## Outputs

This action uploads the following artifacts:
- **`playwright-report`** - The Playwright test report (HTML and traces)
- **`pr`** - A file containing the PR number (useful for subsequent workflow jobs)

## Usage Example

```yaml
name: Component Tests

on:
  pull_request:

jobs:
  test:
    name: Run Component Tests
    runs-on: ubuntu-latest
    container:
      image: mcr.microsoft.com/playwright:v1.55.0-jammy
    steps:
      - uses: gravity-ui/component-tests-run-action@v1
        with:
          pr: ${{ github.event.pull_request.number }}
          command: 'npx playwright test'
```

## Advanced Usage

### Custom Playwright Version

Use a specific Playwright container version:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    container:
      image: mcr.microsoft.com/playwright:v1.55.0-jammy
    steps:
      - uses: gravity-ui/component-tests-run-action@v1
```

### Custom Report Path

If your Playwright config outputs reports to a custom location:

```yaml
- uses: gravity-ui/component-tests-run-action@v1
  with:
    report-path: 'test-results/playwright-report'
```

### Custom Test Command

Run specific tests or use custom Playwright commands:

```yaml
- uses: gravity-ui/component-tests-run-action@v1
  with:
    command: 'npm run test:component -- --project=chromium'
```

### Custom Node Version

Specify a different Node.js version:

```yaml
- uses: gravity-ui/component-tests-run-action@v1
  with:
    node-version: '20'
```

### Custom Node Version File

Specify a different Node.js version:

```yaml
- uses: gravity-ui/component-tests-run-action@v1
  with:
    node-version-file: '.nvmrc'
```