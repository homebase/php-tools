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

### Symlink Helpers

The symlink scripts assume they are run from the repo root and link every file under `bin/` into either `~/bin` or `/usr/local/bin`.

## Practical Code Review

## Validation Notes

This review is based on the current tracked files in the repository:

- top-level scripts: `install`, `update`, `create-symlinks-local`, `create-symlinks-global`
- package scripts: all files under `scripts/`
- documentation: `README.md` and `scripts/README.md`

Shell syntax was checked earlier with `sh -n` against the script set before the Bash shebang update; the current entrypoints are Bash scripts and should be validated with `bash -n` in follow-up. `shellcheck` was not available in the environment during this review.
