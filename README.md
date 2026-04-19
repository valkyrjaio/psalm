<p align="center"><a href="https://valkyrja.io" target="_blank">
    <img src="https://raw.githubusercontent.com/valkyrjaio/art/refs/heads/master/long-banner/orange/php.png" width="100%">
</a></p>

# Valkyrja Psalm

Shared Psalm configuration for Valkyrja PHP projects — a reference
configuration and reusable workflow that enforce consistent static analysis
across consuming repositories.

<p>
    <a href="https://packagist.org/packages/valkyrja/psalm"><img src="https://poser.pugx.org/valkyrja/psalm/require/php" alt="PHP Version Require"></a>
    <a href="https://packagist.org/packages/valkyrja/psalm"><img src="https://poser.pugx.org/valkyrja/psalm/v" alt="Latest Stable Version"></a>
    <a href="https://packagist.org/packages/valkyrja/psalm"><img src="https://poser.pugx.org/valkyrja/psalm/license" alt="License"></a>
    <a href="https://github.com/valkyrjaio/ci-psalm-php/actions/workflows/ci.yml?query=branch%3A26.x"><img src="https://github.com/valkyrjaio/ci-psalm-php/actions/workflows/ci.yml/badge.svg?branch=26.x" alt="CI Status"></a>
    <a href="https://scrutinizer-ci.com/g/valkyrjaio/ci-psalm-php/?branch=26.x"><img src="https://scrutinizer-ci.com/g/valkyrjaio/ci-psalm-php/badges/quality-score.png?b=26.x" alt="Scrutinizer"></a>
    <a href="https://coveralls.io/github/valkyrjaio/ci-psalm-php?branch=26.x"><img src="https://coveralls.io/repos/github/valkyrjaio/ci-psalm-php/badge.svg?branch=26.x" alt="Coverage Status" /></a>
    <a href="https://shepherd.dev/github/valkyrjaio/ci-psalm-php"><img src="https://shepherd.dev/github/valkyrjaio/ci-psalm-php/coverage.svg" alt="Psalm Shepherd" /></a>
    <a href="https://sonarcloud.io/summary/new_code?id=valkyrjaio_psalm"><img src="https://sonarcloud.io/api/project_badges/measure?project=valkyrjaio_psalm&metric=sqale_rating" alt="Maintainability Rating" /></a>
</p>

Usage
-----

Place a `psalm.xml` in your CI directory pointing to the project source. Run
via the root Composer scripts:

```
# Check only (no shepherd, no cache cleared)
composer psalm

# Check with no cache
composer psalm-no-cache

# Post results to Psalm Shepherd
composer psalm-shepherd

# Post results with stats to Psalm Shepherd
composer psalm-shepherd-with-stats

# Show type coverage stats
composer psalm-stats

# Update the baseline file
composer psalm-update-baseline
```

Configuration
-------------

The CI directory ships with a `psalm.xml` that serves as the reference
configuration. Key settings:

| Setting                   | Value                | Effect                                                                         |
|---------------------------|----------------------|--------------------------------------------------------------------------------|
| `errorLevel`              | `1`                  | Strictest level — all issues reported                                          |
| `totallyTyped`            | `true`               | Every expression must be typed                                                 |
| `findUnusedBaselineEntry` | `true`               | Warns when a baseline suppression is no longer needed                          |
| `findUnusedCode`          | `false`              | Dead-code detection disabled                                                   |
| `errorBaseline`           | `psalm-baseline.xml` | Known issues tracked in baseline; update with `composer psalm-update-baseline` |

### Scanned Paths

| Path      | Included |
|-----------|----------|
| `src/`    | Yes      |
| `vendor/` | No       |

### Suppressed Issue Types

These issue types are suppressed globally via `<issueHandlers>`:

