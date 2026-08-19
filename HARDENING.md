<!-- markdownlint-disable -->

# Hardening Report: marocchino--validate-dependabot/v2.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **marocchino--validate-dependabot/v2.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references in workflow files use mutable tag refs instead of pinned 40-character SHA digests, making the action vulnerable to supply-chain attacks if any referenced action is compromised or its tag is moved.

- `.github/workflows/codeql.yml`: `actions/checkout@v3`, `github/codeql-action/init@v2`, `github/codeql-action/autobuild@v2`, `github/codeql-action/analyze@v2`
- `.github/workflows/comment.yml`: `marocchino/on_artifact@v1`, `marocchino/sticky-pull-request-comment@v2`
- `.github/workflows/test.yml`: `actions/checkout@v2` (×2), `actions/upload-artifact@v2`

Locations:

- `.github/workflows/codeql.yml:22`
- `.github/workflows/codeql.yml:26`
- `.github/workflows/codeql.yml:31`
- `.github/workflows/codeql.yml:34`
- `.github/workflows/comment.yml:12`
- `.github/workflows/comment.yml:14`
- `.github/workflows/test.yml:13`
- `.github/workflows/test.yml:19`
- `.github/workflows/test.yml:30`

### missing-permissions (severity: medium)

Workflow files `comment.yml` and `test.yml` have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, the GITHUB_TOKEN is granted default (potentially write) permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/comment.yml:1`
- `.github/workflows/test.yml:1`

### script-injection (severity: high)

Sub-rule (a) violation: The `run:` block in `test.yml` directly interpolates GitHub Actions expressions inside shell commands. The expressions `${{ github.event.number }}`, `${{ steps.validate.outputs.markdown }}`, and `${{ steps.validate.outputs.raw }}` are substituted into the shell script before execution. An attacker who controls a pull request number or action outputs could inject arbitrary shell commands.

Offending lines:
  `echo ${{ github.event.number }} | tee ./pr/number`
  `${{ steps.validate.outputs.markdown }}`
  `${{ steps.validate.outputs.raw }}`

Locations:

- `.github/workflows/test.yml:22`
- `.github/workflows/test.yml:24`
- `.github/workflows/test.yml:27`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings across .github/workflows/codeql.yml, comment.yml, and test.yml:

1. unpinned-uses: Pinned all 9 action references to full 40-char SHAs with original tags as comments:
   - actions/checkout@v3 → @a37ce9120846195fa4ece8f58b268e6043cb2f26
   - actions/checkout@v2 → @0717577d45739eb3c851188b29f50ed6c0b2194e (×2)
   - github/codeql-action/init@v2 → @b8d3b6e8af63cde30bdc382c0bc28114f4346c88
   - github/codeql-action/autobuild@v2 → @b8d3b6e8af63cde30bdc382c0bc28114f4346c88
   - github/codeql-action/analyze@v2 → @b8d3b6e8af63cde30bdc382c0bc28114f4346c88
   - marocchino/on_artifact@v1 → @fa52c295bf1972c5d09a615f31edb68c4438fb06
   - marocchino/sticky-pull-request-comment@v2 → @773744901bac0e8cbb5a0dc842800d45e9b2b405
   - actions/upload-artifact@v2 → @82c141cc518b40d92cc801eee768e7aafc9c2fa2

2. missing-permissions: Added top-level `permissions: {}` to comment.yml and test.yml; added job-level `pull-requests: write` to comment job and `contents: read` to build/test jobs.

3. script-injection: Moved ${{ github.event.number }}, ${{ steps.validate.outputs.markdown }}, and ${{ steps.validate.outputs.raw }} out of the run: shell script into the step's env: block as PR_NUMBER, VALIDATE_MARKDOWN, and VALIDATE_RAW, then referenced them as plain shell variables.

