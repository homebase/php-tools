# php-tools

## Set of commonly used php 8/8.1 tools: psalm, codestyle fixer, phpdoc, ...

provide scripts to INSTALL and UPDATE (all tools or specific ones)

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