<!-- markdownlint-disable -->

# Hardening Report: akiomik--mado/v0.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **akiomik--mado/v0.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In action/entrypoint.sh, the env var $INPUT_ARGS (populated from `inputs.args` via `INPUT_ARGS: ${{ inputs.args }}` in action.yml) is expanded unquoted in two places: `echo "Run '$COMMAND_PATH $INPUT_ARGS'"` (line 29) and `$COMMAND_PATH $INPUT_ARGS` (line 30). An attacker-controlled value in `inputs.args` containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) would be parsed by the shell, enabling command injection. The fix is to double-quote the expansion: `"$INPUT_ARGS"`.

Locations:

- `action/entrypoint.sh:29`
- `action/entrypoint.sh:30`

### unpinned-uses (severity: high)

All `uses:` references across every workflow file use mutable tag, branch, or version-string refs instead of full 40-character SHA commit hashes. This exposes the pipeline to supply-chain attacks if any referenced action is compromised or its tag is moved. Failing references include: cd.yml — `actions/checkout@v4`, `houseabsolute/actions-rust-cross@v0`, `houseabsolute/actions-rust-release@v0`; ci-action.yml — `actions/checkout@v4`; ci-pkg.yml — `Homebrew/actions/setup-homebrew@master`, `actions/checkout@v4`, `MinoruSekine/setup-scoop@v4.0.1`, `cachix/install-nix-action@v27`; ci.yml — `actions/checkout@v4`, `dtolnay/rust-toolchain@1.84.0`, `Swatinem/rust-cache@v2`, `taiki-e/install-action@cargo-llvm-cov`, `codecov/codecov-action@v5`; taplo.yml — `actions/checkout@v4`, `uncenter/setup-taplo@v1`; typos.yml — `actions/checkout@v4`, `crate-ci/typos@v1.28.4`.

Locations:

- `.github/workflows/cd.yml:33`
- `.github/workflows/cd.yml:35`
- `.github/workflows/cd.yml:40`
- `.github/workflows/ci-action.yml:13`
- `.github/workflows/ci-pkg.yml:13`
- `.github/workflows/ci-pkg.yml:20`
- `.github/workflows/ci-pkg.yml:30`
- `.github/workflows/ci-pkg.yml:31`
- `.github/workflows/ci-pkg.yml:38`
- `.github/workflows/ci-pkg.yml:43`
- `.github/workflows/ci-pkg.yml:52`
- `.github/workflows/ci-pkg.yml:53`
- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:14`
- `.github/workflows/ci.yml:15`
- `.github/workflows/ci.yml:26`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:29`
- `.github/workflows/ci.yml:40`
- `.github/workflows/ci.yml:41`
- `.github/workflows/ci.yml:43`
- `.github/workflows/ci.yml:54`
- `.github/workflows/ci.yml:55`
- `.github/workflows/ci.yml:57`
- `.github/workflows/ci.yml:68`
- `.github/workflows/ci.yml:69`
- `.github/workflows/ci.yml:71`
- `.github/workflows/ci.yml:73`
- `.github/workflows/ci.yml:80`
- `.github/workflows/taplo.yml:11`
- `.github/workflows/taplo.yml:12`
- `.github/workflows/typos.yml:11`
- `.github/workflows/typos.yml:12`

### missing-permissions (severity: medium)

Five workflow files have no top-level `permissions:` block and no job-level `permissions:` blocks on any of their jobs. Without explicit permissions, workflows run with the default token permissions (which may be read/write depending on repository settings), violating the principle of least privilege. Affected files: ci-action.yml, ci-pkg.yml, ci.yml, taplo.yml, typos.yml.

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

1. script-injection (entrypoint.sh lines 29-30): Replaced unquoted `$INPUT_ARGS` with xargs-based tokenization into a bash array (`args=()`), then expanded with `"${args[@]}"`. This safely handles the list-type `inputs.args` input while preventing shell metacharacter injection.

2. unpinned-uses: Pinned all action references to full 40-char SHAs with original tag/ref as inline comments:
   - actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262
   - houseabsolute/actions-rust-cross@v0 → @9ea5352c0f279f0b89ea2eb503337acf4f27c73e
   - houseabsolute/actions-rust-release@v0 → @e47c661840f938dcc4587f52ef780bf05b1e1381
   - Homebrew/actions/setup-homebrew@master → @fd832223f9f99ebf0244dd20658680e5d4aca049
   - MinoruSekine/setup-scoop@v4.0.1 → @80f7f261b2e62af5d7450c85317b194046aa91f5
   - cachix/install-nix-action@v27 → @ba0dd844c9180cbf77aa72a116d6fbc515d0e87b
   - dtolnay/rust-toolchain@1.84.0 → @8ac6587749dbe3babc697a844a5e5053fde90c5a
   - Swatinem/rust-cache@v2 → @e18b497796c12c097a38f9edb9d0641fb99eee32
   - taiki-e/install-action@cargo-llvm-cov → @eba66cc6f87204a1e73f96e528e759b6c1fcf573
   - codecov/codecov-action@v5 → @0fb7174895f61a3b6b78fc075e0cd60383518dac
   - uncenter/setup-taplo@v1 → @09968a8ae38d66ddd3d23802c44bf6122d7aa991
   - crate-ci/typos@v1.28.4 → @9d890159570d5018df91fedfa40b4730cd4a81b1

3. missing-permissions: Added `permissions: {}` top-level block to ci-action.yml, ci-pkg.yml, ci.yml, taplo.yml, and typos.yml. cd.yml already had appropriate `permissions: contents: write`.

