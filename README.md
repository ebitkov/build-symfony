# Build Symfony action

This action builds a Symfony app. You can optionally enable webpack asset building with NPM and testing with PHPUnit.

## Usage

```yaml
- uses: ebitkov/build-symfony@main
  with:
    # Repository to fetch, passed to action/checkout.
    # See https://github.com/actions/checkout for more details.
    # Mainly used for internal testing.
    # Default: ${{ github.repository }}
    repository: ''

    # Whether to build webpack assets with NPM.
    # Default: false
    build-webpack-assets: ''

    # Whether to run tests with PHPUnit.
    # runs `composer require test`, if enabled.
    # Default: false
    run-tests: ''

    # PHP version to use.
    # Default: 8.2
    php-version: ''

    # PHP extensions to install.
    # String in CSV format.
    # Default: 'mbstring, xml, ctype, iconv, intl, pdo, pdo_mysql, dom, filter, gd, json'
    php-extensions: ''

    # Additional options passed to Composer on installation.
    # --no-interaction --no-progress --ansi are added automatically.
    # Default: '--prefer-dist'
    composer-options: ''

    # Additional Composer dependencies to install.
    # Runs after the main installation.
    # Accepts flex aliases and full package names with version constraints.
    # Multiple dependencies can be defined as space-separated (e.g. 'twig symfony/doctrine-bundle')
    # Default: null
    composer-additional-dependencies: ''

    # APP_ENV value.
    # Default: 'dev'
    symfony-environment: ''
```