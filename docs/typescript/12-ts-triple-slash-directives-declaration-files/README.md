# 12. TypeScript Triple-Slash Directives and Declaration Files

TypeScript needs ways to understand the shape of JavaScript code that wasn't originally written in TypeScript. This is primarily achieved through **Declaration Files (`.d.ts` files)**. Additionally, **Triple-Slash Directives** are special comments that provide compiler instructions, often related to declaration files or older compilation methods.

## Table of Contents
1.  [Triple-Slash Directives](#triple-slash-directives)
    *   [`/// <reference path="..." />`](#ref-path)
    *   [`/// <reference types="..." />`](#ref-types)
    *   [`/// <reference lib="..." />`](#ref-lib)
    *   [Placement and Usage](#placement-usage-directives)
2.  [Declaration Files (`.d.ts`)](#declaration-files)
    *   [What are Declaration Files?](#what-are-dts-files)
    *   [Why Use Declaration Files?](#why-use-dts-files)
    *   [Authoring Declaration Files](#authoring-dts-files)
        *   [Global Variables, Functions, and Types](#global-declarations)
        *   [Module Declarations (`declare module "..."`)](#module-declarations)
        *   [UMD Module Declarations (`export as namespace`)](#umd-module-declarations)
        *   [Shorthand Ambient Modules](#shorthand-ambient-modules)
        *   [Augmenting Existing Types (Module Augmentation, Global Augmentation)](#augmenting-types)
    *   [Consuming Declaration Files](#consuming-dts-files)
        *   [Automatic Discovery](#automatic-discovery-dts)
        *   [DefinitelyTyped (`@types` packages)](#definitelytyped-types)
        *   [`typeRoots` and `types` Compiler Options](#typeroots-types-options)
    *   [Generating Declaration Files from TypeScript (`declaration: true`)](#generating-dts-files)
3.  [Relationship and Best Practices](#relationship-best-practices)
4.  [Key Takeaways](#key-takeaways-directives-dts)

## 1. Triple-Slash Directives <a name="triple-slash-directives"></a>
Triple-slash directives are single-line comments containing an XML tag. They are processed by the TypeScript compiler to include additional files or declare dependencies.

### `/// <reference path="..." />` <a name="ref-path"></a>
This directive declares a dependency between files. It's primarily used for ordering and dependency management when compiling multiple `.ts` files into a single output file using the `--outFile` compiler option (less common in modern module-based development).

```typescript
// fileA.ts
namespace Validation {
  export interface StringValidator {
    isAcceptable(s: string): boolean;
  }
}

// fileB.ts
/// <reference path="fileA.ts" />
namespace Validation {
  const lettersRegexp = /^[A-Za-z]+$/;
  export class LettersOnlyValidator implements StringValidator {
    isAcceptable(s: string) {
      return lettersRegexp.test(s);
    }
  }
}

// main.ts
/// <reference path="fileA.ts" />
/// <reference path="fileB.ts" />
let strings = ["Hello", "98052", "101"];
let validator = new Validation.LettersOnlyValidator();
strings.forEach(s => {
  console.log(`"${s}" ${validator.isAcceptable(s) ? " matches" : " does not match"}`);
});
```
When compiling `main.ts` with `--outFile app.js`, the compiler includes `fileA.ts` and `fileB.ts` in the correct order.

**Note:** This directive is generally not needed when using ES modules, as `import` statements handle dependencies.

### `/// <reference types="..." />` <a name="ref-types"></a>
This directive declares a dependency on a type package. It's used to include declaration files from an `@types` package or a similar structure.

```typescript
// main.ts
/// <reference types="node" />

import * as fs from "fs"; // Now 'fs' is recognized because of the 'node' types reference

console.log(fs.readdirSync("."));
```
This tells the compiler to include declarations found in `node_modules/@types/node/index.d.ts` (or a similar path configured by `typeRoots`). It's often automatically handled by the compiler if you `import` from a package that has an associated `@types` package, but can be explicit for global UMD libraries or when `types` option is used in `tsconfig.json`.

### `/// <reference lib="..." />` <a name="ref-lib"></a>
This directive allows a file to explicitly include an existing built-in library declaration file. For example, to use DOM APIs in a Node.js project without including the full `dom` lib in `tsconfig.json`.

```typescript
/// <reference lib="es2015.promise" />

function getPromise(): Promise<string> {
  return Promise.resolve("Hello from Promise!");
}
```
This is similar to using the `lib` option in `tsconfig.json` but on a per-file basis.

### Placement and Usage <a name="placement-usage-directives"></a>
*   Triple-slash directives must be at the **top of their file**. Only single or multi-line comments can precede them.
*   They are primarily for compiler instructions and don't become part of the emitted JavaScript.

## 2. Declaration Files (`.d.ts`) <a name="declaration-files"></a>

### What are Declaration Files? <a name="what-are-dts-files"></a>
Declaration files (files with a `.d.ts` extension) are used to provide type information for JavaScript code that was not written in TypeScript. They describe the shape of JavaScript libraries or modules, allowing TypeScript to type-check code that uses them.

They contain **only type information** and no implementations (no JavaScript code is generated from them).

### Why Use Declaration Files? <a name="why-use-dts-files"></a>
*   **Type Safety:** Enables TypeScript to check calls to JavaScript libraries, catching errors at compile time.
*   **Autocompletion:** Provides rich autocompletion and IntelliSense in code editors for JavaScript libraries.
*   **Interoperability:** Allows seamless integration of existing JavaScript code into TypeScript projects.

### Authoring Declaration Files <a name="authoring-dts-files"></a>
Declaration files use the `declare` keyword to describe types that exist elsewhere.

#### Global Declarations <a name="global-declarations"></a>
For libraries that add variables or functions to the global scope:

```typescript
// my-global-library.d.ts
declare var myGlobalVar: string;
declare function myGlobalFunction(name: string): void;

declare namespace MyGlobalNamespace {
  export interface MyInterface { id: number; }
  export function process(data: MyInterface): void;
}

// If you need to add to an existing global interface like Window:
declare global {
  interface Window {
    customProperty: boolean;
  }
}
```

#### Module Declarations (`declare module "..."`) <a name="module-declarations"></a>
For libraries that are consumed as modules (e.g., CommonJS, ES modules):

```typescript
// my-module-lib.d.ts
declare module "my-module-lib" {
  export const version: string;
  export function processData(data: any[]): void;
  export default class MyLibClass {
    constructor(options: object);
    doWork(): Promise<void>;
  }
}

// Usage in a .ts file:
// import MyLibClass, { version, processData } from "my-module-lib";
```

#### UMD Module Declarations (`export as namespace`) <a name="umd-module-declarations"></a>
For libraries that can be used as modules or as globals (UMD pattern):

```typescript
// my-umd-library.d.ts
export as namespace MyUMDLib;

export function calculate(x: number, y: number): number;
export interface Options { setting: boolean; }

// Allows: import { calculate } from "my-umd-library";
// OR (if loaded via script tag): MyUMDLib.calculate(1,2);
```

#### Shorthand Ambient Modules <a name="shorthand-ambient-modules"></a>
If you don't want to write out detailed type declarations for a module and just want to quickly tell TypeScript it exists (e.g., for an image or CSS module import), you can use a shorthand declaration:

```typescript
// image.d.ts
declare module '*.png';
declare module '*.jpg';

// styles.d.ts
declare module '*.css' {
  const content: { [className: string]: string };
  export default content;
}

// In your .ts file:
// import logo from './logo.png';
// import styles from './styles.css';
```

#### Augmenting Existing Types (Module Augmentation, Global Augmentation) <a name="augmenting-types"></a>
You can add members to existing modules or global types.

```typescript
// my-module-augmentation.d.ts
import { SomeType } from 'original-module';

declare module 'original-module' {
  // Add a new function to the original module
  export function newUtilityFunction(): void;

  // Add a new property to an existing interface
  export interface SomeType {
    newProperty: string;
  }
}

// global-augmentation.d.ts
declare global {
  interface Array<T> {
    toCustomString(): string; // Add a new method to global Array
  }
}
// Array.prototype.toCustomString = function() { return this.join(','); }
```

### Consuming Declaration Files <a name="consuming-dts-files"></a>

#### Automatic Discovery <a name="automatic-discovery-dts"></a>
TypeScript automatically finds and includes `.d.ts` files:
*   Part of your project (alongside your `.ts` files).
*   Specified in the `files`, `include`, or `exclude` options in `tsconfig.json`.
*   From `node_modules/@types` (see below).
*   Shipped with an npm package itself (if `types` or `typings` field in `package.json` points to a `.d.ts` file).

#### DefinitelyTyped (`@types` packages) <a name="definitelytyped-types"></a>
[DefinitelyTyped](https://definitelytyped.org/) is a massive repository of high-quality declaration files for thousands of JavaScript libraries. You can install them using npm or yarn:

```bash
npm install --save-dev @types/lodash
npm install --save-dev @types/react
```
Once installed, TypeScript will automatically pick up these type definitions.

#### `typeRoots` and `types` Compiler Options <a name="typeroots-types-options"></a>
In `tsconfig.json`:
*   `typeRoots`: Specifies a list of folders to include type definitions from. Defaults to `["./node_modules/@types"]`.
*   `types`: If specified, only packages listed here will be included from `typeRoots`. For example, `"types": ["node", "jest"]` would only include `@types/node` and `@types/jest`.

### Generating Declaration Files from TypeScript (`declaration: true`) <a name="generating-dts-files"></a>
If you are authoring a TypeScript library, you can instruct the compiler to generate `.d.ts` files for your library alongside the JavaScript output.

In `tsconfig.json`:
```json
{
  "compilerOptions": {
    "declaration": true,
    "outDir": "./dist" // Emitted .js and .d.ts files go here
  }
}
```
This is essential for making your TypeScript library usable by other TypeScript projects.

## 3. Relationship and Best Practices <a name="relationship-best-practices"></a>
*   **Modern Approach:** Rely on ES modules (`import`/`export`) for code organization. TypeScript will typically resolve types automatically from installed `@types` packages or `.d.ts` files shipped with libraries.
*   **`/// <reference types="..." />`:** Use this explicitly if you need to reference a global UMD library's types or if the `types` array in `tsconfig.json` is being used and you need to add a specific package.
*   **`/// <reference path="..." />`:** Mostly legacy for non-module, `--outFile` scenarios. Avoid in modern module-based projects.
*   **Declaration Files are Key:** Understanding and using `.d.ts` files (either consuming from `@types` or authoring your own) is fundamental for integrating JavaScript libraries into TypeScript.
*   **Ship `.d.ts` files with your library:** If you publish a library written in TypeScript, ensure you generate and include `.d.ts` files.

## 4. Key Takeaways <a name="key-takeaways-directives-dts"></a>
*   **Triple-Slash Directives** are compiler instructions:
    *   `/// <reference path="..." />` for file dependencies in `--outFile` compilations.
    *   `/// <reference types="..." />` for depending on type definition packages.
    *   `/// <reference lib="..." />` for including specific built-in libs per file.
*   **Declaration Files (`.d.ts`)** describe the types of JavaScript code for TypeScript.
    *   They contain only type information (`declare` keyword is common).
    *   Crucial for type safety and editor support when using JS libraries.
    *   Can be authored for global code, modules, or UMD libraries.
    *   **DefinitelyTyped (`@types`)** is the main source for community-maintained `.d.ts` files.
*   Enable `"declaration": true` in `tsconfig.json` to generate `.d.ts` files for your TypeScript library.

Mastering declaration files and understanding when to use triple-slash directives allows for smooth integration of TypeScript with the vast JavaScript ecosystem.

---

⬅️ [Back to Mixins](../11-ts-mixins/README.md) | ➡️ [Next: Type Checking JavaScript Files](../13-ts-type-checking-javascript/README.md)
