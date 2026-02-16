# MetaEngine OpenAPI Angular

[![npm version](https://img.shields.io/npm/v/@metaengine/openapi-angular.svg)](https://www.npmjs.com/package/@metaengine/openapi-angular)
[![npm downloads](https://img.shields.io/npm/dm/@metaengine/openapi-angular.svg)](https://www.npmjs.com/package/@metaengine/openapi-angular)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**Generate TypeScript models and Angular services from OpenAPI/Swagger specifications.**

Perfect for API-first development workflows where your API contract drives the implementation.

---

## Quick Links

- **NPM Package**: [@metaengine/openapi-angular](https://www.npmjs.com/package/@metaengine/openapi-angular)
- **NuGet Package**: [MetaEngine.TypeScript.OpenApi.Angular](https://www.nuget.org/packages/MetaEngine.TypeScript.OpenApi.Angular)
- **Website**: [metaengine.eu/packages/openapi-angular](https://www.metaengine.eu/packages/openapi-angular)

---

## Features

- ✅ **Angular 14+** - Modern Angular with RxJS observables
- ✅ **httpResource (Angular 19.2+)** - Signal-based data fetching with automatic request management
- ✅ **TypeScript** - Fully typed API clients and models
- ✅ **Dependency Injection** - Injectable services with configurable scope
- ✅ **HttpClient Integration** - Native Angular HTTP client
- ✅ **Error Handling** - Smart error handling with interceptors
- ✅ **Modern inject()** - Support for functional injection (Angular 14+)
- ✅ **Tree-shakeable** - Optimized bundle size with separate files

---

## Installation

```bash
npm install --save-dev @metaengine/openapi-angular
```

Or use directly with npx:

```bash
npx @metaengine/openapi-angular <input> <output>
```

---

## Requirements

- Node.js 18.0 or later
- .NET 8.0 or later runtime ([Download](https://dotnet.microsoft.com/download))
- Angular 14.0 or later (for generated code)

---

## Quick Start

### Recommended Setup

```bash
npx @metaengine/openapi-angular api.yaml ./src/app/api \
  --inject-function \
  --error-handling \
  --documentation
```

### With npm scripts

Add to your `package.json`:

```json
{
  "scripts": {
    "generate:api": "metaengine-openapi-angular api.yaml ./src/app/api --inject-function --error-handling --documentation"
  }
}
```

Then run:
```bash
npm run generate:api
```

### More Examples

```bash
# From URL
npx @metaengine/openapi-angular https://api.example.com/openapi.json ./src/app/api

# Filter by OpenAPI tags
npx @metaengine/openapi-angular api.yaml ./src/app/api --include-tags users,auth

# With httpResource (Angular 19.2+)
npx @metaengine/openapi-angular api.yaml ./src/app/api --http-resource --inject-function

# With lazy loading (trigger signal for on-demand resource loading)
npx @metaengine/openapi-angular api.yaml ./src/app/api --lazy-resource --inject-function

# Custom DI scope
npx @metaengine/openapi-angular api.yaml ./src/app/api --provided-in any
```

---

## CLI Options

| Option | Description | Default |
|--------|-------------|---------|
| `--include-tags <tags>` | Filter by OpenAPI tags (comma-separated, case-insensitive) | - |
| `--provided-in <value>` | Angular injection scope (root, any, platform) | - |
| `--base-url-token <name>` | Injection token name for base URL | `BASE_URL` |
| `--options-threshold <n>` | Parameter count for options object | `4` |
| `--documentation` | Generate JSDoc comments | `false` |
| `--inject-function` | Use inject() instead of constructor injection | `false` |
| `--http-resource` | Use httpResource with Signals for GET operations (Angular 19.2+) | `false` |
| `--lazy-resource` | Add trigger signal for lazy loading (implies `--http-resource`) | `false` |
| `--date-transformation` | Convert date strings in responses to Date objects | `false` |
| `--error-handling` | Enable smart error handling | `false` |
| `--strict-validation` | Enable strict OpenAPI validation | `false` |
| `--clean` | Remove files no longer part of generation while preserving unchanged files (smart VCS-friendly cleanup) | `false` |
| `--verbose` | Enable verbose logging | `false` |
| `--help, -h` | Show help message | - |

---

## Generated Code Structure

```
output/
  ├── models/                    # One file per model
  │   ├── user.ts               # export interface User { ... }
  │   ├── product.ts
  │   └── ...
  ├── services/                  # One file per service/tag
  │   ├── users.service.ts      # UsersService with all user operations
  │   ├── products.service.ts
  │   ├── base-api.service.ts   # Base service with common functionality
  │   └── ...
  ├── alias-types.ts            # Type aliases from OpenAPI spec
  ├── dictionary-types.ts       # Dictionary/map types
  └── union-types.ts            # Union types for complex schemas
```

---

## httpResource Support (Angular 19.2+)

When using `--http-resource`, the generator creates Signal-based resource methods alongside traditional Observable methods for GET operations:

```typescript
import { Injectable, Signal } from '@angular/core';
import { httpResource, HttpResourceRef } from '@angular/common/http';
import { User } from '../models/user';

@Injectable({ providedIn: 'root' })
export class UsersService extends BaseApiService {

  // Traditional Observable method (always available)
  getUser(userId: string): Observable<User> {
    return this.createRequest(() => this.http.get<User>(
      this.buildUrl(`/users/${userId}`)
    ), 'getUser');
  }

  // Signal-based httpResource method (with --http-resource flag)
  getUserResource(userId: Signal<string | undefined>): HttpResourceRef<User | undefined> {
    return httpResource<User>(() => {
      const userIdValue = userId();
      if (typeof userIdValue === 'undefined') return undefined;

      return { url: this.buildUrl(`/users/${userIdValue}`) };
    });
  }
}
```

**Benefits:**
- Automatic request lifecycle management
- Reactive updates when Signal inputs change
- Prevents requests when required parameters are undefined
- Perfect for forms with initially undefined inputs

**Usage in components:**
```typescript
export class UserDetailsComponent {
  private userIdSignal = signal<string | undefined>(undefined);

  // Automatically refetches when userIdSignal changes
  userResource = inject(UsersService).getUserResource(this.userIdSignal);

  loadUser(id: string) {
    this.userIdSignal.set(id);  // Triggers automatic refetch
  }
}
```

---

## Lazy Loading with `--lazy-resource`

By default, parameterless GET endpoints fire immediately because there are no signal dependencies.
With `--lazy-resource`, all httpResource methods get an optional `trigger` signal parameter that
controls when the resource loads:

```bash
npx @metaengine/openapi-angular api.yaml ./src/app/api --lazy-resource
```

```typescript
// Parameterless endpoint — stays idle until trigger fires
getAccountsResource(trigger?: Signal<unknown>): HttpResourceRef<Account[]> {
  return httpResource<Account[]>(
    () => {
      if (trigger && trigger() === undefined) {
        return undefined;
      }
      return { url: this.buildUrl(`/api/accounts`) };
    },
    { defaultValue: [] });
}

// Parameterized endpoint — trigger appended after existing params
getUserByIdResource(id: Signal<number | undefined>, trigger?: Signal<unknown>): HttpResourceRef<User | undefined> {
  return httpResource<User>(
    () => {
      const idValue = id();
      if (trigger && trigger() === undefined) return undefined;
      if (typeof idValue === 'undefined') return undefined;
      return { url: this.buildUrl(`/api/users/${idValue}`) };
    });
}
```

**Usage — lazy load on demand:**
```typescript
export class AccountsComponent {
  private loadTrigger = signal<unknown>(undefined);

  // Resource stays idle until trigger fires
  accountsResource = inject(AccountsService).getAccountsResource(this.loadTrigger);

  onCompanySelected() {
    this.loadTrigger.set(true);  // Triggers the HTTP request
  }
}
```

Without the trigger parameter (or when omitted), methods behave exactly as before.

---

## Programmatic Usage

The NuGet package allows programmatic use in .NET projects. See the [website documentation](https://www.metaengine.eu/packages/openapi-angular) for full C# API reference.

---

## Support

- **Issues**: [GitHub Issues](https://github.com/meta-engine/openapi-angular/issues)
- **Email**: info@metaengine.eu
- **Website**: [metaengine.eu](https://www.metaengine.eu)

---

## License

MIT License - see [LICENSE](./LICENSE) file for details.

---

## About This Repository

This is the **documentation and issue tracking repository** for MetaEngine OpenAPI Angular. The compiled NPM package is available at [@metaengine/openapi-angular](https://www.npmjs.com/package/@metaengine/openapi-angular).

Source code is proprietary, but the package is free to use under MIT license.
