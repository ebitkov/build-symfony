# Build Symfony action

This action builds a Symfony app. You can optionally enable webpack asset building with NPM and testing with PHPUnit.
AssetMapper is detected automatically (`importmap.php`): vendor assets are installed, and compiled in `prod`.

With `symfony-environment: prod` the build mirrors a production build: no dev dependencies, optimized autoloader,
`composer dump-env prod` and `cache:warmup`. Note that `run-tests` installs dev dependencies.

The build can be saved to the [GitHub Action Cache](https://github.com/actions/cache) and restored in other steps or
jobs.

## Usage

```yaml
- uses: ebitkov/build-symfony@v3
  with:
    # Whether to build webpack assets with NPM.
    # Default: false
    build-webpack-assets: ''

    # Node.js version to use when building webpack assets.
    # Default: '24'
    node-version: ''

    # Whether to save the build to cache for later reuse.
    # Default: false
    cache-build: ''

    # Prefix used for the build cache key.
    # github.run_id and github.run_attempt are always appended to the end.
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

    # SSH private key, loaded into ssh-agent before Composer runs.
    # Use for private packages fetched via SSH (e.g. git@github.com:org/repo.git).
    # Pass it from a secret: ${{ secrets.SSH_PRIVATE_KEY }}
    # Default: ''
    ssh-private-key: ''

    # Symfony secrets decryption key, exported as SYMFONY_DECRYPTION_SECRET.
    # Generate with: php -r 'echo base64_encode(require "config/secrets/prod/prod.decrypt.private.php");'
    # Pass it from a secret: ${{ secrets.SYMFONY_DECRYPTION_SECRET }}
    # Default: ''
    secrets-decryption-key: ''

    # PHP extensions to install.
    # String in CSV format.
    # Default: 'mbstring, xml, ctype, iconv, intl, pdo, pdo_mysql, dom, filter, gd, json'
    php-extensions: ''

    # PHP version to use.
    # Default: 8.4
    php-version: ''
    
    # Repository to fetch, passed to action/checkout.
    # See https://github.com/actions/checkout for more details.
    # Mainly used for internal testing.
    # Default: ${{ github.repository }}
    repository: ''

    # Branch, tag or SHA to check out, passed to actions/checkout.
    # Default: 'main'
    branch: ''

    # Directory (relative to the workspace) to check out and build the app in.
    # Also the path saved to the build cache, so restore with the same `path`.
    # Default: './'
    path: ''

    # Whether to run tests with PHPUnit.
    # Installs PHPUnit automatically, if enabled.
    # Default: false
    run-tests: ''

    # APP_ENV value. 'prod' enables the production build (see above).
    # Default: 'dev'
    symfony-environment: ''

    # Whether to download Git-LFS files.
    # Default: false
    lfs: ''
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
          uses: ebitkov/build-symfony@v3
          with:
            cache-build: true

  next_job:
    needs: build
    # ...
    steps:
      - uses: actions/cache/restore@v6
        with:
          key: ${{ needs.build.outputs.cache-key }}
          path: ./
          fail-on-cache-miss: true
          
      - # next steps ...
```