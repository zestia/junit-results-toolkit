# JUnit Results Toolkit

GitHub action that parses JUnit XML reports, outputs a JSON summary and (optionally) generates an HTML report.

## Usage

To parse test results in JUnit XML format add this action as a step:

```yaml
jobs:
  java-build:
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          java-version: 11

      - name: Run unit tests
        run: sbt test

      - name: Parse Test Results
        uses: zestia/junit-results-toolkit@v2
        id: test-results
        if: ${{ always() }}
        with:
          files: '**/TEST-*.xml'

      - name: Echo Test Results
        run: |
          echo "Test Results:"
          echo "  Passed:  ${{ fromJson(steps.test-results.outputs.test.results).passed }}"
          echo "  Failed:  ${{ fromJson(steps.test-results.outputs.test.results).failed }}"
          echo "  Skipped: ${{ fromJson(steps.test-results.outputs.test.results).skipped }}"
```

**Note:** it is important to add an `if` clause to ensure that the test results are always parsed.

## Options

files:
description: Glob pattern to match JUnit files required: true upload-report:
description: If true then upload reports in HTML & JSON formats required: false default: 'true' artifact-name:
description: Name of the artifact to store reports in required: false retention-days:
description: Number of days to retain report artifact for required: false

| Name             | Description                                                                     | Default                               |
| ---------------- | ------------------------------------------------------------------------------- | ------------------------------------- |
| `files`          | Glob pattern to match JUnit XML files.                                          | _none_ (required)                     |
| `upload-report`  | If `true` then an HTML report will be generated & uploaded to `$artifact-name`. | `true`                                |
| `artifact-name`  | Name of the artifact to use when uploading HTML report.                         | `test-report-${context.job}`          |
| `retention-days` | Number of days to retain HTML report artifact.                                  | Repository default (usually 90 days). |

## Outputs

### `test-results`

Summary of test results in JSON format.

For example:

```json
{
  "name": "java-build",
  "summary": {
    "startTime": 1648128401000,
    "duration": 0.032,
    "tests": 3,
    "passed": 2,
    "failed": 1,
    "skipped": 0
  },
  "suites": [
    {
      "name": "com.example.WidgetTest",
      "startTime": 1648128437000,
      "duration": 0.011,
      "tests": 1,
      "passed": 1,
      "failed": 0,
      "skipped": 0
    },
    {
      "name": "com.example.GadgetTest",
      "startTime": 1648128437000,
      "duration": 0.017,
      "tests": 1,
      "passed": 1,
      "failed": 0,
      "skipped": 0
    },
    {
      "name": "com.example.FlangeTest",
      "startTime": 1648128401000,
      "duration": 0.004,
      "tests": 1,
      "passed": 0,
      "failed": 1,
      "skipped": 0
    }
  ]
}
```
