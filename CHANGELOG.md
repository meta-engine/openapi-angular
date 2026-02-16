# Changelog

See the [npm releases page](https://www.npmjs.com/package/@metaengine/openapi-angular?activeTab=versions) for detailed version history and changes.

## 1.0.4

- **New: `--lazy-resource` flag** — Adds an optional `trigger?: Signal<unknown>` parameter to all generated httpResource methods, enabling lazy loading patterns where resources stay idle until explicitly triggered
- Implies `--http-resource` when used standalone

## 1.0.3

- **New: `--date-transformation` flag** — Automatically converts ISO date strings in API responses to JavaScript `Date` objects

## 1.0.2

- **New: `--http-resource` flag** — Generate Signal-based `httpResource` methods alongside traditional Observable methods for GET operations (Angular 19.2+)
- **New: `--date-transformation` flag** — Convert date strings in responses to `Date` objects

## 1.0.1

- Initial public release with full OpenAPI 3.0 support
- Angular 14+ with RxJS observables
- `--inject-function`, `--documentation`, `--error-handling`, `--strict-validation`, `--clean` flags

For the complete changelog and all previous versions, visit the npm package page.
