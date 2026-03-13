# Changelog

See the [npm releases page](https://www.npmjs.com/package/@metaengine/openapi-angular?activeTab=versions) for detailed version history and changes.

## 1.0.5

### Bug Fixes
- **Fixed: Optional path parameters leaving raw placeholders in URL** — When a parameter is marked as `in: query` in the OpenAPI spec but its name still appears as `{placeholder}` in the URL path (common with C# optional path parameters like `{param?}`), the placeholder is now correctly removed from the URL and the parameter is handled as a query parameter ([#2](https://github.com/meta-engine/openapi-angular/issues/2))
- **Fixed: Cookie parameters incorrectly treated as query parameters** — Parameters with `in: cookie` are now properly filtered out instead of being silently mapped to query parameters

### Improvements
- **Style/Explode parameter serialization** — Added support for `explode: false` (comma-separated arrays) and `style: deepObject` (bracket notation like `?filter[name]=value`) query parameter serialization
- **FormData/multipart support** — `multipart/form-data` request bodies now generate proper `FormData` handling with file and field support
- **Binary and text/plain responses** — Full support for binary response types (`application/octet-stream`) and `text/plain` response content types
- **ReadOnly/WriteOnly fields** — Proper support for OpenAPI `readOnly` and `writeOnly` field markers
- **FreeForm to Dictionary** — Schemas with untyped `additionalProperties` are now correctly classified as `Record<string, unknown>` instead of opaque freeform objects

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
