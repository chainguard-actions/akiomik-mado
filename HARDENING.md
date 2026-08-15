<!-- markdownlint-disable -->

# Hardening Report: akiomik--mado/v0.2.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **akiomik--mado/v0.2.2** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In action.yml, the input `inputs.args` is mapped to the env var INPUT_ARGS via `INPUT_ARGS: ${{ inputs.args }}`. In action/entrypoint.sh, this env var is expanded unquoted in the final command: `$COMMAND_PATH $INPUT_ARGS`. An attacker-controlled value in `inputs.args` containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) will be interpreted by the shell, enabling command injection.

Locations:

- `action.yml:20`
- `action/entrypoint.sh:33`

### unpinned-uses (severity: high)

All workflow files use mutable tag, branch, or version refs instead of immutable 40-character SHA pins, making them vulnerable to supply-chain attacks if the referenced action is compromised or the tag is moved. Failing references:
- cd.yml: actions/checkout@v4, houseabsolute/actions-rust-cross@v0, houseabsolute/actions-rust-release@v0
- ci-action.yml: actions/checkout@v4
- ci-pkg.yml: Homebrew/actions/setup-homebrew@master, actions/checkout@v4, MinoruSekine/setup-scoop@v4.0.1, cachix/install-nix-action@v27
- ci.yml: actions/checkout@v4, dtolnay/rust-toolchain@1.84.0, Swatinem/rust-cache@v2, taiki-e/install-action@cargo-llvm-cov, codecov/codecov-action@v5
- taplo.yml: actions/checkout@v4, uncenter/setup-taplo@v1
- typos.yml: actions/checkout@v4, crate-ci/typos@v1.28.4

Locations:

- `.github/workflows/cd.yml:34`
- `.github/workflows/cd.yml:36`
- `.github/workflows/cd.yml:42`
- `.github/workflows/ci-action.yml:14`
- `.github/workflows/ci-pkg.yml:15`
- `.github/workflows/ci-pkg.yml:24`
- `.github/workflows/ci-pkg.yml:34`
- `.github/workflows/ci-pkg.yml:35`
- `.github/workflows/ci-pkg.yml:43`
- `.github/workflows/ci-pkg.yml:45`
- `.github/workflows/ci-pkg.yml:58`
- `.github/workflows/ci-pkg.yml:59`
- `.github/workflows/ci.yml:14`
- `.github/workflows/ci.yml:15`
- `.github/workflows/ci.yml:16`
- `.github/workflows/ci.yml:26`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:28`
- `.github/workflows/ci.yml:38`
- `.github/workflows/ci.yml:39`
- `.github/workflows/ci.yml:40`
- `.github/workflows/ci.yml:50`
- `.github/workflows/ci.yml:51`
- `.github/workflows/ci.yml:52`
- `.github/workflows/ci.yml:62`
- `.github/workflows/ci.yml:63`
- `.github/workflows/ci.yml:64`
- `.github/workflows/ci.yml:65`
- `.github/workflows/ci.yml:66`
- `.github/workflows/taplo.yml:13`
- `.github/workflows/taplo.yml:14`
- `.github/workflows/typos.yml:13`
- `.github/workflows/typos.yml:14`

### missing-permissions (severity: medium)

Five workflow files have no top-level `permissions:` key and no job-level `permissions:` keys on any of their jobs. Without explicit permissions, GitHub Actions grants the default token permissions (which may include write access to contents and other scopes depending on repository settings), violating the principle of least privilege.

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

Fixed script injection in action/entrypoint.sh by using 'read -ra ARGS <<< "$INPUT_ARGS"' and '${ARGS[@]}' instead of unquoted $INPUT_ARGS expansion. Pinned all action references in all workflow files to their full 40-character SHA commits with original tag/ref preserved in comments. Added 'permissions: contents: read' to ci-action.yml, ci-pkg.yml, ci.yml, taplo.yml, and typos.yml (cd.yml already had appropriate write permissions for releases).

