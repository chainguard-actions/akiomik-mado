<!-- markdownlint-disable -->

# Hardening Report: akiomik--mado/v0.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **akiomik--mado/v0.3.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: The env var `INPUT_ARGS` is populated from `${{ inputs.args }}` (an attacker-controlled input) in action.yml, and then expanded **unquoted** in `action/entrypoint.sh` as `$COMMAND_PATH $INPUT_ARGS`. An attacker can supply shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) in the `args` input to inject arbitrary shell commands. The fix is to double-quote the expansion: `"$COMMAND_PATH" "$INPUT_ARGS"` (or pass args through an array if multiple arguments are needed).

Locations:

- `action.yml:21`
- `action/entrypoint.sh:33`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in action/entrypoint.sh: replaced unquoted `$COMMAND_PATH $INPUT_ARGS` with `read -ra ARGS <<< "$INPUT_ARGS"` followed by `"$COMMAND_PATH" "${ARGS[@]}"`. This safely splits the INPUT_ARGS string into an array without interpreting shell metacharacters, then executes the command with all arguments properly quoted. The `INPUT_ARGS` env var in action.yml (populated from `${{ inputs.args }}`) is already correctly placed in the env block rather than inline in the run script, so no changes were needed there.

