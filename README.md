# Build Symfony action

This action builds a Symfony app. You can optionally enable webpack asset building with NPM and testing with PHPUnit.

The build can be saved to the [GitHub Action Cache](https://github.com/actions/cache) and restored in other steps or
jobs.

## Usage

```yaml
- uses: ebitkov/build-symfony@v1
  with:
    # Whether to build webpack assets with NPM.
    # Default: false
    build-webpack-assets: ''

    # Whether to save the build to cache for later reuse.
    # Default: false
    cache-build: ''

    # Prefix used for the build cache key.
    # github.run_id is always appended to the end.
    # Default: 'symfony-build-'
    cache-key-prefix: ''

    # Additional Composer dependencies to install.
    # Runs after the main installation.
    # Accepts flex aliases and full package names with version constraints.
    # Multiple dependencies can be defined as space-separated (e.g. 'twig symfony/doctrine-bundle')
    # Default: null
    composer-additional-dependencies: ''

    # Additional options passed to Composer on installation.
    # --no-interaction --no-progress --ansi are added automatically.
    # Default: '--prefer-dist'
    composer-options: ''
    
    # GitHub Access Token.
    # Passed to Composer for authentication.
    # Default: ''
    github-token: ''

    # PHP extensions to install.
    # String in CSV format.
    # Default: 'mbstring, xml, ctype, iconv, intl, pdo, pdo_mysql, dom, filter, gd, json'
    php-extensions: ''

    # PHP version to use.
    # Default: 8.2
    php-version: ''
    
    # Repository to fetch, passed to action/checkout.
    # See https://github.com/actions/checkout for more details.
    # Mainly used for internal testing.
    # Default: ${{ github.repository }}
    repository: ''

    # Whether to run tests with PHPUnit.
    # Installs PHPUnit automatically, if enabled.
    # Default: false
    run-tests: ''

    # APP_ENV value.
    # Default: 'dev'
    symfony-environment: ''
```

### Reusing build files in other jobs

```yaml
jobs:
  build:
    # ...
    outputs:
      cache-key: ${{ steps.build.outputs.cache-key }}
    steps:
        - id: build
          uses: ebitkov/build-symfony@v1
          with:
            cache-build: true

  next_job:
    # ...
    steps:
      - needs: build
        uses: actions/cache/restore@v3
        with:
          key: ${{ needs.build.outputs.cache-key }}
          path: ./
          fail-on-cache-miss: true
          
      - # next steps ...
```