| Issue                                  | Reason                                                                  |
|----------------------------------------|-------------------------------------------------------------------------|
| `PropertyNotSetInConstructor`          | Properties initialised outside constructors are common in the framework |
| `DeprecatedClass`                      | Suppressed temporarily while the `Env` class deprecation is in progress |
| `ClassMustBeFinal`                     | Framework classes are intentionally left non-final for extensibility    |
| `RedundantPropertyInitializationCheck` | False positives triggered by `??=` assignments                          |
| `UnsafeInstantiation`                  | Child class constructor parameter matching is left to the developer     |

### Notes

- `PSALM_ALLOW_XDEBUG=1` is set in CI as a workaround for a JIT interaction
  with the `#[Override]` attribute.
  See [vimeo/psalm#11723](https://github.com/vimeo/psalm/issues/11723).
- An `autoload.php` is required in the CI directory to bootstrap the project
  autoloader before Psalm analyses the source.

Workflows
---------

The [`_workflow-call.yml`](.github/workflows/_workflow-call.yml) reusable
workflow runs Psalm against the calling repository's source. It is designed
to be called from other repositories via `workflow_call`.

### Inputs

| Input                  | Type    | Default              | Description                                                                                                                                           |
|------------------------|---------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| `paths`                | string  | —                    | **Required.** YAML filter spec with two keys: `ci` (CI config files that trigger a base-branch fetch) and `files` (all files that trigger the check). |
| `post-pr-comment`      | boolean | `true`               | Post a PR comment on failure and remove it on success. Disable when the calling workflow handles its own reporting.                                   |
| `composer-options`     | string  | `''`                 | Extra flags passed to every `composer install` step (e.g. `--ignore-platform-req=ext-openswoole`).                                                    |
| `php-version`          | string  | `'8.4'`              | PHP version to use.                                                                                                                                   |
| `ci-directory`         | string  | `'.github/ci/psalm'` | Path to the CI directory containing `composer.json` and the tool config.                                                                              |
| `extensions`           | string  | `'mbstring, intl'`   | PHP extensions to install via `shivammathur/setup-php`.                                                                                               |
| `additional-directory` | string  | `''`                 | Path to an additional Composer dependencies directory to install before running Psalm. Leave empty to skip.                                           |
| `run-script`           | string  | `'psalm'`            | Composer script to run (e.g. `psalm`, `psalm-shepherd`, `psalm-shepherd-with-stats`).                                                                 |

### Usage

```yaml
jobs:
  psalm:
    uses: valkyrjaio/ci-psalm-php/.github/workflows/_workflow-call.yml@26.x
    permissions:
      pull-requests: write
      contents: read
    with:
      php-version: '8.4'
      run-script: 'psalm-shepherd-with-stats'
      paths: |
        ci:
          - '.github/ci/psalm/**'
          - '.github/workflows/psalm.yml'
        files:
          - '.github/ci/psalm/**'
          - '.github/workflows/psalm.yml'
          - 'src/**/*.php'
          - 'composer.json'
    secrets: inherit
```

`secrets: inherit` is required to pass the `VALKYRJA_GHA_APP_ID` and
`VALKYRJA_GHA_PRIVATE_KEY` org secrets used for PR comments.

Contributing
------------

See [`CONTRIBUTING.md`][contributing url] for the submission process and
[`VOCABULARY.md`][vocabulary url] for the terminology used across Valkyrja.

Security Issues
---------------

If you discover a security vulnerability, please follow our
[disclosure procedure][security vulnerabilities url].

License
-------

Licensed under the [MIT license][MIT license url]. See
[`LICENSE.md`](./LICENSE.md).

[contributing url]: https://github.com/valkyrjaio/.github/blob/master/CONTRIBUTING.md

[vocabulary url]: https://github.com/valkyrjaio/.github/blob/master/VOCABULARY.md

[security vulnerabilities url]: https://github.com/valkyrjaio/.github/blob/master/SECURITY.md

[MIT license url]: https://opensource.org/licenses/MIT
