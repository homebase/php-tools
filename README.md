# php-tools

## Set of standard php 8 / php 8.1 tools: psalm, cs-fixer, phpdoc, ...

scripts to INSTALL and UPDATE (all tools or specific ones)

**it is always a good idea to keep your tools and their dependencies in a separate place from your apps**

## Tools:
* `phpstan`
* `psalm`
* `rector`
* `phpDocumentor`
* `php-cs-fixer`
* `php-unit`
* `spartan-test`
* `spartan-test (legacy)`

## Scripts
* `install all`             - install ALL
* `install $TOOL`           - install specific tool
* `update  all`             - update installed packages
* `update  $TOOL`           - update specific tool
* `create-symlinks-local`   - create tools symlinks in ~/bin
* `create-symlinks-global`  - run-as-root: create tools symlinks /usr/local/bin

Once tool is installed symlink(s) placed in bin directory - so you can just add this directory to PATH if you want

# How to Use / Install
* checkout this repo somewhere:
  - for local install we suggest: `~/src`
  - for global(all users) install we suggest: `/usr/local/src`
* run `./install all`
  - if you want specific packages, run `./install` to see available packages ; then `./install package_name`

## Making all tools available anywhere:

  - Method 1: add checked out `bin` directory to your path
  - Method 2: run `create-symlinks-local` - this will create symlinks in your "~/bin" directory
  - Method 3: run `create-symlinks-global` - this will create symlinks in `/usr/local/bin`

### Required Tools
* wget
* composer
* git