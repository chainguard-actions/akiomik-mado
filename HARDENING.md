<!-- markdownlint-disable -->

# Hardening Report: akiomik--mado/v0.3.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **akiomik--mado/v0.3.1** was hardened automatically. 4 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. Rule (b) violation: In action/entrypoint.sh (called from action.yml), the env var $INPUT_ARGS holds the value of inputs.args (user-controlled, set via `INPUT_ARGS: ${{ inputs.args }}` in action.yml) and is expanded unquoted in the shell command `$COMMAND_PATH $INPUT_ARGS` on line 32. An attacker can supply shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) via the `args` input to inject arbitrary commands.

Locations:

- `action/entrypoint.sh:32`
- `action.yml:18`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action/entrypoint.sh without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action/entrypoint.sh b/action/entrypoint.sh
index a36e1d3..bf6127f 100755
--- a/action/entrypoint.sh
+++ b/action/entrypoint.sh
@@ -30,4 +30,5 @@ if [[ ! -x "$COMMAND" ]]; then
 fi
 
 echo "Run '$COMMAND_PATH $INPUT_ARGS'"
-$COMMAND_PATH $INPUT_ARGS
+read -ra args <<< "$INPUT_ARGS"
+"$COMMAND_PATH" "${args[@]}"

```

Locations:

- `action/entrypoint.sh`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 70f1e61..5b5d185 100644
--- a/action.yml
+++ b/action.yml
@@ -4,16 +4,31 @@ author: "Akiomi Kamakura"
 branding:
   icon: check
   color: white
-
 inputs:
   args:
     description: "Arguments passed to Mado. Defaults to `check .`."
     required: false
     default: "check ."
-
 runs:
   using: 'composite'
   steps:
     - id: mado
       shell: bash
       run: $GITHUB_ACTION_PATH/action/entrypoint.sh

```

Locations:

- `action.yml`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed scripts/acceptance/.mdlrc without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/scripts/acceptance/.mdlrc b/scripts/acceptance/.mdlrc
deleted file mode 120000
index c0b19a6..0000000
--- a/scripts/acceptance/.mdlrc
+++ /dev/null
@@ -1 +0,0 @@
-../benchmarks/.mdlrc
\ No newline at end of file
diff --git a/scripts/acceptance/.mdlrc b/scripts/acceptance/.mdlrc
new file mode 100644
index 0000000..a296d3c
--- /dev/null
+++ b/scripts/acceptance/.mdlrc
@@ -0,0 +1 @@
+rules "MD001", "MD002", "MD003", "MD004", "MD005", "MD006", "MD007", "MD009", "MD010", "MD012", "MD013", "MD014", "MD018", "MD019", "MD020", "MD021", "MD022", "MD023", "MD024", "MD025", "MD026", "MD027", "MD028", "MD029", "MD030", "MD031", "MD032", "MD033", "MD034", "MD035", "MD036", "MD037", "MD038", "MD039", "MD040", "MD041", "MD046", "MD047"

```

Locations:

- `scripts/acceptance/.mdlrc`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, prior-fix

**Notes:**

I fixed the three files below by reading the code. Nothing was run or tested.

1. **`action/entrypoint.sh`** (script-injection): `$INPUT_ARGS` was expanded unquoted when the script ran mado. It's now split into a bash array and passed as "$COMMAND_PATH" "${args[@]}". The split uses `read -r -d '' -a args <<< "$INPUT_ARGS" || true`, behind an `if [[ -n "$INPUT_ARGS" ]]` check. This splits on spaces and newlines like upstream's unquoted variable did, and keeps quote characters as plain text. The one change in behaviour: `*` or `?` in `args` reaches mado as a literal word instead of being expanded by the shell. I didn't use the previous harden's `read -ra`, because it only reads the first line of the input.

2. **`action.yml`**: the script path is now quoted (`run: '"$GITHUB_ACTION_PATH/action/entrypoint.sh"'`). Mapping `inputs.args` through `env:` was already in place. The previous patch only removed blank lines, so I didn't repeat that.

3. **`scripts/acceptance/.mdlrc`**: replaced the symlink to `../benchmarks/.mdlrc` with a regular file holding the same content, as the previous harden did.

### Iteration 1

**Fixes applied:** publish-gate

**Notes:**

Replaced bash array-style assignments PROJECT_ROOT=(...) with plain scalar assignments in scripts/acceptance/test.sh and scripts/benchmarks/comparison.sh (#!/bin/sh scripts). Value is unchanged (a one-element array's $PROJECT_ROOT is the same string).

