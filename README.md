# php-tools

## Set of commonly used php tools: psalm, codestyle fixer, phpdoc, ...
so far compatible with php 7.4, php 8.0, php 8.1

Package provides scripts to INSTALL and UPDATE (all tools or specific ones)

**it is always a good idea to keep your tools and their dependencies in a separate place from your apps**

## Provided Tools:
* `phpstan`
* `psalm`
* `rector`
* `phpDocumentor`
* `php-cs-fixer`
* `php-unit`
* `spartan-test`
* `spartan-test (legacy)`

## INSTALL
```
mkdir -p ~/src
cd ~/src
git checkout https://github.com/homebase/php-tools.git
cd php-tools
./install all
./create-symlinks-local
```

## How to Use
* `install`                 - get help, list of available packages
* `install all`             - install ALL
* `install $TOOL`           - install specific tool
* `update  all`             - update installed packages
* `update  $TOOL`           - update specific tool

after `install` *choose **one** method out of*:
* add checked out `bin` directory to your PATH
* `create-symlinks-local`   - create tools symlinks in ~/bin (your home directory)
* `create-symlinks-global`  - create tools symlinks in /usr/local/bin

### Required Tools
* wget
* composer
* git


## php 7.4 compatibility note
as of this moment all provided tools are compatible

## See Also
 please check [parf/composer-php8-template](https://github.com/parf/composer-php8-template) project that provides default configs for above tools
