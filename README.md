# GitHub Action for govulncheck

[Govulncheck](https://pkg.go.dev/golang.org/x/vuln/cmd/govulncheck) provides a low-noise, reliable way for Go users to learn about known vulnerabilities that may affect their dependencies.
See details on [Go's support for vulnerability management](https://go.dev/blog/vuln).

## Usage

To use the govulncheck GitHub Action add the following step to your workflow:

  ```yaml
  - id: govulncheck
    uses: nicholas-fedor/govulncheck-action@master
  ```

By default the govulncheck Github Action will run with the [latest version of Go](https://go.dev/doc/install) and analyze all packages in the provided Go module.

To run `govulncheck` locally:

1. Install govulncheck:

    ```bash
    go install golang.org/x/vuln/cmd/govulncheck@latest
    ```

2. Run `govulncheck` from your project's root directory:

    ```bash
    govulncheck ./...
    ```

## Configuration

Optional inputs:

  ```yaml
  # Version of Go to use for govulncheck
  go-version-input: # default: "stable"

  # Check for the latest Go version
  check-latest: # default: true

  # Specify whether Go caching is needed
  cache: # default: true

  # Go package to scan with govulncheck
  go-package: # default: "./..."

  # Directory in which to run govulncheck
  work-dir: # default: "."

  # Checkout the repository
  repo-checkout: # default: true

  # Path to the go.mod or go.work file specifying the Go version
  go-version-file: # no default

  # Output format ("text", "json", or "sarif")
  output-format: # default: "text"

  # Output filepath
  output-file: # default: ""
  ```

Example GitHub workflow that uploads results to [CodeQL](https://docs.github.com/en/code-security/code-scanning/introduction-to-code-scanning/about-code-scanning-with-codeql):

```yaml
---
name: Run govulncheck
on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

permissions:
  contents: read
  actions: read
  pull-requests: read
  security-events: write

env:
  GO_VERSION: 1.24.x
  OUTPUT_FILE: results.sarif


jobs:
  govulncheck:
    name: govulncheck
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Set up Go
        uses: actions/setup-go@v5
        with:
          go-version: ${{ env.GO_VERSION }}

      - id: govulncheck
        uses: nicholas-fedor/govulncheck-action@master
        with:
          output-format: sarif
          output-file: ${{ env.OUTPUT_FILE }}
          go-version-input: ${{ env.GO_VERSION }}

      - name: Upload SARIF file
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: ${{ env.OUTPUT_FILE }}
```

The precedence for specifying the Go version via the inputs `go-version-input`, `go-version-file`, and `check-latest` is inherited from [actions/setup-go](https://github.com/actions/setup-go).

The govulncheck-action follows the exit codes of govulncheck command.
Specifying the output format 'json' or 'sarif' will return success even if there are some vulnerabilities detected. See [here](https://pkg.go.dev/golang.org/x/vuln/cmd/govulncheck#hdr-Exit_codes)
for more information.

When a vulnerability is found with 'text' output format, an error will be displayed for that [GitHub job](https://docs.github.com/en/actions/using-jobs/using-jobs-in-a-workflow) with information about the vulnerability and how to fix it.

For example:

![image](https://github.com/bkessler-go/prototype-repo/assets/107496148/932a2e5c-730e-4583-90f3-edab3ca06f60)

## Contributing

Our canonical Git repository is located at <https://go.googlesource.com/govulncheck-action>.
There is a mirror of the repository at <https://github.com/golang/govulncheck-action>.
See <https://go.dev/doc/contribute.html> for details on how to contribute.

## Feedback

If you want to report a bug or have a feature suggestion, please file an issue at <https://github.com/golang/go/issues>.
Please, use the prefix `govulncheck-action:` in the issue title.

## License

Unless otherwise noted, the Go source files are distributed under the BSD-style license found in the [LICENSE](LICENSE) file.
