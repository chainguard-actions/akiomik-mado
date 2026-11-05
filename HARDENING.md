<!-- markdownlint-disable -->

# Hardening Report: akiomik--mado/v0.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **akiomik--mado/v0.4.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

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

**Notes:**

Not fixed: scripts/acceptance/.mdlrc is still a symlink to ../benchmarks/.mdlrc. write_file went through the symlink and rewrote scripts/benchmarks/.mdlrc with the content it already had, so nothing changed there. None of the available tools can delete a symlink, so it could not be replaced with a regular file. To finish it, run from hardened/action: rm scripts/acceptance/.mdlrc && cp scripts/benchmarks/.mdlrc scripts/acceptance/.mdlrc. The resulting file should hold the one 'rules "MD001", ... "MD047"' line, exactly as in the previous patch.

