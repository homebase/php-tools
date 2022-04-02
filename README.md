# php-tools

## Set of standard php 8 / php 8.1 tools: psalm, cs-fixer, phpdoc, ...

scripts to INSTALL and UPDATE (all tools or specific ones)

**it is always a good idea to keep your tools and their dependencies in a separate place from your apps**

## Tools:
* phpstan
* psalm
* rector
* phpDocumentor
* php-cs-fixer
* php-unit
* spartan-test
* spartan-test (legacy)

## Scripts
* install all             - install ALL
* install $TOOL           - install specific tool
* update  all             - update installed packages
* update  $TOOL           - update specific tool
* create-symlinks-local   - create tools symlinks in ~/bin
* create-symlinks-global  - run-as-root: create tools symlinks /usr/local/bin

Once tool is installed symlink(s) placed in bin directory - so you can just add this directory to PATH if you want

Actual install/update scripts kept in scripts directory
* scripts/install-$TOOL   - install/update specific tool
* scripts/update-$TOOL    - update specific tool



### PS:
 required tools: wget, composer, git