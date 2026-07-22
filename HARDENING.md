<!-- markdownlint-disable -->

# Hardening Report: akiomik--mado/v0.3.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **akiomik--mado/v0.3.1** was hardened automatically. 12 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In action/entrypoint.sh (called from action.yml), the env var $INPUT_ARGS holds the value of inputs.args (user-controlled, set via `INPUT_ARGS: ${{ inputs.args }}` in action.yml) and is expanded unquoted in the shell command `$COMMAND_PATH $INPUT_ARGS` on line 32. An attacker can supply shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) via the `args` input to inject arbitrary commands.

Locations:

- `action/entrypoint.sh:32`
- `action.yml:18`

### unpinned-uses (severity: high)

All uses: references in cd.yml use mutable tags instead of pinned SHA hashes: `actions/checkout@v7` (line 30), `houseabsolute/actions-rust-cross@v1` (line 32), `houseabsolute/actions-rust-release@v0.0.8` (line 40). These can be silently updated to malicious versions.

Locations:

- `.github/workflows/cd.yml:30`
- `.github/workflows/cd.yml:32`
- `.github/workflows/cd.yml:40`

### unpinned-uses (severity: high)

All uses: references in ci-action.yml use mutable tags instead of pinned SHA hashes: `actions/checkout@v7` (line 13).

Locations:

- `.github/workflows/ci-action.yml:13`

### unpinned-uses (severity: high)

All uses: references in ci-pkg.yml use mutable tags or branch names instead of pinned SHA hashes: `Homebrew/actions/setup-homebrew@master` (line 15), `actions/checkout@v7` (lines 24, 36, 52), `MinoruSekine/setup-scoop@v5.0.0` (lines 38, 55), `cachix/install-nix-action@v31` (line 66).

Locations:

- `.github/workflows/ci-pkg.yml:15`
- `.github/workflows/ci-pkg.yml:24`
- `.github/workflows/ci-pkg.yml:36`
- `.github/workflows/ci-pkg.yml:38`
- `.github/workflows/ci-pkg.yml:52`
- `.github/workflows/ci-pkg.yml:55`
- `.github/workflows/ci-pkg.yml:64`
- `.github/workflows/ci-pkg.yml:65`

### unpinned-uses (severity: high)

All uses: references in ci.yml use mutable tags or version strings instead of pinned SHA hashes: `actions/checkout@v7` (×5), `dtolnay/rust-toolchain@1.91.1` (×5), `Swatinem/rust-cache@v2` (×5), `taiki-e/install-action@cargo-llvm-cov`, `codecov/codecov-action@v7`.

Locations:

- `.github/workflows/ci.yml:14`
- `.github/workflows/ci.yml:15`
- `.github/workflows/ci.yml:16`
- `.github/workflows/ci.yml:28`
- `.github/workflows/ci.yml:29`
- `.github/workflows/ci.yml:30`
- `.github/workflows/ci.yml:42`
- `.github/workflows/ci.yml:43`
- `.github/workflows/ci.yml:44`
- `.github/workflows/ci.yml:56`
- `.github/workflows/ci.yml:57`
- `.github/workflows/ci.yml:58`
- `.github/workflows/ci.yml:70`
- `.github/workflows/ci.yml:71`
- `.github/workflows/ci.yml:72`
- `.github/workflows/ci.yml:73`
- `.github/workflows/ci.yml:77`

### unpinned-uses (severity: high)

All uses: references in taplo.yml use mutable tags instead of pinned SHA hashes: `actions/checkout@v7` (line 11), `uncenter/setup-taplo@v2` (line 12).

Locations:

- `.github/workflows/taplo.yml:11`
- `.github/workflows/taplo.yml:12`

### unpinned-uses (severity: high)

All uses: references in typos.yml use mutable tags instead of pinned SHA hashes: `actions/checkout@v7` (line 11), `crate-ci/typos@v1.48.0` (line 12).

Locations:

- `.github/workflows/typos.yml:11`
- `.github/workflows/typos.yml:12`

### missing-permissions (severity: medium)

Workflow file ci-action.yml has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the GITHUB_TOKEN inherits the repository's default permissions, which may be overly broad.

Locations:

- `.github/workflows/ci-action.yml:1`

### missing-permissions (severity: medium)

Workflow file ci-pkg.yml has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the GITHUB_TOKEN inherits the repository's default permissions, which may be overly broad.

Locations:

- `.github/workflows/ci-pkg.yml:1`

### missing-permissions (severity: medium)

Workflow file ci.yml has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the GITHUB_TOKEN inherits the repository's default permissions, which may be overly broad.

Locations:

- `.github/workflows/ci.yml:1`

### missing-permissions (severity: medium)

Workflow file taplo.yml has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the GITHUB_TOKEN inherits the repository's default permissions, which may be overly broad.

Locations:

- `.github/workflows/taplo.yml:1`

### missing-permissions (severity: medium)

Workflow file typos.yml has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the GITHUB_TOKEN inherits the repository's default permissions, which may be overly broad.

Locations:

- `.github/workflows/typos.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all findings: (1) Script injection in action/entrypoint.sh: replaced unquoted `$COMMAND_PATH $INPUT_ARGS` with `read -ra args <<< "$INPUT_ARGS"` + `"$COMMAND_PATH" "${args[@]}"` to safely handle user-controlled args without shell metacharacter injection. (2) Pinned all unpinned `uses:` references across cd.yml, ci-action.yml, ci-pkg.yml, ci.yml, taplo.yml, and typos.yml to full commit SHAs with original tags as comments. (3) Added `permissions: contents: read` top-level blocks to ci-action.yml, ci-pkg.yml, ci.yml, taplo.yml, and typos.yml (cd.yml already had appropriate permissions).

