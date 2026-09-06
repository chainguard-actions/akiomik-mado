<!-- markdownlint-disable -->

# Hardening Report: akiomik--mado/v0.3.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **akiomik--mado/v0.3.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In action/entrypoint.sh, the final command `$COMMAND_PATH $INPUT_ARGS` expands `$INPUT_ARGS` without double-quoting. `INPUT_ARGS` is set from `${{ inputs.args }}` (user-controlled) in action.yml's env block. An attacker can supply shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) via the `args` input to inject arbitrary shell commands. The fix is to quote the expansion: `"$COMMAND_PATH" $INPUT_ARGS` should become `"$COMMAND_PATH" "$INPUT_ARGS"` (or use an array if multiple arguments are needed).

Locations:

- `action/entrypoint.sh:55`
- `action.yml:23`

### missing-permissions (severity: medium)

Workflow files ci-pkg.yml, ci.yml, taplo.yml, and typos.yml have no top-level `permissions:` key and no job-level `permissions:` keys on any of their jobs. Without explicit permissions, these workflows run with the repository's default token permissions, which may be overly broad (e.g., write access to contents). Each workflow should declare `permissions: {}` at the top level (or minimal specific scopes) to follow the principle of least privilege.

Locations:

- `.github/workflows/ci-pkg.yml:1`
- `.github/workflows/ci.yml:1`
- `.github/workflows/taplo.yml:1`
- `.github/workflows/typos.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

1. Fixed script injection in action/entrypoint.sh: replaced unquoted `$COMMAND_PATH $INPUT_ARGS` with xargs-based tokenization into a bash array (`args=()`), then expanded safely with `"$COMMAND_PATH" "${args[@]}"`. The `INPUT_ARGS` input is a list (default: `check .`), so xargs tokenization correctly splits it into separate arguments while preventing shell metacharacter injection. 2. Added `permissions: {}` at the top level of all four workflow files (.github/workflows/ci-pkg.yml, .github/workflows/ci.yml, .github/workflows/taplo.yml, .github/workflows/typos.yml) to enforce least privilege and prevent overly broad default token permissions.

