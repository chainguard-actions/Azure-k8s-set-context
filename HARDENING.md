<!-- markdownlint-disable -->

# Hardening Report: Azure--k8s-set-context/v4.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Azure--k8s-set-context/v4.0.0** was hardened automatically. 11 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Workflow uses action references pinned to mutable tags or branches instead of immutable full-length commit SHAs, making the workflow vulnerable to supply-chain attacks. Failing references: `actions/stale@v3` (lines 16, 26).

Locations:

- `.github/workflows/default-labels.yml:16`
- `.github/workflows/default-labels.yml:26`

### unpinned-uses (severity: high)

Workflow uses action references pinned to mutable tags instead of immutable full-length commit SHAs. Failing reference: `actions/checkout@v2` (line 17).

Locations:

- `.github/workflows/integration-tests.yml:17`

### unpinned-uses (severity: high)

Workflow uses action references pinned to mutable tags instead of immutable full-length commit SHAs. Failing references: `actions/checkout@v2` (line 11), `actionsx/prettier@v2` (line 14).

Locations:

- `.github/workflows/prettify-code.yml:11`
- `.github/workflows/prettify-code.yml:14`

### unpinned-uses (severity: high)

Workflow uses a reusable workflow reference pinned to the mutable branch `main` instead of an immutable full-length commit SHA. Failing reference: `OliverMKing/javascript-release-workflow/.github/workflows/tag-and-release.yml@main` (line 10).

Locations:

- `.github/workflows/tag-and-draft.yml:10`

### unpinned-uses (severity: high)

Workflow uses an action reference pinned to a mutable tag instead of an immutable full-length commit SHA. Failing reference: `actions/checkout@v1` (line 15).

Locations:

- `.github/workflows/unit-tests.yml:15`

### missing-permissions (severity: medium)

Workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

Locations:

- `.github/workflows/default-labels.yml:1`

### missing-permissions (severity: medium)

Workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

Locations:

- `.github/workflows/integration-tests.yml:1`

### missing-permissions (severity: medium)

Workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

Locations:

- `.github/workflows/prettify-code.yml:1`

### missing-permissions (severity: medium)

Workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

Locations:

- `.github/workflows/tag-and-draft.yml:1`

### missing-permissions (severity: medium)

Workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

Locations:

- `.github/workflows/unit-tests.yml:1`

### hardcoded-credentials (severity: high)

A literal value is assigned to a key named `password` inside an inline kubeconfig YAML block used as test input to the action: `password: some-password`. Even though this appears to be a placeholder, hardcoded credential-named fields with literal values should use secrets references instead.

Locations:

- `.github/workflows/integration-tests.yml:55`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, hardcoded-credentials

**Notes:**

Fixed all 5 workflow files:

1. default-labels.yml: Pinned both `actions/stale@v3` references to SHA `98ed4cb500039dbcccf4bd9bedada4d0187f2757`. Added `permissions: { issues: write, pull-requests: write }` (needed for stale action to label issues/PRs).

2. integration-tests.yml: Pinned `actions/checkout@v2` to SHA `ee0669bd1cc54295c223e0bb666b733df41de1c5`. Added `permissions: { contents: read }`. Replaced hardcoded `password: some-password` with `password: ${{ secrets.KUBECONFIG_TEST_PASSWORD }}`.

3. prettify-code.yml: Pinned `actions/checkout@v2` to SHA `ee0669bd1cc54295c223e0bb666b733df41de1c5` and `actionsx/prettier@v2` to SHA `8b6d14bd1241e743fa9a6112b43d691d1890a8e9`. Added `permissions: { contents: read }`.

4. tag-and-draft.yml: Pinned reusable workflow `OliverMKing/javascript-release-workflow/.github/workflows/tag-and-release.yml@main` to SHA `a2f171c6ca04fea2de31f4dbb6dc576140395512`. Added `permissions: { contents: write }` (needed for creating releases/tags).

5. unit-tests.yml: Pinned `actions/checkout@v1` to SHA `50fbc622fc4ef5163becd7fab6573eac35f8462e`. Added `permissions: { contents: read }`.

### Iteration 2

**Fixes applied:** hardcoded-credentials

**Notes:**

Removed the hardcoded literal `"some-password"` from the EXPECTED_KC env var in `.github/workflows/integration-tests.yml`. The fix: (1) adds `KUBECONFIG_TEST_PASSWORD: ${{ secrets.KUBECONFIG_TEST_PASSWORD }}` to the step's env block, (2) replaces the hardcoded password in the JSON template with an empty string placeholder, and (3) updates the run command to use `jq --arg pw "$KUBECONFIG_TEST_PASSWORD"` to inject the secret value into the expected JSON at runtime before the diff comparison. This eliminates the hardcoded credential while preserving the test's correctness.

