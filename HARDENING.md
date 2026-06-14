<!-- markdownlint-disable -->

# Hardening Report: akiomik--mado/v0.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **akiomik--mado/v0.2.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (b) violation: The env var `INPUT_ARGS` holds the user-controlled value `${{ inputs.args }}` (set in action.yml's `env:` block) and is expanded **unquoted** in `action/entrypoint.sh` on the line `$COMMAND_PATH $INPUT_ARGS`. An attacker can supply shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) via the `args` input to execute arbitrary commands on the runner. The fix is to double-quote the expansion: `"$COMMAND_PATH" "$INPUT_ARGS"` (or use an array/`eval`-safe approach).

Locations:

- `action/entrypoint.sh:34`
- `action.yml:19`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted `$INPUT_ARGS` expansion in action/entrypoint.sh line 34. Replaced `$COMMAND_PATH $INPUT_ARGS` with `read -ra ARGS <<< "$INPUT_ARGS"` followed by `"$COMMAND_PATH" "${ARGS[@]}"`. This uses an array to safely split the user-controlled input while preventing shell metacharacter injection, and also properly double-quotes `$COMMAND_PATH`. The `INPUT_ARGS` env var in action.yml (line 19) already correctly holds the user input via `${{ inputs.args }}` — no change needed there.

