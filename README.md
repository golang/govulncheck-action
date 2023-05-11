# GitHub Action for govulncheck

This repository holds the GitHub Action for govulncheck. Govulncheck reports known vulnerabilities that affect Go code. It uses static analysis of source code or a binary's symbol table to narrow down reports to only those that could affect the application. You can read more about govulncheck at https://pkg.go.dev/golang.org/x/vuln/cmd/govulncheck.

The govulncheck GitHub Action is currently experimental and is under active development.

## Using the govulncheck GitHub Action

To use the govulncheck GitHub Action add the following step to your workflow:

```yaml
- id: govulncheck
  uses: golang/govulncheck-action@v1
```

By default the govulncheck Github Action will run with the latest version of Go using the ./... package path:

```govulncheck ./...```

If you would like to specify a specific version of Go to use or a different package path to run govulncheck against then you can do so by adding the following step to your workflow:

```yaml
- id: govulncheck
  uses: golang/govulncheck-action@v1
  with:
     go-version-input: 1.XX
     go-package: ./...
```

Below is a full example of a workflow that runs govulncheck against a simple repository on every push:

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
           go-version-input: 1.20.3
```
When this workflow finds a vulnerability you will see an error in the Run govulncheck job like the one below. The output contains information about the vulnerability and how to fix it:

![image](https://github.com/bkessler-go/prototype-repo/assets/107496148/932a2e5c-730e-4583-90f3-edab3ca06f60)

## Report Issues / Send Patches

This repository uses Gerrit for code changes. To learn how to submit changes to
this repository, see https://go.dev/doc/contribute.html.

The main issue tracker for the time repository is located at
https://github.com/golang/go/issues. Prefix your issue with "x/govulncheck-action:" in the
subject line, so it is easy to find.
