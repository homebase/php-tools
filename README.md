# php-tools

## Set of commonly used php tools: psalm, codestyle fixer, phpdoc, ...
so far compatible with php 7.4, php 8.0, php 8.1

Package provides one command to install, update, delete, and list tools.

**it is always a good idea to keep your tools and their dependencies in a separate place from your apps**

## Provided Tools:
* [composer](https://getcomposer.org/download/)
* [mago](https://mago.carthage.software/guide/installation)
* [pest](https://pestphp.com/docs/installation)
* [psalm](https://psalm.dev/docs/annotating_code/supported_annotations/)
* [php-stan](https://phpstan.org/writing-php-code/phpdocs-basics)
* [rector](https://github.com/rectorphp/rector/blob/main/docs/rector_rules_overview.md)
* [phpDocumentor](https://docs.phpdoc.org/3.0/guide/guides/running-phpdocumentor.html#quickstart)
* [php-cs-fixer](https://mlocati.github.io/php-cs-fixer-configurator/#version:3.8)
* [php-unit](https://phpunit.readthedocs.io/en/9.5/writing-tests-for-phpunit.html)
* [psysh](https://developpaper.com/psysh-php-interactive-console/)
* [spartan-test](https://github.com/parf/spartan-test)

## INSTALL (LOCAL)
```
mkdir -p ~/src
cd ~/src
git clone https://github.com/homebase/php-tools.git
cd php-tools
./php-tools install all
./php-tools symlink local
```

## INSTALL (GLOBAL)
```
sudo mkdir -p /usr/local/src/php-tools
cd /usr/local/src
sudo chown $USER /usr/local/src/php-tools
git clone https://github.com/homebase/php-tools.git
cd php-tools
./php-tools install all
sudo ./php-tools symlink global
```


## Commands
* `php-tools list`            - list available tools and show installed ones
* `php-tools install all`     - install all supported tools
* `php-tools install $TOOL`   - install one tool
* `php-tools update all`      - update installed tools
* `php-tools update self`     - update the `php-tools` repo with `git pull`
* `php-tools update $TOOL`    - update one tool
* `php-tools delete all`      - delete installed tools
* `php-tools delete $TOOL`    - delete one tool
* `php-tools symlink local`   - create tools symlinks in `~/bin`
* `php-tools symlink global`  - create tools symlinks in `/usr/local/bin`

after `php-tools install` *choose **one** method out of*:
* add checked out `bin` directory to your PATH
* add checked out repo root to your PATH if you want direct `php-tools` access
* `php-tools symlink local`   - create tools symlinks in `~/bin`
* `php-tools symlink global`  - create tools symlinks in `/usr/local/bin`

### Operational Notes
* run the top-level command from the repo root: `./php-tools`
* the repo checkout must be writable for install, update, and delete operations
* `php-tools update self` runs `git pull` in the repo checkout
* `php-tools update all` updates installed tools and also runs the self-update
* `php-tools delete all` deletes installed tools and also cleans stale local `spartan-test-legacy` state if present
* if nothing has been installed yet, `php-tools delete all` prints `no installed packages to delete` and exits successfully
* `php-tools symlink global` must be run as root

### Required Tools
* bash
* wget
* git


## PHP compatibility note
Most tools in this repo can still be managed for older PHP versions, but Pest currently requires PHP 8.3+ according to the official installation docs.

## See Also
 please check [parf/composer-php8-template](https://github.com/parf/composer-php8-template) project that provides default configs for above tools
