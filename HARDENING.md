<!-- markdownlint-disable -->

# Hardening Report: Azure--k8s-set-context/v5.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Azure--k8s-set-context/v5.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two workflow files reference external actions/workflows using mutable tags or branch names instead of pinned full-length SHA commit hashes:
- `.github/workflows/release-pr.yml` (line 15): `uses: Azure/action-release-workflows/.github/workflows/release_js_project.yaml@v1` — uses tag `@v1`
- `.github/workflows/tag-and-draft.yml` (line 10): `uses: OliverMKing/javascript-release-workflow/.github/workflows/tag-and-release.yml@main` — uses branch `@main`

Mutable refs can be silently updated to point to malicious code, enabling supply-chain attacks.

Locations:

- `.github/workflows/release-pr.yml:15`
- `.github/workflows/tag-and-draft.yml:10`

### hardcoded-credentials (severity: high)

The workflow file `.github/workflows/integration-tests.yml` contains a literal hardcoded password value `some-password` assigned to the key `password` inside an embedded kubeconfig test fixture. Even though this appears to be a placeholder/fake credential, it is a literal non-expression value assigned to a field named `password` in a workflow file. Pattern matched: `password: some-password`.

Locations:

- `.github/workflows/integration-tests.yml:56`

### missing-permissions (severity: medium)

The following workflow files have no top-level `permissions:` block and no job-level `permissions:` block on any of their jobs. Without explicit permissions, workflows inherit the default (potentially broad) repository permissions:
- `.github/workflows/default-labels.yml`
- `.github/workflows/integration-tests.yml`
- `.github/workflows/prettify-code.yml`
- `.github/workflows/tag-and-draft.yml`
- `.github/workflows/unit-tests.yml`

Locations:

- `.github/workflows/default-labels.yml:1`
- `.github/workflows/integration-tests.yml:1`
- `.github/workflows/prettify-code.yml:1`
- `.github/workflows/tag-and-draft.yml:1`
- `.github/workflows/unit-tests.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, hardcoded-credentials, missing-permissions

**Notes:**

Fixed all three findings:
1. unpinned-uses: Pinned Azure/action-release-workflows@v1 to SHA 3c677ba5ab58f5c5c1a6f0cfb176b333b1f27405 in release-pr.yml, and OliverMKing/javascript-release-workflow@main to SHA a2f171c6ca04fea2de31f4dbb6dc576140395512 in tag-and-draft.yml.
2. hardcoded-credentials: Replaced both occurrences of literal 'some-password' in integration-tests.yml (kubeconfig input fixture and EXPECTED_KC JSON comparison block) with ${{ secrets.INTEGRATION_TEST_PASSWORD }}.
3. missing-permissions: Added top-level permissions blocks to all five affected workflows — default-labels.yml (issues: write, pull-requests: write for stale action), integration-tests.yml (contents: read), prettify-code.yml (contents: read), tag-and-draft.yml (permissions: {}), and unit-tests.yml (contents: read).

