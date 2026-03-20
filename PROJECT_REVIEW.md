# Project Review: php-tools

## Summary

`php-tools` is a small Bash-based toolbox for installing and updating a shared set of PHP developer tools outside application repositories. The project keeps tool binaries in `bin/`, installs Composer-managed packages under `tools/`, and records install/update timestamps in `installed/`.

The repository is centered on two entrypoints:

- `install`: installs one tool or all known tools
- `update`: updates one installed tool or all installed tools

It also includes helper scripts to expose the generated binaries through symlinks in `~/bin` or `/usr/local/bin`.

## Project Structure

- `install`: main installer and package discovery entrypoint
- `update`: main updater and reinstall fallback entrypoint
- `create-symlinks-local`: creates symlinks from `bin/*` into `~/bin`
- `create-symlinks-global`: creates symlinks from `bin/*` into `/usr/local/bin`
- `scripts/*.install`: per-tool installation commands
- `scripts/*.update`: per-tool update commands when the project supports in-place updates
- `bin/`: generated executables or symlinks to executables under `tools/`
- `tools/`: cloned repositories and Composer working directories for managed tools
- `installed/`: marker files used to decide what has been installed and what `update all` should process

## Current Behavior

### Install Flow

`install` resolves its own path, changes into the repo root, checks for `git` and `wget`, and refuses to continue if the repo directory is not writable.

If `bin/composer` does not exist, it bootstraps Composer first through `scripts/composer.install` by invoking it with `bash`. It then creates `bin/`, `tools/`, and `installed/`.

Behavior by argument:

- no args: lists all available `scripts/*.install` packages and marks already-installed items
- `all`: iterates every `scripts/*.install` file and installs packages missing from `installed/`
- `<tool>`: runs `scripts/<tool>.install` with `bash` and writes `installed/<tool>`

### Update Flow

`update` resolves its own path, changes into the repo root, creates `installed/`, and checks repo write permission.

Behavior by argument:

- no args: prints installed package names from `installed/`
- `all`: iterates every file in `installed/` and runs `./update <tool>`
- `<tool>`: runs `scripts/<tool>.update` with `bash` if present, otherwise falls back to `scripts/<tool>.install`, then rewrites `installed/<tool>`

### Installation Methods by Tool

- PHAR or downloaded executable:
  - `composer`
  - `phpDocumentor`
  - `phpunit`
  - `psalm`
  - `psysh`
- Composer project under `tools/` with a symlink into `bin/`:
  - `php-cs-fixer`
  - `phpstan`
  - `rector`
- Git clone under `tools/` with symlinked executables:
  - `spartan-test`
  - `spartan-test-legacy`

### Symlink Helpers

The symlink scripts assume they are run from the repo root and link every file under `bin/` into either `~/bin` or `/usr/local/bin`.

## Practical Code Review

### High Severity

1. Bash is now a hard runtime dependency, but the docs and prerequisite checks do not say so.
   - All top-level scripts now declare `#!/usr/bin/env bash`: `install:1`, `update:1`, `create-symlinks-local:1`, `create-symlinks-global:1`.
   - `install` and `update` also invoke package scripts explicitly with `bash` at `install:32`, `install:74`, `update:46`, and `update:49`.
   - `README.md` still lists `wget`, `composer`, and `git` as required tools, but not `bash`.
   - Impact: the project now fails earlier and less clearly on systems where Bash is missing or not in the expected path.

2. `install` can attempt to download Composer before creating the `bin/` directory.
   - `install` calls `bash ./scripts/composer.install` at `install:31-32`.
   - `scripts/composer.install` writes directly to `bin/composer` at `scripts/composer.install:1`.
   - `mkdir -p bin tools installed` only happens later at `install:35`.
   - Impact: a fresh clone without `bin/` can fail on the first install attempt.

3. The Spartan Test installers delete existing directories unconditionally before cloning.
   - `scripts/spartan-test.install:3`
   - `scripts/spartan-test-legacy.install:3`
   - Impact: rerunning install destroys any local modifications or debugging state in `tools/spartan-test*` before a successful replacement is confirmed.

### Medium Severity

4. The symlink helper scripts depend on the caller's current directory instead of the script location.
   - `create-symlinks-local` records `pwd=$(pwd)` at `create-symlinks-local:8`.
   - `create-symlinks-global` records `pwd=$(pwd)` at `create-symlinks-global:13`.
   - Impact: running these scripts from outside the repo root creates broken symlinks.

5. Several commands interpolate unquoted variables and paths.
   - `install` uses `$0`, `$*`, `$USER`, `$what`, and path fragments unquoted at `install:26-27`, `install:43`, `install:57`, `install:68`, `install:74-75`.
   - `update` does the same at `update:22-23`, `update:33-34`, `update:39`, `update:44-51`.
   - Symlink scripts use unquoted `$pwd` and `$file` at `create-symlinks-local:12` and `create-symlinks-global:17`.
   - Impact: paths containing spaces or shell metacharacters can break argument handling or write to unintended locations.

6. `update all` relies on globbing behavior that becomes incorrect when `installed/` is empty.
   - `update` loops over `installed/*` at `update:31-35`.
   - In Bash with default glob settings, an unmatched glob remains literal, producing `what='*'` after the `sed` expression.
   - Impact: `update all` can try to update a non-existent package and return a confusing `unknown package '*'` style error.

7. Some scripts assume dependencies indirectly instead of validating them where they are needed.
   - Top-level `install` checks `git` and `wget`, but `update` does not check `git` before calling `scripts/spartan-test*.update`.
   - Composer-managed updates rely on `bin/composer` existing but `update` does not validate it before calling `scripts/composer.update`, `scripts/php-cs-fixer.update`, `scripts/phpstan.update`, or `scripts/rector.update`.
   - Impact: failure modes are deferred to lower-level commands and become less actionable for users.

### Low Severity

8. The permission error message in `install` mentions running the update command again.
   - `install:27`
   - Impact: wording is slightly misleading in the install path.

9. `scripts/README.md` warns not to run scripts directly, but several `scripts/*.install` files are still bare command fragments with no shebang.
   - Example files: `scripts/composer.install`, `scripts/php-cs-fixer.install`, `scripts/phpstan.update`
   - Impact: this is workable because the top-level scripts invoke them with `bash`, but the execution model is implicit rather than self-describing.

10. The README overstates dependency requirements.
   - `README.md` lists `composer` as a required tool even though the project bootstraps its own local `bin/composer`.
   - Impact: onboarding documentation is slightly more restrictive than the code path.

## Recommended Follow-Up

1. Document Bash as an explicit prerequisite and optionally add an early dependency check so missing Bash fails with a human-readable error.
2. Move `mkdir -p bin tools installed` ahead of the Composer bootstrap in `install`.
3. Resolve script directories in both symlink helpers the same way `install` and `update` do.
4. Quote variable expansions consistently and replace brittle `echo | sed` parsing with safer parameter expansion where practical.
5. Make `update all` explicitly handle the empty `installed/` case before entering the loop.
6. Replace destructive `rm -rf` clone flows with a safer refresh strategy or at least guard them with clearer messaging.
7. Align README dependency notes and operational details with the actual bootstrap behavior.

## Validation Notes

This review is based on the current tracked files in the repository:

- top-level scripts: `install`, `update`, `create-symlinks-local`, `create-symlinks-global`
- package scripts: all files under `scripts/`
- documentation: `README.md` and `scripts/README.md`

Shell syntax was checked earlier with `sh -n` against the script set before the Bash shebang update; the current entrypoints are Bash scripts and should be validated with `bash -n` in follow-up. `shellcheck` was not available in the environment during this review.
