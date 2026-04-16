<p align="center"><a href="https://valkyrja.io" target="_blank">
    <img src="https://raw.githubusercontent.com/valkyrjaio/art/refs/heads/master/full-logo/orange/php.png" width="400">
</a></p>

# Valkyrja Psalm

Psalm configuration for the Valkyrja project.

<p>
    <a href="https://packagist.org/packages/valkyrja/psalm"><img src="https://poser.pugx.org/valkyrja/psalm/require/php" alt="PHP Version Require"></a>
    <a href="https://packagist.org/packages/valkyrja/psalm"><img src="https://poser.pugx.org/valkyrja/psalm/v" alt="Latest Stable Version"></a>
    <a href="https://packagist.org/packages/valkyrja/psalm"><img src="https://poser.pugx.org/valkyrja/psalm/license" alt="License"></a>
    <!-- <a href="https://packagist.org/packages/valkyrja/psalm"><img src="https://poser.pugx.org/valkyrja/psalm/downloads" alt="Total Downloads"></a>-->
    <a href="https://scrutinizer-ci.com/g/valkyrjaio/psalm/?branch=master"><img src="https://scrutinizer-ci.com/g/valkyrjaio/psalm/badges/quality-score.png?b=master" alt="Scrutinizer"></a>
    <a href="https://coveralls.io/github/valkyrjaio/psalm?branch=master"><img src="https://coveralls.io/repos/github/valkyrjaio/psalm/badge.svg?branch=master" alt="Coverage Status" /></a>
    <a href="https://shepherd.dev/github/valkyrjaio/psalm"><img src="https://shepherd.dev/github/valkyrjaio/psalm/coverage.svg" alt="Psalm Shepherd" /></a>
    <a href="https://sonarcloud.io/summary/new_code?id=valkyrjaio_psalm"><img src="https://sonarcloud.io/api/project_badges/measure?project=valkyrjaio_psalm&metric=sqale_rating" alt="Maintainability Rating" /></a>
</p>

Build Status
------------

<table>
    <tbody>
        <tr>
            <td>Linting</td>
            <td>
                <a href="https://github.com/valkyrjaio/psalm/actions/workflows/phpcodesniffer.yml?query=branch%3Amaster"><img src="https://github.com/valkyrjaio/psalm/actions/workflows/phpcodesniffer.yml/badge.svg?branch=master" alt="PHP Code Sniffer Build Status"></a>
            </td>
            <td>
                <a href="https://github.com/valkyrjaio/psalm/actions/workflows/phpcsfixer.yml?query=branch%3Amaster"><img src="https://github.com/valkyrjaio/psalm/actions/workflows/phpcsfixer.yml/badge.svg?branch=master" alt="PHP CS Fixer Build Status"></a>
            </td>
        </tr>
        <tr>
            <td>Coding Rules</td>
            <td>
                <a href="https://github.com/valkyrjaio/psalm/actions/workflows/phparkitect.yml?query=branch%3Amaster"><img src="https://github.com/valkyrjaio/psalm/actions/workflows/phparkitect.yml/badge.svg?branch=master" alt="PHPArkitect Build Status"></a>
            </td>
            <td>
                <a href="https://github.com/valkyrjaio/psalm/actions/workflows/rector.yml?query=branch%3Amaster"><img src="https://github.com/valkyrjaio/psalm/actions/workflows/rector.yml/badge.svg?branch=master" alt="Rector Build Status"></a>
            </td>
        </tr>
        <tr>
            <td>Static Analysis</td>
            <td>
                <a href="https://github.com/valkyrjaio/psalm/actions/workflows/phpstan.yml?query=branch%3Amaster"><img src="https://github.com/valkyrjaio/psalm/actions/workflows/phpstan.yml/badge.svg?branch=master" alt="PHPStan Build Status"></a>
            </td>
            <td>
                <a href="https://github.com/valkyrjaio/psalm/actions/workflows/psalm.yml?query=branch%3Amaster"><img src="https://github.com/valkyrjaio/psalm/actions/workflows/psalm.yml/badge.svg?branch=master" alt="Psalm Build Status"></a>
            </td>
        </tr>
        <tr>
            <td>Testing</td>
            <td>
                <a href="https://github.com/valkyrjaio/psalm/actions/workflows/phpunit.yml?query=branch%3Amaster"><img src="https://github.com/valkyrjaio/psalm/actions/workflows/phpunit.yml/badge.svg?branch=master" alt="PHPUnit Build Status"></a>
            </td>
            <td></td>
        </tr>
    </tbody>
</table>

## Usage

Place a `psalm.xml` in your CI directory pointing to the project source. Run
via the root Composer scripts:

```bash
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

## Configuration

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

- `PSALM_ALLOW_XDEBUG=1` is set in CI as a workaround for a JIT interaction with
  the `#[Override]` attribute.
  See [vimeo/psalm#11723](https://github.com/vimeo/psalm/issues/11723).
- An `autoload.php` is required in the CI directory to bootstrap the project
  autoloader before Psalm analyses the source.
