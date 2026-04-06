# Changelog

See the [npm releases page](https://www.npmjs.com/package/@metaengine/openapi-angular?activeTab=versions) for detailed version history and changes.

## 1.0.8

### New Feature
- **New: `--interceptors` flag** — Generates an `api-interceptors.ts` file with Angular-native HTTP interceptor infrastructure:
  - `ApiMiddleware` type alias for `HttpInterceptorFn`
  - `provideApiInterceptors(...interceptors)` factory that returns `EnvironmentProviders` for the Angular DI providers array

### Bug Fixes
- **Fixed: `Array<Blob>` FormData serialization corrupts binary data** — The generated FormData builder used `JSON.stringify()` on array fields, corrupting binary data. Now detects arrays via `Array.isArray()` and appends each element individually, matching standard multipart file upload behavior. Also fixes the model type mapping: `type: array` with `items: { format: binary }` now correctly generates `Array<Blob>` instead of `Array<string>` ([#5](https://github.com/meta-engine/openapi-angular/issues/5))
- **Fixed: FormData `for...of` iteration causes TS2358** — `Object.entries(body)` returned a specific union type where `instanceof Blob` failed on primitive members. Added `[string, unknown]` cast to restore proper type narrowing
- **Fixed: FormData indentation drift** — Switched from `forEach` callbacks to `for...of` loops; the `forEach(... => {` / `});` brace pattern caused the formatter to miscalculate indentation levels
- **Fixed: discriminated unions without explicit `discriminator.mapping`** — When an OpenAPI schema uses `discriminator.propertyName` without a mapping object, the mapping is now inferred from `oneOf` `$ref` names per the OpenAPI 3.0 spec

## 1.0.7

### Bug Fixes
- **Fixed: `format: binary` properties mapped to `string` instead of `Blob`** — Multipart form-data file fields (C# `IFormFile`) now correctly generate as `Blob` in TypeScript interfaces. Since `File extends Blob`, both `File` objects from `<input type="file">` and programmatic `Blob` instances are accepted ([#4](https://github.com/meta-engine/openapi-angular/issues/4))
- **Fixed: `instanceof Blob` check in FormData builder** — `Object.entries()` value typed as `unknown` so `instanceof Blob` works without type narrowing issues
- **Fixed: httpResource URL path placeholders** — Unresolved path parameters (e.g. `{expiredOnly}`) in httpResource methods are now correctly handled via the shared URL template generator
- **Fixed: false "Circular reference detected" comments on union types** — oneOf/union types no longer emit incorrect circular reference warnings

### httpResource Improvements
- **deepObject and explode:false query parameters** — httpResource methods now support `style: deepObject` (bracket notation) and `explode: false` (comma-separated arrays), matching Observable method behavior
- **Header parameter support** — httpResource methods now include header parameters in the request object
- **Skip binary/text response GETs** — httpResource methods are no longer generated for endpoints returning binary or text/plain responses, since Angular's `httpResource` API doesn't support `responseType` — Observable methods remain available for these

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
