<!-- markdownlint-disable -->

# Hardening Report: marocchino--validate-dependabot/v2.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **marocchino--validate-dependabot/v2.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references in codeql.yml use mutable tag refs instead of full 40-character SHA commit hashes, making the workflow vulnerable to supply-chain attacks if the referenced action tags are moved. Unpinned refs: `actions/checkout@v4`, `github/codeql-action/init@v2`, `github/codeql-action/autobuild@v2`, `github/codeql-action/analyze@v2`.

Locations:

- `.github/workflows/codeql.yml:22`
- `.github/workflows/codeql.yml:25`
- `.github/workflows/codeql.yml:31`
- `.github/workflows/codeql.yml:34`

### unpinned-uses (severity: high)

All `uses:` references in test.yml use mutable tag refs instead of full 40-character SHA commit hashes, making the workflow vulnerable to supply-chain attacks if the referenced action tags are moved. Unpinned refs: `actions/checkout@v4` (used twice) and `marocchino/sticky-pull-request-comment@v2`.

Locations:

- `.github/workflows/test.yml:8`
- `.github/workflows/test.yml:16`
- `.github/workflows/test.yml:19`

### missing-permissions (severity: medium)

test.yml has no top-level `permissions:` block, and the `build` job also has no job-level `permissions:` block. Only the `test` job defines permissions (`pull-requests: write`). The `build` job therefore inherits the default broad repository permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/test.yml:5`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all findings across two workflow files:

1. codeql.yml (unpinned-uses): Pinned actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262, github/codeql-action/init@v2 → @b8d3b6e8af63cde30bdc382c0bc28114f4346c88, github/codeql-action/autobuild@v2 → @b8d3b6e8af63cde30bdc382c0bc28114f4346c88, github/codeql-action/analyze@v2 → @b8d3b6e8af63cde30bdc382c0bc28114f4346c88. All with # v2/v4 tag comments for readability.

2. test.yml (unpinned-uses): Pinned actions/checkout@v4 (both occurrences) → @11d5960a326750d5838078e36cf38b85af677262, marocchino/sticky-pull-request-comment@v2 → @773744901bac0e8cbb5a0dc842800d45e9b2b405.

3. test.yml (missing-permissions): Added top-level `permissions: {}` to deny all permissions by default. Added `contents: read` to the `build` job (required for checkout). The `test` job already had `pull-requests: write`; added `contents: read` to it as well for the checkout step.

