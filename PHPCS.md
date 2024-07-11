# Installing and Running PHP_CodeSniffer in a Drupal Project for Custom Modules and upgrading PHP 8.2 to 8.3 version

This guide will walk you through the steps to install PHP_CodeSniffer using Composer and run it in a Drupal project to check custom modules and upgrading PHP versions

## Prerequisites

- Composer installed on your system
- A Drupal project set up

## Step-by-Step Guide

### Step 1: Install PHP_CodeSniffer via Composer

- Navigate to the root directory of your Drupal project and run the following command to install PHP_CodeSniffer:
  ```bash
  composer require --dev "squizlabs/php_codesniffer=*"

### Step 2: Adding Drupal Coding Standards

- To use Drupal's coding standards with PHP_CodeSniffer, we need to add the Coder module:
  ```bash
  composer require --dev drupal/coder

- Next, register the Drupal coding standards with PHP_CodeSniffer:
  ```bash
  vendor/bin/phpcs --config-set installed_paths vendor/drupal/coder/coder_sniffer

- Verify that the Drupal coding standards are installed:
  ```bash
  vendor/bin/phpcs -i

### Step 3: Running PHP_CodeSniffer

- To run PHP_CodeSniffer on your custom modules, use the following command:
  ```bash
  vendor/bin/phpcs --standard=Drupal docroot/modules/custom/

- Handling Memory Limit Issues:
  Edit the php.ini file to increase the memory_limit (or) Using the -d memory_limit=4096M option
  ```bash
  vendor/bin/phpcs -d memory_limit=4096M --standard=Drupal docroot/modules/custom/


### Step 4: Fixing Code Sniffer Violations
- PHP_CodeSniffer can automatically fix certain coding standard violations. To do this, run:
  ```bash
  vendor/bin/phpcbf -d memory_limit=4096M --standard=Drupal docroot/modules/custom/

### Additional Resources

- [PHP_CodeSniffer Documentation](https://github.com/squizlabs/PHP_CodeSniffer)
- [Drupal Coding Standards](https://www.drupal.org/docs/develop/standards)
- [Composer Documentation](https://getcomposer.org/doc/)

## Upgrading PHP 8.2 to PHP 8.3 on macOS Using Homebrew

### Steps to Upgrade

1. **Install PHP 8.3**
    ```bash
    brew install shivammathur/php/php@8.3
    ```

2. **Link PHP 8.3**
    ```bash
    brew link --overwrite --force php@8.3
    ```

3. **Unlink and Relink PHP**
    ```bash
    brew unlink php && brew link php
    ```

4. **Update Shell Configuration**
    ```bash
    vim ~/.zshrc
    ```

    Press `i` to enter insert mode, then paste the following code at the bottom:
    ```sh
    # PHP 8.3
    export PATH="/usr/local/opt/php@8.3/bin:$PATH"
    export PATH="/usr/local/opt/php@8.3/sbin:$PATH"
    ```
    To save and exit, press `Esc`, then type `:wq!` and press `Enter`.

5. **Apply Changes**
    ```bash
    source ~/.zshrc
    ```

6. **Verify Installation**
    ```bash
    php -v
    ```

    You should see output similar to:
    ```sh
    PHP 8.3.9 (cli) (built: Jul  4 2024 21:27:35) (NTS)
    Copyright (c) The PHP Group
    Zend Engine v4.3.9, Copyright (c) Zend Technologies
        with Zend OPcache v8.3.9, Copyright (c), by Zend Technologies
    ```

## Validate composer

Run `fin blt validate:composer`. If you see a symfony error, you will need to run `sudo composer self-update` to update some of your global packages.

## Pipelines updates
Update the following on your acquia-pipelines.yml
```
  - php:
      version: 8.3
```
## Composer updates

You will likely have quite a few composer errors and it is recommended that you `composer nuke` and `composer install` to just start fresh after updating your composer.json file.

Update the PHP version in the require line. Also check your BLT and Drush versions to make sure they are set to the major versions below.
```
"require": {
  "php": ">=8.3",
  "acquia/blt": "^13",
  "drush/drush": "^12"
},
"require-dev": {
    "webny/docksal": "^1.0"
},
```

Another change:
```
"config": {
  "platform": {
      "php": "8.3"
  },
}
```
### Additional Configuration for Your Project


### Changes to docksal.env file in local

```
DOCKSAL_STACK=acquia
DOCROOT=docroot
CLI_IMAGE='docksal/cli:php8.3-3'
COMPOSER_DEFAULT_VERSION="2"
DB_IMAGE="docksal/mysql:8.0-2.0"
```

### Changes to phpcs.xml.dist file

```
  <!-- By default, warnings and errors cause an exception. -->
  <config name="ignore_warnings_on_exit" value="0" />
  <config name="ignore_errors_on_exit" value="0" />
  <config name="testVersion" value="8.3"/>

  <config name="testVersion" value="8.3"/>
```

### Deployment steps/testing your code
```
Switch the test environment on Acquia to PHP 8.3 and test out your PR branch there. 
Turn on dblog and create a bunch of content. Check the dblog for errors.
```
