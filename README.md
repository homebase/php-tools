# php-tools

One shared home for your PHP CLI tools.

`php-tools` installs, updates, deletes, and exposes tool binaries from one dedicated checkout.

> Keep tool dependencies separate from project dependencies.
>
> This is the whole point of `php-tools` ✦ you can keep your CLI tools current even when an app is older, pinned, or has requirements that clash with the tools you want to run.

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
- [psysh](https://psysh.org/) - PHP CLI shell (Interactive PHP REPL).

## Quick Start

### Local install

```bash
mkdir -p ~/src
cd ~/src
git clone https://github.com/homebase/php-tools.git
cd php-tools
./php-tools install all
```

↳ After that first install, `php-tools` is on your `PATH`.

### Global install tools in `/usr/local/bin` (`/usr/local/src/php-tools`)

```bash
sudo mkdir -p /usr/local/src/php-tools
sudo chown "$USER" /usr/local/src/php-tools
git clone https://github.com/homebase/php-tools.git /usr/local/src/php-tools
cd /usr/local/src/php-tools
sudo USER="$USER" ./php-tools install all
```

↳ This is the setup to use when you want one shared install for the machine.

## Commands

```bash
php-tools list                    # show available tools and what is installed
php-tools install rector          # install one tool, or use: all
php-tools update all              # update installed tools + php-tools itself
php-tools update self             # just update the php-tools checkout
php-tools delete pest             # delete one tool, or use: all
php-tools symlink local           # refresh links: local|global
```

## Operational Notes

- Use `./php-tools` only for the very first install from a fresh checkout.
- After that, call `php-tools` from anywhere.

## Global owner behavior

For `/usr/local/src/php-tools`, mutating commands try to run as the checkout owner instead of staying as `root`.

- `php-tools` prefers `USER=name` if provided, otherwise the current repo owner, otherwise `dev`
- if needed, it runs `chown -R` and then re-runs the command as that user
- if no suitable non-root user can be determined, rerun with an explicit override such as `sudo USER=parf /usr/local/src/php-tools/php-tools install all`

## Compatibility Notes

- Want the latest tool versions? Use PHP 8.5 or newer.
- Most tools here still work with older PHP versions, but Pest requires PHP 8.3+.

## See Also

[parf/composer-php85-template](https://github.com/parf/composer-php85-template) → starter config for several of the tools managed here.
