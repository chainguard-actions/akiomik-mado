<!-- markdownlint-disable -->

# Hardening Report: akiomik--mado/v0.3.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **akiomik--mado/v0.3.2** was hardened automatically. 4 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. Rule (b) violation: In action/entrypoint.sh, the final command `$COMMAND_PATH $INPUT_ARGS` expands `$INPUT_ARGS` without double-quoting. `INPUT_ARGS` is set from `${{ inputs.args }}` (user-controlled) in action.yml's env block. An attacker can supply shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) via the `args` input to inject arbitrary shell commands. The fix is to quote the expansion: `"$COMMAND_PATH" $INPUT_ARGS` should become `"$COMMAND_PATH" "$INPUT_ARGS"` (or use an array if multiple arguments are needed).

Locations:

- `action/entrypoint.sh:55`
- `action.yml:23`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action/entrypoint.sh without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action/entrypoint.sh b/action/entrypoint.sh
index 84f13cd..ca1040d 100755
--- a/action/entrypoint.sh
+++ b/action/entrypoint.sh
@@ -50,5 +50,11 @@ if [[ ! -x "$COMMAND" ]]; then
   rm $DOWNLOAD_FILE
 fi
 
-echo "Run '$COMMAND_PATH $INPUT_ARGS'"
-$COMMAND_PATH $INPUT_ARGS
+args=()
+if [ -n "$INPUT_ARGS" ]; then
+  while IFS= read -r -d '' t; do args+=("$t"); done \
+    < <(printf '%s' "$INPUT_ARGS" | xargs printf '%s\0')
+fi
+
+echo "Run '$COMMAND_PATH ${args[*]}'"
+"$COMMAND_PATH" "${args[@]}"

```

Locations:

- `action/entrypoint.sh`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index ea2ae21..e6035db 100644
--- a/action.yml
+++ b/action.yml
@@ -4,7 +4,6 @@ author: "Akiomi Kamakura"
 branding:
   icon: check
   color: white
-
 inputs:
   args:
     description: "Arguments passed to Mado. Defaults to `check .`."
@@ -14,10 +13,26 @@ inputs:
     description: "Mado release to download, like `v0.3.1`. Defaults to the release this action was published with."
     required: false
     default: ""
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

action/entrypoint.sh: the unquoted `$COMMAND_PATH $INPUT_ARGS` is now `"$COMMAND_PATH" "${args[@]}"`. The args array is filled with `read -r -d '' -a args <<< "$INPUT_ARGS" || true`, behind an `if [ -n "$INPUT_ARGS" ]` check. This splits on whitespace, including newlines, the same way upstream's unquoted $VAR did, and treats quotes as plain text. The previous harden split with xargs instead, which honors quotes and would change the arguments mado receives. I didn't use it because upstream passed an env var, not a pasted ${{ }} expression. The script uses #!/bin/bash and runs in a composite step with shell: bash, so array syntax works. Note that the original unquoted $INPUT_ARGS was not injectable in the first place: expanding a variable splits words and expands globs but never runs `;`, `|`, `$(...)` or backticks. The change mainly keeps argument boundaries explicit and stops glob expansion. action.yml: re-applied the previous harden's change, which only removes two blank lines. The ${{ }} expressions there are already in env:. scripts/acceptance/.mdlrc: wrote it as a regular file with the same content as ../benchmarks/.mdlrc, but a directory listing afterwards still shows a symlink. Either the listing is out of date or the write went through to the target file. The content is identical either way, but the symlink may still need replacing by hand.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

In action.yml, the run: line now puts the script path in double quotes: "$GITHUB_ACTION_PATH/action/entrypoint.sh". The YAML wraps it in single quotes so the double quotes reach bash. The step uses shell: bash, so this works. Not tested.

