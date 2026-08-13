<!-- markdownlint-disable -->

# Hardening Report: Azure--k8s-set-context/v5.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Azure--k8s-set-context/v5.0.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### missing-permissions (severity: medium)

Four workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, workflows inherit the default repository permissions (which may be broad). Each of these files should declare minimal required permissions at the top level or per job.

Locations:

- `.github/workflows/default-labels.yml:1`
- `.github/workflows/integration-tests.yml:1`
- `.github/workflows/prettify-code.yml:1`
- `.github/workflows/unit-tests.yml:1`

### hardcoded-credentials (severity: high)

A literal password value `password: some-password` is hardcoded in the kubeconfig input block of the integration-tests workflow. Even though this appears to be a fake test credential, hardcoding literal passwords in workflow files is a security anti-pattern that can be mistakenly reused or serve as a template for real credentials. The value matches the pattern `password:\s*[A-Za-z0-9...]{7,}`.

Locations:

- `.github/workflows/integration-tests.yml:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** missing-permissions, hardcoded-credentials

**Notes:**

1. Added `permissions:` blocks to all 4 workflow files: default-labels.yml gets `issues: write` and `pull-requests: write` (required by the stale action); integration-tests.yml, prettify-code.yml, and unit-tests.yml each get `contents: read` (minimum needed for checkout). 2. Replaced the hardcoded literal `password: some-password` with `${{ secrets.INTEGRATION_TEST_PASSWORD }}` in both occurrences within integration-tests.yml — once in the kubeconfig input block and once in the EXPECTED_KC env block used for result verification.

