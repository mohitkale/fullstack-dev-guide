# 09. TypeScript Modules and Namespaces

TypeScript, like JavaScript, needs mechanisms to organize code into logical units, manage dependencies, and prevent naming conflicts in the global scope. This is primarily achieved through **Modules** (aligning with ES Modules standard) and, to a lesser extent for modern development, **Namespaces** (TypeScript's original way of organizing code, formerly known as "internal modules").

This tutorial explores both concepts.

## Table of Contents
1.  [Modules (ES Modules)](#modules-es)
    *   [What are Modules?](#what-are-modules)
    *   [Exporting Declarations](#exporting-declarations)
        *   [Named Exports](#named-exports)
        *   [Default Exports](#default-exports)
        *   [Re-exports](#re-exports)
    *   [Importing Declarations](#importing-declarations)
        *   [Named Imports](#named-imports)
        *   [Default Imports](#default-imports)
        *   [Namespace Imports (`import * as name`)](#namespace-imports)
        *   [Imports for Side Effects Only](#side-effect-imports)
    *   [Module Resolution](#module-resolution)
    *   [Dynamic Imports (`import()`)](#dynamic-imports)
    *   [Ambient Modules (`declare module "..."`)](#ambient-modules)
    *   [TypeScript Specific Module Features (`.ts` extensions)](#ts-module-features)
2.  [Namespaces](#namespaces)
    *   [What are Namespaces?](#what-are-namespaces)
    *   [Basic Namespace Usage](#basic-namespace-usage)
    *   [Exporting from Namespaces](#exporting-from-namespaces)
    *   [Multi-file Namespaces and `/// <reference path="..." />`](#multi-file-namespaces)
    *   [Aliases for Namespaces](#aliases-for-namespaces)
3.  [Modules vs. Namespaces](#modules-vs-namespaces)
    *   [Key Differences](#key-differences-modules-namespaces)
    *   [When to Use Which](#when-to-use-which)
4.  [Key Takeaways](#key-takeaways-modules-namespaces)

## 1. Modules (ES Modules) <a name="modules-es"></a>

### What are Modules? <a name="what-are-modules"></a>
In TypeScript (and modern JavaScript), each file is its own module. Modules have their own scope; variables, functions, classes, etc., declared in a module are not visible outside the module unless they are explicitly **exported**.

To use an exported declaration from another module, you must **import** it.

Modules are the recommended way to structure code in TypeScript for most applications, especially when working with frameworks like Node.js, React, Angular, or Vue.

### Exporting Declarations <a name="exporting-declarations"></a>
Any declaration (variable, function, class, type alias, interface) can be exported using the `export` keyword.

#### Named Exports <a name="named-exports"></a>
You can have multiple named exports from a single module.

```typescript
// utils.ts
export const PI = 3.14159;

export function add(x: number, y: number): number {
  return x + y;
}

export interface Point {
  x: number;
  y: number;
}

// Can also export at the end
class Helper {}
const MAX_USERS = 100;
// export { Helper, MAX_USERS }; // This would also work
```

#### Default Exports <a name="default-exports"></a>
A module can have one default export. This is often used for the main functionality or class of a module.

```typescript
// MyClass.ts
export default class MyClass {
  constructor() {
    console.log("MyClass instance created");
  }
  greet() {
    return "Hello from MyClass";
  }
}

// Another example: a function as default export
// export default function myFunc() { /* ... */ }
```
Classes and functions are common default exports. You can also export a value (e.g., an object or a primitive) as default.

#### Re-exports <a name="re-exports"></a>
Modules can re-export declarations from other modules, which can be useful for creating a single entry point for multiple internal modules (barrel files).

```typescript
// shapes.ts
export class Circle { /* ... */ }
export class Square { /* ... */ }

// drawing.ts
export { Circle, Square } from "./shapes"; // Re-exporting named exports
export { default as MyUtility } from "./utility-module"; // Re-exporting a default export as named
```

### Importing Declarations <a name="importing-declarations"></a>

#### Named Imports <a name="named-imports"></a>
Import specific declarations using their exported names.

```typescript
// main.ts
import { PI, add, Point } from "./utils";

console.log(PI);
let sum: number = add(10, 5);
let p: Point = { x: 1, y: 2 };

// Can alias imports
import { add as sumNumbers } from "./utils";
console.log(sumNumbers(3, 4));
```

#### Default Imports <a name="default-imports"></a>
Import the default export. You can choose any name for the import.

```typescript
// app.ts
import MyMainClass from "./MyClass"; // MyMainClass is the chosen name for the default export

const instance = new MyMainClass();
console.log(instance.greet());

// You can import both default and named exports from the same module:
// Assuming MyClass.ts also had: export const version = "1.0";
// import MyMainClass, { version } from "./MyClass";
```

#### Namespace Imports (`import * as name`) <a name="namespace-imports"></a>
Import everything exported from a module into a single namespace object.

```typescript
// main.ts
import * as Utils from "./utils";

console.log(Utils.PI);
let total: number = Utils.add(20, 5);
let pt: Utils.Point = { x: 3, y: 4 };
```

#### Imports for Side Effects Only <a name="side-effect-imports"></a>
Sometimes you might want to import a module only for its side effects (e.g., it modifies global state or runs some initialization code) without importing any specific values.

```typescript
// polyfill.ts
// console.log("Polyfill loaded and executed.");
// Array.prototype.customMethod = function() { /* ... */ };

// main.ts
import "./polyfill"; // Imports the module for its side effects
```

### Module Resolution <a name="module-resolution"></a>
TypeScript uses a module resolution strategy to find modules. This is configured in `tsconfig.json` via the `moduleResolution` option (commonly `"node"` or `"classic"`). The `module` option (e.g., `"ESNext"`, `"CommonJS"`) determines the JavaScript module format of the output code.

### Dynamic Imports (`import()`) <a name="dynamic-imports"></a>
ES dynamic `import()` expressions allow you to load modules on demand. This returns a Promise.

```typescript
async function loadModule() {
  if (Math.random() > 0.5) {
    const mathUtils = await import("./utils"); // utils.ts
    console.log(mathUtils.add(5, 5));
  } else {
    const MyClassModule = await import("./MyClass"); // MyClass.ts
    const instance = new MyClassModule.default(); // Access default export
    console.log(instance.greet());
  }
}

loadModule();
```
This is useful for code splitting and lazy loading parts of your application.

### Ambient Modules (`declare module "..."`) <a name="ambient-modules"></a>
When working with JavaScript libraries that don't have TypeScript typings, or for modules that exist at runtime but aren't standard ES modules (e.g., some global libraries or custom module loaders), you can declare an ambient module.

```typescript
// typings/my-untyped-library.d.ts
declare module "my-untyped-library" {
  export function doSomething(input: string): string;
  export const version: string;
}

// app.ts
import { doSomething, version } from "my-untyped-library";
console.log(doSomething("test"));
console.log(version);
```
This tells TypeScript about the shape of the module.

### TypeScript Specific Module Features (`.ts` extensions) <a name="ts-module-features"></a>
While standard ES modules don't use file extensions in import paths, TypeScript allows you to use `.ts` (or `.tsx`, `.js`, `.jsx`) in import paths if `allowImportingTsExtensions` is enabled in `tsconfig.json` (generally not recommended for library code, but can be useful in some build setups).

TypeScript also has the concept of path mapping (`paths` in `tsconfig.json`) to create aliases for module paths.

## 2. Namespaces <a name="namespaces"></a>

### What are Namespaces? <a name="what-are-namespaces"></a>
Namespaces are a TypeScript-specific way to organize code. Before ES Modules became standard, namespaces (originally called "internal modules") were used to prevent naming collisions in the global scope, especially in larger applications.

A namespace defines a scope. Declarations inside a namespace are scoped to that namespace and are not visible globally unless explicitly exported from the namespace and the namespace itself is accessible.

### Basic Namespace Usage <a name="basic-namespace-usage"></a>
```typescript
namespace MyMath {
  const PI_INTERNAL = 3.14;
  export function calculateCircumference(diameter: number): number {
    return PI_INTERNAL * diameter;
  }
  export function calculateRectangleArea(width: number, height: number): number {
    return width * height;
  }
}

// Usage
let circ = MyMath.calculateCircumference(10);
// MyMath.PI_INTERNAL; // Error: PI_INTERNAL is not exported

console.log(circ);
```

### Exporting from Namespaces <a name="exporting-from-namespaces"></a>
Use the `export` keyword to make declarations within a namespace accessible from outside.

### Multi-file Namespaces and `/// <reference path="..." />` <a name="multi-file-namespaces"></a>
Namespaces can span multiple files. To tell the TypeScript compiler about the relationship between files that contribute to the same namespace, you use triple-slash reference directives.

```typescript
// file1.ts
/// <reference path="file2.ts" />
namespace Utility {
  export function func1() { console.log("func1 from file1"); }
}

// file2.ts
namespace Utility {
  export function func2() { console.log("func2 from file2"); }
}

// main.ts
/// <reference path="file1.ts" />
/// <reference path="file2.ts" />
Utility.func1();
Utility.func2();
```
When compiling with `--outFile` (which concatenates output), these references help order the files. This approach is less common in modern TypeScript development, which favors ES Modules.

### Aliases for Namespaces <a name="aliases-for-namespaces"></a>
You can create aliases for namespaces to simplify long, nested namespace names.

```typescript
namespace Outer.Inner.TooDeep {
  export class MyItem {}
}

import ItemContainer = Outer.Inner.TooDeep;
let item = new ItemContainer.MyItem();
```

## 3. Modules vs. Namespaces <a name="modules-vs-namespaces"></a>

### Key Differences <a name="key-differences-modules-namespaces"></a>
| Feature         | Modules (ES Modules)                                  | Namespaces                                               |
|-----------------|-------------------------------------------------------|----------------------------------------------------------|
| **Standard**    | ECMAScript standard                                   | TypeScript-specific                                      |
| **File-based**  | Each file is a module                                 | Can span multiple files (using `/// <reference>`)         |
| **Scope**       | File-scoped by default                                | Namespace-scoped, can be global if not managed carefully |
| **Dependencies**| Explicit `import`/`export`                            | Implicit via `/// <reference>` or global script order    |
| **Loading**     | Handled by module loaders (e.g., Node.js, Webpack)    | Typically concatenated into a single file or rely on `<script>` tags order |
| **Modern Usage**| Preferred for new projects, library development       | Legacy, or for internal structuring within a project     |

### When to Use Which <a name="when-to-use-which"></a>
*   **Use Modules (ES Modules) by default for all new projects.** They align with JavaScript standards, integrate well with the ecosystem (npm, bundlers), and provide better encapsulation and dependency management.
*   **Use Namespaces if:**
    *   You are working with a very large codebase that is not yet migrated to modules and uses `/// <reference` and `--outFile`.
    *   You need to structure code that will be output as a single global script (e.g., for simple browser scripts not using a module loader), though even then, modern bundlers can often convert ES modules to such formats.
    *   You are writing declaration files (`.d.ts`) for libraries that expose global APIs through nested objects; namespaces can model this structure.

It's generally recommended to **avoid mixing modules and namespaces** in the same project if possible, as it can lead to confusion. If a file contains any top-level `import` or `export` statement, it is considered a module.

## 4. Key Takeaways <a name="key-takeaways-modules-namespaces"></a>
*   **Modules (ES Modules)** are the standard way to organize TypeScript code. Each file is a module with its own scope.
    *   Use `export` (named, default) to make declarations available outside.
    *   Use `import` (named, default, namespace) to consume exported declarations.
    *   Dynamic `import()` allows for lazy loading.
*   **Namespaces** are a TypeScript-specific feature for organizing code, primarily used in older codebases or for specific scenarios like global API modeling.
    *   They provide a way to group related code and avoid global namespace pollution.
    *   Can span multiple files using `/// <reference path="..." />`.
*   **Prefer ES Modules over namespaces** for modern TypeScript development due to better standardization, tooling support, and encapsulation.

Understanding how to effectively use modules is crucial for building scalable and maintainable TypeScript applications.

---

⬅️ [Back to Advanced Types](../08-ts-advanced-types/README.md) | ➡️ [Next: Decorators](../10-ts-decorators/README.md)
