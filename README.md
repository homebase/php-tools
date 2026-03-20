# php-tools

Manage a shared set of PHP CLI tools from one checkout instead of installing them inside each app.

`php-tools` installs, updates, deletes, and exposes tool binaries from a dedicated repo checkout.

> Keep tool dependencies separate from project dependencies.
>
> This is the main reason to use `php-tools`: you can keep your CLI tools on current versions even when an app is older or when app requirements and tool requirements are incompatible.

## Managed Tools

- [composer](https://getcomposer.org/download/) - PHP dependency manager.
- [phpunit](https://phpunit.readthedocs.io/en/9.5/writing-tests-for-phpunit.html) - Standard PHP unit testing framework.
- [pest](https://pestphp.com/docs/installation) - Modern testing framework built on PHPUnit.
- [spartan-test](https://github.com/parf/spartan-test) - Lightweight shell-oriented test runner.
- [phpstan](https://phpstan.org/writing-php-code/phpdocs-basics) - Static analysis tool for finding type and code issues before runtime.
- [psalm](https://psalm.dev/docs/annotating_code/supported_annotations/) - Static analysis tool with strong type inference and annotation support.
- [mago](https://mago.carthage.software/guide/installation) - Single binary that replaces PHP-CS-Fixer, PHPStan, and PHP_CodeSniffer in one tool.
- [rector](https://github.com/rectorphp/rector/blob/main/docs/rector_rules_overview.md) - Automated refactoring tool for applying PHP upgrades and code transformations.
- [php-cs-fixer](https://mlocati.github.io/php-cs-fixer-configurator/#version:3.8) - Code style fixer for applying consistent formatting rules across PHP code.
- [phpDocumentor](https://docs.phpdoc.org/3.0/guide/guides/running-phpdocumentor.html#quickstart) - API documentation generator for PHP projects.
- [psysh](https://psysh.org/) - Interactive PHP REPL for quick experiments and runtime inspection.

## Quick Start

### Local install

```bash
mkdir -p ~/src
cd ~/src
git clone https://github.com/homebase/php-tools.git
cd php-tools
./php-tools install all
```

### Global-style install under `/usr/local/src/php-tools`

```bash
sudo mkdir -p /usr/local/src/php-tools
sudo chown "$USER" /usr/local/src/php-tools
git clone https://github.com/homebase/php-tools.git /usr/local/src/php-tools
cd /usr/local/src/php-tools
./php-tools install all
```

## Commands

```bash
php-tools list                     # show all managed tools and installed ones
php-tools install rector          # install one tool or use: all
php-tools update all              # update installed tools and self
php-tools update self             # git pull in the php-tools checkout
php-tools delete pest             # delete one tool or use: all
php-tools symlink local           # local|global
```

## Operational Notes

- Use `./php-tools` only for the first install from a fresh checkout. After that, the install step places `php-tools` on your `PATH`.
- `php-tools` resolves its own repo path, so it can also be called by absolute path from another directory.

## Global owner behavior

For `/usr/local/src/php-tools`, mutating commands run under the checkout owner instead of continuing as `root`.

- `php-tools` prefers `USER=name` if provided, otherwise the current repo owner, otherwise `dev`
- if needed, it `chown -R`s the checkout to that user and re-runs the command as that user
- if no suitable non-root user can be determined, rerun with an explicit override such as `sudo USER=parf /usr/local/src/php-tools/php-tools install all`

## Compatibility Notes

- To get the latest tool versions, use PHP 8.5 or newer.
- Most tools in this repo can still be managed on older PHP versions, but Pest requires PHP 8.3+.

## See Also

[parf/composer-php8-template](https://github.com/parf/composer-php8-template) provides starter config for several of the tools managed here.
