# shogo82148/actions-goveralls

[![test](https://github.com/shogo82148/actions-goveralls/workflows/test/badge.svg?branch=master)](https://github.com/shogo82148/actions-goveralls/actions)
[![Coverage Status](https://coveralls.io/repos/github/shogo82148/actions-goveralls/badge.svg)](https://coveralls.io/github/shogo82148/actions-goveralls)

[Coveralls](https://coveralls.io/) GitHub Action with Go integration powered by [mattn/goveralls](https://github.com/mattn/goveralls).

## SYNOPSIS

### Basic Usage

Add the following step snippet to your workflows.

```yaml
- uses: actions/checkout@v2
- uses: shogo82148/actions-goveralls@v1
  with:
    path-to-profile: profile.cov
```

### Parallel Job Example

actions-goveralls supports [Parallel Builds Webhook](https://docs.coveralls.io/parallel-build-webhook).
Here is an example of matrix builds.

```yaml
on: [push, pull_request]
jobs:

  test:
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        go: ['1.11', '1.12', '1.13']

    steps:
      - uses: actions/setup-go@v1
        with:
          go-version: ${{ matrix.go }}
      - uses: actions/checkout@v2
      - run: go test -v -coverprofile=profile.cov .

      - name: Send coverage
        uses: shogo82148/actions-goveralls@v1
        with:
          path-to-profile: profile.cov
          parallel: true

  # notifies that all test jobs are finished.
  finish:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: shogo82148/actions-goveralls@v1
        with:
          parallel-finished: true
```

### Use with Legacy GOPATH mode

If you want to use Go 1.10 or earlier, you have to set `GOPATH` environment value and the working directory.
See <https://github.com/golang/go/wiki/GOPATH> for more detail.

Here is an example for testing `example.com/owner/repo` package.

```yaml
- uses: actions/checkout@v2
  with:
    path: src/example.com/owner/repo

# run test
- run: go test
  working-directory: src/example.com/owner/repo
  env:
    GOPATH: ${{ github.workspace }}

# send coverage
- uses: shogo82148/actions-goveralls@v1
  with:
    path-to-profile: profile.cov
    working-directory: src/example.com/owner/repo
  env:
    GOPATH: ${{ github.workspace }}
```
