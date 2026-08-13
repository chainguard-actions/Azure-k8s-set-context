<!-- markdownlint-disable -->

# Hardening Report: Azure--k8s-set-context/v4.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Azure--k8s-set-context/v4.0.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of full 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the referenced tag is moved or the branch is updated maliciously.

Failing references:
- .github/workflows/default-labels.yml: `actions/stale@v3` (×2)
- .github/workflows/integration-tests.yml: `actions/checkout@v2`
- .github/workflows/prettify-code.yml: `actions/checkout@v2`, `actionsx/prettier@v2`
- .github/workflows/release-pr.yml: `Azure/action-release-workflows/.github/workflows/release_js_project.yaml@v1`
- .github/workflows/tag-and-draft.yml: `OliverMKing/javascript-release-workflow/.github/workflows/tag-and-release.yml@main`
- .github/workflows/unit-tests.yml: `actions/checkout@v1`

Locations:

- `.github/workflows/default-labels.yml:14`
- `.github/workflows/default-labels.yml:24`
- `.github/workflows/integration-tests.yml:17`
- `.github/workflows/prettify-code.yml:11`
- `.github/workflows/prettify-code.yml:14`
- `.github/workflows/release-pr.yml:14`
- `.github/workflows/tag-and-draft.yml:8`
- `.github/workflows/unit-tests.yml:14`

### missing-permissions (severity: medium)

Five workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, workflows run with the default (often broad) token permissions, violating the principle of least privilege.

Affected files:
- .github/workflows/default-labels.yml
- .github/workflows/integration-tests.yml
- .github/workflows/prettify-code.yml
- .github/workflows/tag-and-draft.yml
- .github/workflows/unit-tests.yml

Locations:

- `.github/workflows/default-labels.yml:1`
- `.github/workflows/integration-tests.yml:1`
- `.github/workflows/prettify-code.yml:1`
- `.github/workflows/tag-and-draft.yml:1`
- `.github/workflows/unit-tests.yml:1`

### hardcoded-credentials (severity: high)

The integration-tests.yml workflow contains a literal hardcoded password (`password: some-password`) embedded in a kubeconfig YAML block passed directly to the action under test. Even if this is a test credential, hardcoding credentials in workflow files is a security anti-pattern and can lead to accidental exposure of real credentials if the pattern is copied.

Locations:

- `.github/workflows/integration-tests.yml:58`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, hardcoded-credentials

**Notes:**

Fixed all findings across 6 workflow files:

1. unpinned-uses: Pinned all 8 unpinned action references to full SHA hashes:
   - actions/stale@v3 → @98ed4cb500039dbcccf4bd9bedada4d0187f2757 (×2 in default-labels.yml)
   - actions/checkout@v2 → @0717577d45739eb3c851188b29f50ed6c0b2194e (integration-tests.yml, prettify-code.yml)
   - actionsx/prettier@v2 → @8b6d14bd1241e743fa9a6112b43d691d1890a8e9 (prettify-code.yml)
   - Azure/action-release-workflows@v1 → @3c677ba5ab58f5c5c1a6f0cfb176b333b1f27405 (release-pr.yml)
   - OliverMKing/javascript-release-workflow@main → @a2f171c6ca04fea2de31f4dbb6dc576140395512 (tag-and-draft.yml)
   - actions/checkout@v1 → @50fbc622fc4ef5163becd7fab6573eac35f8462e (unit-tests.yml)

2. missing-permissions: Added top-level permissions blocks to all 5 affected files:
   - default-labels.yml: issues: write, pull-requests: write (needed by actions/stale)
   - integration-tests.yml: contents: read
   - prettify-code.yml: contents: read
   - tag-and-draft.yml: contents: write (needed for tagging/releasing)
   - unit-tests.yml: contents: read

3. hardcoded-credentials: Replaced literal `password: some-password` in integration-tests.yml kubeconfig input with `${{ secrets.TEST_EXPERIMENTER_PASSWORD }}` to avoid hardcoded credentials in the workflow file.

