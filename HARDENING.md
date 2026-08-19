<!-- markdownlint-disable -->

# Hardening Report: marocchino--validate-dependabot/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **marocchino--validate-dependabot/v3.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files are pinned to mutable tags rather than full 40-character SHA digests, making them vulnerable to supply-chain attacks if the tag is moved.

.github/workflows/codeql.yml:
  - uses: actions/checkout@v4
  - uses: github/codeql-action/init@v3
  - uses: github/codeql-action/autobuild@v3
  - uses: github/codeql-action/analyze@v3

.github/workflows/test.yml:
  - uses: actions/checkout@v4 (appears twice)
  - uses: marocchino/sticky-pull-request-comment@v2

Locations:

- `.github/workflows/codeql.yml:21`
- `.github/workflows/codeql.yml:24`
- `.github/workflows/codeql.yml:29`
- `.github/workflows/codeql.yml:32`
- `.github/workflows/test.yml:8`
- `.github/workflows/test.yml:18`
- `.github/workflows/test.yml:20`

### missing-permissions (severity: medium)

The workflow file test.yml has no top-level `permissions:` key, and the `build` job also has no job-level `permissions:` key. Only the `test` job defines permissions (`pull-requests: write`). Without explicit permissions on the `build` job, it inherits the default (potentially broad) token permissions. Every job must have explicit permissions, or a top-level `permissions:` block must be present.

Locations:

- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references by pinning to full SHA digests: actions/checkout@v4 → 11d5960a326750d5838078e36cf38b85af677262, github/codeql-action/{init,autobuild,analyze}@v3 → 4187e74d05793876e9989daffde9c3e66b4acd07, marocchino/sticky-pull-request-comment@v2 → 773744901bac0e8cbb5a0dc842800d45e9b2b405. Fixed missing permissions in test.yml by adding a top-level `permissions: {}` block and explicit `contents: read` permission to the `build` job. The `test` job already had `pull-requests: write` and was updated to also include `contents: read`.

