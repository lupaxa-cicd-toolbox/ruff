<p align="center">
    <a href="https://github.com/lupaxa-cicd-toolbox">
        <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/organisations/cicd-toolbox/readme-logo.png" alt="Organisation Logo" />
    </a>
</p>

<h1 align="center">ruff</h1>

## Overview

A tool to lint Python code with [Ruff](https://docs.astral.sh/ruff/) (`ruff check`).

This tool has been tested against the following:

1. GitHub Actions
2. Travis CI
3. CircleCI
4. BitBucket pipelines
5. Local command line

Because it is a plain Bash script, it should work on most CI platforms where you can run arbitrary commands.

## Basic Usage

### GitHub Actions

```yaml
on: [push, pull_request]

jobs:
  build:
    name: Ruff
    runs-on: ubuntu-latest

    steps:
      - name: Checkout the Repository
        uses: actions/checkout@v4
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - name: Run Ruff
        run: bash <(curl -s https://raw.githubusercontent.com/lupaxa-cicd-toolbox/ruff/master/src/pipeline.sh)
```

### Local

```bash
./src/pipeline.sh
```

Or without cloning:

```bash
bash <(curl -s https://raw.githubusercontent.com/lupaxa-cicd-toolbox/ruff/master/src/pipeline.sh)
```

## Configuration Options

The following environment variables customise the script:

| Variable         | Default        | Purpose                                            |
| ---------------- | -------------- | -------------------------------------------------- |
| `INCLUDE_FILES`  | `(empty)`      | Comma-separated path regexes to force-include      |
| `EXCLUDE_FILES`  | `(empty)`      | Comma-separated path regexes to skip               |
| `NO_COLOR`       | `false`        | Disable colour output                              |
| `REPORT_ONLY`    | `false`        | Report results but always exit 0                   |
| `SHOW_ERRORS`    | `true`         | Show detailed errors for failed files              |
| `SHOW_FILTERED`  | `false`        | Show files skipped by exclude rules                |
| `SHOW_UNMATCHED` | `false`        | Show files that matched neither pattern            |
| `SCAN_ROOT`      | script default | Override scan directory without editing the script |

> If you set `INCLUDE_FILES`, only matching paths are scanned (everything else is skipped, including paths that would match `EXCLUDE_FILES`).

You can combine any of the settings above:

```yaml
on: [push, pull_request]

jobs:
  build:
    name: Ruff
    runs-on: ubuntu-latest

    steps:
      - name: Checkout the Repository
        uses: actions/checkout@v4
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - name: Run Ruff
        env:
          REPORT_ONLY: true
          SHOW_ERRORS: true
        run: bash <(curl -s https://raw.githubusercontent.com/lupaxa-cicd-toolbox/ruff/master/src/pipeline.sh)
```

## Example Output

Example shape of a successful run (details vary by tool version):

```text
--------------------------------------------------------------------- Stage 1: Parameters --
 No parameters given
---------------------------------------------------------- Stage 2: Install Prerequisites --
 [ OK ] ruff is already installed
------------------------------------------------------------- Stage 3: Run ruff (v0.9.0) --
 [ ✅ ] path/to/file
------------------------------------------------------------------------- Stage 4: Report --
 Total: 1 Passed: 1 Failed: 0 Filtered: 0 Unmatched: 0
----------------------------------------------------------------------- Stage 5: Complete --
```

## File Identification

Files are identified using the following checks:

```shell
file -b "${filename}" | grep -qE '^Python script'
```

and / or name match:

```shell
[[ ${filename} =~ \.py$ ]]
```

<a href="https://github.com/the-lupaxa-project">
    <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/components/footer-for-child-orgs.svg" alt="The Lupaxa Project Footer" width="100%" />
</a>
