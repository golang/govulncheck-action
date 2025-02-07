# GitHub Action for govulncheck

This repository holds the GitHub Action for govulncheck.

[Govulncheck](https://pkg.go.dev/golang.org/x/vuln/cmd/govulncheck) provides a
low-noise, reliable way for Go users to learn about known vulnerabilities that
may affect their dependencies. See details on [Go's support for vulnerability
management](https://go.dev/blog/vuln).

The govulncheck GitHub Action is currently experimental and is under active
development.

## Using the govulncheck GitHub Action

To use the govulncheck GitHub Action add the following step to your workflow:

```yaml
- id: govulncheck
  uses: golang/govulncheck-action@v1
```

By default the govulncheck GitHub Action will run with the
[latest version of Go](https://go.dev/doc/install) and analyze all packages in
the provided Go module. Assuming you have the latest Go version installed
locally, this is equivalent to running the following on your command line:

```
$ govulncheck ./...
```

To specify a specific Go version, directory in which to run govulncheck, or
[package pattern](https://pkg.go.dev/cmd/go#hdr-Package_lists_and_patterns),
use the following syntax:

```yaml
- id: govulncheck
  uses: golang/govulncheck-action@v1
  with:
     go-version-input: <your-Go-version>
     go-package: <your-package-pattern>
```

For example, the code snippet below can be used to run govulncheck against a
repository on every push:

```yaml
on: [push]

jobs:
  govulncheck_job:
    runs-on: ubuntu-latest
    name: Run govulncheck
    steps:
      - id: govulncheck
        uses: golang/govulncheck-action@v1
        with:
           go-version-input: 1.20.6
           go-package: ./...
```

govulncheck GitHub Action accepts several other optional inputs:

```yaml
work-dir: directory in which to run govulncheck, default '.'
repo-checkout: checkout the repository, default true
check-latest: check for the latest Go version, default false
go-version-file: go.mod or go.work file specifying Go version, default ''
output-format: the format of govulncheck output ('text', 'json', or 'sarif'), default 'text'
output-file: the file to which the output is redirected, default '' (no
redirection)
```
The precedence for inputs `go-version-input`, `go-version-file`, and `check-latest`
specifying Go version is inherited from [actions/setup-go](https://github.com/actions/setup-go).

The govulncheck-action follows the exit codes of govulncheck command.
Specifying the output format 'json' or 'sarif' will return success even if
there are some vulnerabilities detected. See
[here](https://pkg.go.dev/golang.org/x/vuln/cmd/govulncheck#hdr-Exit_codes)
for more information.

When a vulnerability is found with 'text' output format, an error will be displayed for that
[GitHub job](https://docs.github.com/en/actions/using-jobs/using-jobs-in-a-workflow)
with information about the vulnerability and how to fix it. For example:

![image](https://github.com/bkessler-go/prototype-repo/assets/107496148/932a2e5c-730e-4583-90f3-edab3ca06f60)

## Contributing

Our canonical Git repository is located at
https://go.googlesource.com/govulncheck-action. There is a mirror of the
repository at https://github.com/golang/govulncheck-action. See
https://go.dev/doc/contribute.html for details on how to contribute.

## Feedback

The main issue tracker for the time repository is located at

If you want to report a bug or have a feature suggestion, please file an issue
at https://github.com/golang/go/issues, prefixed with `govulncheck-action:` in the title.

## License

Unless otherwise noted, the Go source files are distributed under the BSD-style
license found in the [LICENSE](LICENSE) file.
