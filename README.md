<p align="center"><a href="https://valkyrja.io" target="_blank">
    <img src="https://raw.githubusercontent.com/valkyrjaio/art/refs/heads/master/full-logo/orange/php.png" width="400">
</a></p>

# Valkyrja Psalm

Psalm configuration for the Valkyrja project.

## Requirements

- PHP >= 8.4
- [`vimeo/psalm`](https://github.com/vimeo/psalm)
  ^6.16.1

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
