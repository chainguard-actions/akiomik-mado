<!-- markdownlint-disable -->

# Hardening Report: akiomik--mado/v0.2.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **akiomik--mado/v0.2.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In action/entrypoint.sh, the variable $INPUT_ARGS holds the user-controlled value of `inputs.args` (set via `INPUT_ARGS: ${{ inputs.args }}` in action.yml). On line 34, it is expanded unquoted: `$COMMAND_PATH $INPUT_ARGS`. This allows shell word-splitting and glob expansion on attacker-controlled input, enabling command injection via shell metacharacters (e.g., semicolons, pipes, backticks). The fix is to quote the variable: `"$COMMAND_PATH" $INPUT_ARGS` → `"$COMMAND_PATH" "$INPUT_ARGS"` (or use an array).

Locations:

- `action/entrypoint.sh:34`
- `action.yml:22`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted `$INPUT_ARGS` expansion in action/entrypoint.sh line 34. Used `read -ra ARGS <<< "$INPUT_ARGS"` to safely split the user-controlled input into a bash array, then executed `"$COMMAND_PATH" "${ARGS[@]}"` with both the command path and each argument properly quoted. This prevents shell word-splitting on metacharacters (semicolons, pipes, backticks, etc.) while preserving the intended multi-argument functionality (e.g., default `check .` passes as two separate arguments).

