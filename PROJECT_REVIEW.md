# Project Review: php-tools

## Summary

`php-tools` is a small Bash-based toolbox for listing, installing, updating, and deleting a shared set of PHP developer tools outside application repositories. The project keeps tool binaries in `bin/`, installs Composer-managed packages under `tools/`, and records install/update timestamps in `installed/`.

The repository is centered on one entrypoint:

- `php-tools`: lists, installs, updates, and deletes managed tools

It also includes a `php-tools symlink` command to expose the generated binaries through symlinks in `~/bin` or `/usr/local/bin`.

## Project Structure

- `php-tools`: main CLI entrypoint and package management dispatcher
- `php-tools symlink local`: creates symlinks from `bin/*` into `~/bin`
- `php-tools symlink global`: creates symlinks from `bin/*` into `/usr/local/bin`
- `scripts/*.install`: per-tool installation commands
- `scripts/*.update`: per-tool update commands when the project supports in-place updates
- `bin/`: generated executables or symlinks to executables under `tools/`
- `tools/`: cloned repositories and Composer working directories for managed tools
- `installed/`: marker files used to decide what has been installed and what `update all` should process

## Current Behavior

### CLI Flow

`php-tools` resolves its own path, changes into the repo root, and dispatches one of five commands:

- `list`: prints all available tools and marks installed ones
- `install <tool|all>`: installs one tool or all known tools
- `update <tool|all|self>`: updates one installed tool, all installed tools, or the `php-tools` repo itself
- `delete <tool|all>`: removes one installed tool or all installed tools
- `symlink <local|global>`: refreshes symlinks into `~/bin` or `/usr/local/bin`

Mutating actions check that the repo directory is writable. Install also checks `git` and `wget`, and bootstraps `bin/composer` through `scripts/composer.install` when needed.

`update self` runs `git pull` in the repo checkout. `update all` updates installed tools and then performs the same self-update. Other tool updates use `scripts/<tool>.update` when present, otherwise fall back to `scripts/<tool>.install`. Delete removes tool-specific state directly from `bin/`, `tools/`, and `installed/`.

Successful installs also refresh executable symlinks automatically. Local checkouts link into `~/bin`. A checkout rooted at `/usr/local/src/php-tools` links into `/usr/local/bin`.

For `/usr/local/src/php-tools`, mutating commands prefer to run as the checkout owner instead of continuing as `root`. The script uses `USER=name` when provided, otherwise the current repo owner, otherwise `dev`. If needed, it `chown -R`s the checkout and re-runs the command as that non-root user before the original root process performs the global symlink refresh.

### Installation Methods by Tool

- PHAR or downloaded executable:
  - `composer`
  - `phpDocumentor`
  - `phpunit`
  - `psalm`
  - `psysh`
- Composer project under `tools/` with a symlink into `bin/`:
  - `mago`
  - `pest`
  - `php-cs-fixer`
  - `phpstan`
  - `rector`
- Git clone under `tools/` with symlinked executables:
  - `spartan-test`

### Symlink Handling

The symlink command resolves the repo root from its own path and links every file under `bin/` plus the top-level `php-tools` command into either `~/bin` or `/usr/local/bin`.

## Practical Code Review

## Validation Notes

This review is based on the current tracked files in the repository:

- top-level script: `php-tools`
- package scripts: all files under `scripts/`
- documentation: `README.md` and `scripts/README.md`

Shell syntax should be validated with `bash -n` for the current entrypoints. `shellcheck` was not available in the environment during this review.
