# catho-go-auto-coverage-composite-action
A custom action to use in go applications.

## Why
This composite can be used to comment on the results of coverage tests in the Pull Requests of Go applications in an automated way. The comment is created when making a pull request and updated whenever a push is made.

## Features
 - use `actions/github-script@v4`;

## Inputs
 - `github-token`: Used to comment on pull request
 - `coverage-file-name`: File name that contains tests results by packaging 
 - `total-coverage-file-name`: File name that contains total tests results

## Outputs
Comment on current pull request

## How to use

```yaml
name: Some API

on: [workflow_dispatch, pull_request]

jobs:
  coverage:
    timeout-minutes: 10
    runs-on: ubuntu-latest
    strategy:
      matrix:
        go-version: [1.19.x]

    steps:
      - uses: actions/checkout@v2
      - name: Use GoLang ${{ matrix.go-version }}
        uses: actions/setup-go@v2
        with:
            go-version: ${{ matrix.go-version }}

      - name: Install dependencies
        run: git config --global url."https://${{ your_github_token }}:x-oauth-basic@github.com/catho".insteadOf "https://github.com/catho" && make vendor

      - name: Run all tests coverage
        run: go test -coverprofile=coverage.out ./... > coverage.txt && go tool cover -func=coverage.out | grep total > coverage-total.txt

      - name: Upload detailed coverage report
        uses: actions/upload-artifact@v2
        with:
          name: coverage-report
          path: coverage.txt
      
      - name: Upload total coverage 
        uses: actions/upload-artifact@v2
        with:
          name: total-coverage-report
          path: coverage-total.txt

      - name: Run auto coverage
        uses: catho/catho-go-auto-coverage-composite-action@v1
        with: 
          github-token: ${{ your_github_token }}
          coverage-file-name: "coverage.txt"
          total-coverage-file-name: "coverage-total.txt"
```