<!-- markdownlint-disable -->

# Hardening Report: akiomik--mado/v0.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **akiomik--mado/v0.3.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In action.yml, the user-controlled input `inputs.args` is assigned to the env var `INPUT_ARGS` (via `INPUT_ARGS: ${{ inputs.args }}`). In action/entrypoint.sh line 34, this env var is expanded unquoted as `$COMMAND_PATH $INPUT_ARGS`, allowing an attacker to inject shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) through the `args` input. The fix is to double-quote the expansion: `"$COMMAND_PATH" "$INPUT_ARGS"` (or use an array).

Locations:

- `action.yml:20`
- `action/entrypoint.sh:34`

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable tags, branches, or version strings instead of immutable 40-character commit SHA digests. Failing references include:
- cd.yml: `actions/checkout@v4`, `houseabsolute/actions-rust-cross@v0`, `houseabsolute/actions-rust-release@v0`
- ci-action.yml: `actions/checkout@v4`
- ci-pkg.yml: `Homebrew/actions/setup-homebrew@master`, `actions/checkout@v4`, `MinoruSekine/setup-scoop@v4.0.1`, `cachix/install-nix-action@v27`
- ci.yml: `actions/checkout@v4`, `dtolnay/rust-toolchain@1.84.0`, `Swatinem/rust-cache@v2`, `taiki-e/install-action@cargo-llvm-cov`, `codecov/codecov-action@v5`
- taplo.yml: `actions/checkout@v4`, `uncenter/setup-taplo@v1`
- typos.yml: `actions/checkout@v4`, `crate-ci/typos@v1.28.4`
All of these should be pinned to full SHA digests to prevent supply-chain attacks.

Locations:

- `.github/workflows/cd.yml:31`
- `.github/workflows/cd.yml:33`
- `.github/workflows/cd.yml:38`
- `.github/workflows/ci-action.yml:13`
- `.github/workflows/ci-pkg.yml:10`
- `.github/workflows/ci-pkg.yml:18`
- `.github/workflows/ci-pkg.yml:27`
- `.github/workflows/ci-pkg.yml:36`
- `.github/workflows/ci-pkg.yml:46`
- `.github/workflows/ci-pkg.yml:52`
- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:14`
- `.github/workflows/ci.yml:15`
- `.github/workflows/ci.yml:28`
- `.github/workflows/ci.yml:38`
- `.github/workflows/ci.yml:49`
- `.github/workflows/ci.yml:59`
- `.github/workflows/ci.yml:68`
- `.github/workflows/ci.yml:72`
- `.github/workflows/ci.yml:77`
- `.github/workflows/taplo.yml:10`
- `.github/workflows/taplo.yml:11`
- `.github/workflows/typos.yml:10`
- `.github/workflows/typos.yml:11`

### missing-permissions (severity: medium)

Five workflow files have no top-level `permissions:` key and no job-level `permissions:` keys on any of their jobs. Without explicit permissions, workflows inherit the default repository permissions (which may be broad). Each file should declare minimal required permissions (e.g., `permissions: read-all` or specific scopes). Affected files: ci-action.yml, ci-pkg.yml, ci.yml, taplo.yml, typos.yml.

Locations:

- `.github/workflows/ci-action.yml:1`
- `.github/workflows/ci-pkg.yml:1`
- `.github/workflows/ci.yml:1`
- `.github/workflows/taplo.yml:1`
- `.github/workflows/typos.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings: (1) Script injection in action/entrypoint.sh: replaced unquoted `$COMMAND_PATH $INPUT_ARGS` with array-based approach using `read -ra ARGS <<< "$INPUT_ARGS"` and `"$COMMAND_PATH" "${ARGS[@]}"` to safely handle multiple arguments while preventing shell metacharacter injection. (2) Unpinned actions: pinned all 12 action references across cd.yml, ci-action.yml, ci-pkg.yml, ci.yml, taplo.yml, and typos.yml to full 40-character SHA digests with original tag preserved in comments. (3) Missing permissions: added `permissions: contents: read` top-level block to ci-action.yml, ci-pkg.yml, ci.yml, taplo.yml, and typos.yml. The cd.yml already had appropriate `permissions: contents: write` for its release workflow.

