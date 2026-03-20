# Project Review: php-tools

## Summary

`php-tools` is a small Bash-based toolbox for listing, installing, updating, and deleting a shared set of PHP developer tools outside application repositories. The project keeps tool binaries in `bin/`, installs Composer-managed packages under `tools/`, and records install/update timestamps in `installed/`.

The repository is centered on one entrypoint:

- `php-tools`: lists, installs, updates, and deletes managed tools

It also includes helper scripts to expose the generated binaries through symlinks in `~/bin` or `/usr/local/bin`.

## Project Structure

- `php-tools`: main CLI entrypoint and package management dispatcher
- `create-symlinks-local`: creates symlinks from `bin/*` into `~/bin`
- `create-symlinks-global`: creates symlinks from `bin/*` into `/usr/local/bin`
- `scripts/*.install`: per-tool installation commands
- `scripts/*.update`: per-tool update commands when the project supports in-place updates
- `bin/`: generated executables or symlinks to executables under `tools/`
- `tools/`: cloned repositories and Composer working directories for managed tools
- `installed/`: marker files used to decide what has been installed and what `update all` should process

## Current Behavior

### CLI Flow

`php-tools` resolves its own path, changes into the repo root, and dispatches one of four commands:

- `list`: prints all available tools and marks installed ones
- `install <tool|all>`: installs one tool or all known tools
- `update <tool|all|self>`: updates one installed tool, all installed tools, or the `php-tools` repo itself
- `delete <tool|all>`: removes one installed tool or all installed tools

Mutating actions check that the repo directory is writable. Install also checks `git` and `wget`, and bootstraps `bin/composer` through `scripts/composer.install` when needed.

`update self` runs `git pull` in the repo checkout. `update all` updates installed tools and then performs the same self-update. Other tool updates use `scripts/<tool>.update` when present, otherwise fall back to `scripts/<tool>.install`. Delete removes tool-specific state directly from `bin/`, `tools/`, and `installed/`.

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

The symlink scripts resolve the repo root from their own path and link every file under `bin/` plus the top-level `php-tools` command into either `~/bin` or `/usr/local/bin`.

## Practical Code Review

## Validation Notes

This review is based on the current tracked files in the repository:

- top-level scripts: `php-tools`, `create-symlinks-local`, `create-symlinks-global`
- package scripts: all files under `scripts/`
- documentation: `README.md` and `scripts/README.md`

Shell syntax should be validated with `bash -n` for the current entrypoints. `shellcheck` was not available in the environment during this review.
