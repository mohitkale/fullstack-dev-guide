# 18. Advanced Declaration File Authoring (.d.ts)

In Tutorial #12, we covered the fundamentals of TypeScript declaration files (`.d.ts`). This tutorial dives deeper into advanced techniques for authoring robust and accurate declaration files, enabling seamless integration of JavaScript libraries into TypeScript projects and effective type sharing for your own TypeScript libraries.

## Table of Contents
1.  [Recap: Basics of Declaration Files](#recap-dts-basics)
2.  [Module Augmentation](#module-augmentation)
    *   Adding New Exports to an Existing Module
    *   Augmenting Existing Interfaces/Classes within a Module
3.  [Global Augmentation](#global-augmentation)
    *   Using `declare global {}`
    *   Adding New Global Variables, Functions, or Types
    *   Augmenting Existing Global Interfaces (e.g., `Window`, `Array.prototype`)
4.  [Structuring Declarations for Different Library Types](#structuring-declarations)
    *   ES Modules / CommonJS Modules (`declare module "module-name"`)
    *   UMD (Universal Module Definition) Libraries (`export as namespace ...`)
    *   Global Libraries / Plugins (Script Tags)
5.  [Publishing Declaration Files](#publishing-declarations)
    *   Bundling with Your npm Package (`types` or `typings` in `package.json`)
    *   Contributing to DefinitelyTyped (`@types/package-name`)
6.  [Best Practices for Advanced `.d.ts` Files](#best-practices-dts)
    *   Accuracy, Completeness, and Maintainability
    *   Minimize `any`; Use Specific Types
    *   Testing Your Declarations
    *   Referencing Other Types and Libraries (`/// <reference types="..." />`)
    *   Correct Use of `export =` vs. `export default`
    *   Handling Overloads for Functions and Methods
7.  [Common Pitfalls and Solutions](#common-pitfalls-dts)
    *   Incorrect Module Name in `declare module`
    *   Conflicts with Existing Declarations
    *   Over-Augmentation or Incorrect Augmentation
8.  [Example: Augmenting a Third-Party Library](#example-augmenting-library)
9.  [Key Takeaways](#key-takeaways-advanced-dts)

## 1. Recap: Basics of Declaration Files <a name="recap-dts-basics"></a>
Declaration files (`.d.ts`) describe the shape of existing JavaScript code for the TypeScript compiler. They contain type information but no implementations.
*   **Ambient Declarations:** Using `declare` keyword (e.g., `declare var`, `declare function`, `declare class`, `declare module`).
*   **Global Declarations:** For libraries loaded via `<script>` tags that add symbols to the global scope.
*   **Module Declarations:** For libraries consumed as ES modules or CommonJS modules.
*   **Generating `.d.ts` files:** Using the `declaration: true` compiler option for your TypeScript projects.

## 2. Module Augmentation <a name="module-augmentation"></a>
Module augmentation allows you to extend existing JavaScript modules by adding to their declared types from within your own TypeScript files. This is useful when a library is missing types for certain features or when you want to add custom extensions.

To augment a module, you use `declare module "module-name" {}` where `"module-name"` is the same string you'd use in an `import` statement.

### Adding New Exports to an Existing Module
```typescript
// original-module.d.ts (simulating an external module's types)
declare module "my-external-library" {
  export function existingFunction(): void;
  export interface ExistingInterface { prop: string; }
}

// my-project/augmentations.ts
import { ExistingInterface } from "my-external-library"; // Import to ensure module context

declare module "my-external-library" {
  // Add a new function
  export function newFunction(config: ExistingInterface): string;

  // Add a new interface
  export interface NewInterface {
    id: number;
  }
}

// my-project/consumer.ts
import { existingFunction, newFunction, NewInterface } from "my-external-library";

existingFunction();
let myNewConfig: NewInterface = { id: 123 };
// newFunction({ prop: 'test' }); // Error: newFunction expects ExistingInterface, but we can use it
```
**Important:** Your augmentation file must be a module (e.g., have at least one `import` or `export` statement at the top level, even if it's `export {};`).

### Augmenting Existing Interfaces/Classes within a Module
Declaration merging allows you to add members to existing interfaces or classes within the augmented module.

```typescript
// my-project/augmentations.ts
declare module "my-external-library" {
  // Augment an existing interface
  interface ExistingInterface {
    addedProp?: boolean; // Add an optional property
  }

  // Note: Augmenting classes is more complex and usually involves augmenting their prototype
  // or static side, often through interface merging if the class implements an interface.
  // Direct class method addition via augmentation is not straightforward.
}

// my-project/consumer.ts
import { ExistingInterface } from "my-external-library";

let obj: ExistingInterface = { prop: "hello" };
obj.addedProp = true; // This property is now available
```

## 3. Global Augmentation <a name="global-augmentation"></a>
Global augmentation allows you to add declarations to the global scope or modify existing global types. This is useful for polyfills, extending built-in JavaScript objects, or describing global variables provided by scripts.

### Using `declare global {}`
To add to the global scope from within a module (a file with `import` or `export`), you must use `declare global {}`.

```typescript
// my-project/global-augmentations.ts
export {}; // Make this file a module

declare global {
  interface Window {
    myAppConfig: { apiKey: string; };
  }

  interface Array<T> {
    customMap<U>(callbackfn: (value: T, index: number, array: T[]) => U, thisArg?: any): U[];
  }

  var myGlobalVar: number;
  function myGlobalFunction(): void;
}

// Usage in another file in your project:
window.myAppConfig = { apiKey: '123' };
const arr = [1, 2, 3];
arr.customMap(x => x.toString());
myGlobalVar = 100;
myGlobalFunction();
```
If your augmentation file is *not* a module (no top-level `import`/`export`), you can declare globals directly without `declare global {}`.

### Adding New Global Variables, Functions, or Types
As shown above, you can declare new global `var`, `let`, `const`, `function`, `class`, `interface`, `type`, or `enum` within the `declare global {}` block (if in a module) or directly at the top level (if in a global script file).

### Augmenting Existing Global Interfaces (e.g., `Window`, `Array.prototype`)
Interfaces in the global scope (like `Window`, `Array<T>`, `String`, `Number`) can be augmented to add new properties or methods. This is how libraries like Lodash might add methods to `Array.prototype` (though this practice, known as prototype pollution, is often discouraged for application code but common for polyfills or utility libraries providing such extensions).

## 4. Structuring Declarations for Different Library Types <a name="structuring-declarations"></a>
How you structure your `.d.ts` file depends on how the JavaScript library is intended to be consumed.

### ES Modules / CommonJS Modules (`declare module "module-name"`)
Most modern libraries are distributed as ES Modules or CommonJS modules.
```typescript
// my-lib.d.ts
declare module "my-lib" {
  export function greet(name: string): string;
  export interface Options { /* ... */ }
  const version: string;
  export default class MyLibClass { /* ... */ }
}
```
*   Use `export` for named exports.
*   Use `export default` for the default export.
*   Use `export =` for modules that export a single entity via `module.exports = ...` (older CommonJS pattern).

### UMD (Universal Module Definition) Libraries (`export as namespace ...`)
UMD libraries can be used as modules (`import`) or as globals (if loaded via a `<script>` tag).
```typescript
// my-umd-lib.d.ts
declare module "my-umd-lib" {
  // Exports for module consumers (import)
  export function process(): void;
  export interface Config { /* ... */ }

  // This makes the module's exports available under a global namespace
  // when used via a <script> tag.
  export as namespace MyUMDLib;
}

// If used via <script>, you can access: MyUMDLib.process()
```
If the UMD library also exposes globals directly (not just under a namespace), you might need global declarations too.

### Global Libraries / Plugins (Script Tags)
For libraries that only modify the global scope or attach themselves to global objects (like jQuery plugins).
```typescript
// jquery-plugin.d.ts

// Augment the existing jQuery interface if jQuery types are available
interface JQuery {
  myPlugin(options?: MyPluginOptions): JQuery;
}

interface MyPluginOptions {
  settingA?: boolean;
}

// If the plugin creates its own global variable:
declare var MyGlobalPlugin: {
  init(): void;
};
```
If the global library exposes a complex API, you might use `declare namespace` to group its types.

## 5. Publishing Declaration Files <a name="publishing-declarations"></a>

### Bundling with Your npm Package (`types` or `typings` in `package.json`)
If you are the author of a TypeScript library or a JavaScript library with accompanying `.d.ts` files:
1.  Ensure your `tsconfig.json` has `"declaration": true`.
2.  Specify the path to your main declaration file in `package.json`:
    ```json
    {
      "name": "my-awesome-library",
      "version": "1.0.0",
      "main": "dist/index.js",
      "types": "dist/index.d.ts", // or "typings": "dist/index.d.ts"
      "files": ["dist"]
    }
    ```
    TypeScript will automatically find and use these types when users install your package.

### Contributing to DefinitelyTyped (`@types/package-name`)
If you want to provide types for a JavaScript library you don't own, the best place is [DefinitelyTyped](https://definitelytyped.org/).
1.  Fork the DefinitelyTyped repository.
2.  Create a new directory under `types/` for the library (e.g., `types/some-js-library`).
3.  Add your `.d.ts` file(s), a `tsconfig.json`, a `tslint.json` (or `eslint.json`), and test files.
4.  Follow their contribution guidelines for testing and submitting a Pull Request.
Once merged, users can install types via `npm install @types/some-js-library`.

## 6. Best Practices for Advanced `.d.ts` Files <a name="best-practices-dts"></a>
*   **Accuracy, Completeness, and Maintainability:** Strive to accurately reflect the JavaScript library's API. Keep declarations up-to-date with library versions.
*   **Minimize `any`; Use Specific Types:** Avoid `any` as much as possible. Define specific interfaces and types. Use `unknown` if a type is truly unknown and force consumers to perform type checks.
*   **Testing Your Declarations:** Write TypeScript code that uses your `.d.ts` files to ensure they work correctly and catch errors. DefinitelyTyped has a robust testing infrastructure.
*   **Referencing Other Types and Libraries:**
    *   Use `import type { ... } from "dependency-module";` for type-only imports within your `.d.ts` files if they are module declarations.
    *   Use `/// <reference types="node" />` for referencing built-in Node.js types or other `@types` packages if needed, especially in global `.d.ts` files or UMD declarations.
*   **Correct Use of `export =` vs. `export default`:**
    *   `export = MyVariable;` is for CommonJS modules that assign directly to `module.exports` (e.g., `module.exports = function() {};`). TypeScript projects consuming these need to use `import lib = require('lib');`.
    *   `export default ...;` is for ES6 default exports.
*   **Handling Overloads for Functions and Methods:** Define multiple `declare function` or method signatures for overloaded functions to provide accurate type checking for different call patterns.

## 7. Common Pitfalls and Solutions <a name="common-pitfalls-dts"></a>
*   **Incorrect Module Name in `declare module`:** Ensure the string literal exactly matches how the module is imported (e.g., `"lodash"`, not `"Lodash"`).
*   **Conflicts with Existing Declarations:** If multiple `.d.ts` files try to declare the same module or global, it can lead to conflicts. This often happens with outdated or misconfigured `@types` packages.
*   **Over-Augmentation or Incorrect Augmentation:** Be careful not to change the fundamental behavior of existing types in a way that misrepresents the library. Augmentations should add to or refine existing types.
*   **Forgetting `export {}` in Augmentation Files:** If your file contains augmentations for modules or `declare global`, it usually needs to be treated as a module itself. Adding `export {};` (if no other top-level exports exist) achieves this.

## 8. Example: Augmenting a Third-Party Library (e.g., Express) <a name="example-augmenting-library"></a>
Let's say you want to add a custom property to the Express `Request` object.

```typescript
// my-project/express-aug.d.ts

// Ensure this file is treated as a module
export {};

declare global {
  namespace Express {
    // Augment the existing Request interface
    export interface Request {
      userContext?: {
        userId: string;
        roles: string[];
      };
    }
  }
}
```
To make this augmentation apply, ensure this `.d.ts` file is included in your project's compilation (e.g., via `tsconfig.json`'s `include` array or by being in the same directory structure).

```typescript
// my-project/server.ts
import express from 'express';

const app = express();

app.use((req, res, next) => {
  // Now you can safely access req.userContext
  if (req.headers.authorization) {
    // Simulate fetching user context
    req.userContext = { userId: 'user123', roles: ['admin'] };
  }
  next();
});

app.get('/', (req, res) => {
  if (req.userContext) {
    res.send(`Hello, ${req.userContext.userId}`);
  } else {
    res.send('Hello, guest!');
  }
});

app.listen(3000, () => console.log('Server started'));
```

## 9. Key Takeaways <a name="key-takeaways-advanced-dts"></a>
*   Module and global augmentation provide powerful ways to extend and describe types for existing JavaScript code.
*   The structure of your `.d.ts` file must match the library's module format (ESM, CommonJS, UMD, global).
*   Publishing types with your package (`"types"` in `package.json`) or via DefinitelyTyped is crucial for the TypeScript ecosystem.
*   Prioritize accuracy, specificity (avoid `any`), and testing when authoring declaration files.
*   `declare global {}` is used to modify the global scope from within a module.

Mastering advanced declaration file authoring is key to bridging the gap between JavaScript and TypeScript, enabling robust type safety across your entire codebase, even when interacting with external JavaScript libraries.

---

⬅️ [Back to TypeScript Namespaces vs Modules (Revisited Deep Dive)](../17-ts-namespaces-vs-modules-revisited/README.md) | ➡️ [Next: TypeScript Build Tools and Integration](../19-ts-build-tools-integration/README.md) *(Placeholder)*
