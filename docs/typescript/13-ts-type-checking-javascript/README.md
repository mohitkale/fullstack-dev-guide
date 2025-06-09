# 13. Type Checking JavaScript Files

TypeScript is not just for `.ts` files. It can also be used to bring some of the benefits of static type checking to existing JavaScript (`.js`) codebases. This is particularly useful when migrating a project from JavaScript to TypeScript incrementally or when working in a mixed codebase.

## Table of Contents
1.  [Why Type Check JavaScript?](#why-type-check-js)
2.  [Enabling JavaScript Checking](#enabling-js-checking)
    *   [`allowJs` Compiler Option](#allowjs-option)
    *   [`checkJs` Compiler Option](#checkjs-option)
    *   `// @ts-check` and `// @ts-nocheck` Comments](#ts-check-comments)
3.  [JSDoc Annotations for Types](#jsdoc-annotations)
    *   [Basic Types](#jsdoc-basic-types)
    *   [Objects and Interfaces (`@typedef`)](#jsdoc-typedef)
    *   [Functions (`@param`, `@returns`, `@type`)](#jsdoc-functions)
    *   [Generics (`@template`)](#jsdoc-generics)
    *   [Casting (`@type`)](#jsdoc-casting)
    *   [Importing Types (`@import`)](#jsdoc-import-types) (Note: This is more of a TypeScript feature for `.ts` files, JSDoc has limited direct import but can reference types defined elsewhere)
4.  [How TypeScript Infers Types in JavaScript](#ts-inference-js)
5.  [Limitations](#limitations-js-checking)
6.  [Example: Type Checking a Simple JavaScript File](#example-js-check)
7.  [Benefits of Using TypeScript with JavaScript](#benefits-ts-with-js)
8.  [Key Takeaways](#key-takeaways-js-check)

## 1. Why Type Check JavaScript? <a name="why-type-check-js"></a>
*   **Catch Errors Early:** Identify common errors like typos, incorrect function arguments, and null/undefined issues before runtime.
*   **Improve Code Quality:** Gain better understanding and confidence in your JavaScript code.
*   **Better Tooling:** Leverage TypeScript's language service for improved autocompletion, refactoring, and navigation in `.js` files.
*   **Gradual Migration:** Provides a pathway to incrementally adopt TypeScript in an existing JavaScript project.

## 2. Enabling JavaScript Checking <a name="enabling-js-checking"></a>
TypeScript provides several ways to enable type checking for JavaScript files.

### `allowJs` Compiler Option <a name="allowjs-option"></a>
In your `tsconfig.json`:
```json
{
  "compilerOptions": {
    "allowJs": true, // Allows JavaScript files to be compiled
    "outDir": "./dist"
    // ... other options
  }
}
```
Setting `allowJs` to `true` tells the TypeScript compiler to include `.js` files in the compilation process. By default, it doesn't type-check these files but will pass them through to the output directory (if `outDir` is specified).

### `checkJs` Compiler Option <a name="checkjs-option"></a>
To enable type checking for `.js` files, set `checkJs` to `true` in `tsconfig.json`:
```json
{
  "compilerOptions": {
    "allowJs": true,
    "checkJs": true, // Enables type checking for .js files
    "outDir": "./dist"
    // ... other options
  }
}
```
With `checkJs: true`, TypeScript will report type errors it finds in your `.js` files, similar to how it does for `.ts` files.

### `// @ts-check` and `// @ts-nocheck` Comments <a name="ts-check-comments"></a>
You can control type checking on a per-file basis using special comments at the top of your `.js` files:

*   **`// @ts-check`**: Enables type checking for the file, even if `checkJs` is `false` in `tsconfig.json` (requires `allowJs: true`).
    ```javascript
    // @ts-check

    let message = "Hello!";
    // message = 10; // Error: Type 'number' is not assignable to type 'string'.
    ```

*   **`// @ts-nocheck`**: Disables type checking for the file, even if `checkJs` is `true`.
    ```javascript
    // @ts-nocheck

    let value = "This won't be checked";
    value = 100; // No error reported
    ```

*   **`// @ts-ignore` or `// @ts-expect-error`**: Can be used on the line preceding an error to suppress it, just like in TypeScript files.

## 3. JSDoc Annotations for Types <a name="jsdoc-annotations"></a>
Since JavaScript doesn't have native syntax for type annotations, TypeScript leverages JSDoc comments to understand and enforce types in `.js` files.

### Basic Types <a name="jsdoc-basic-types"></a>
Use `@type` to specify the type of a variable.
```javascript
// @ts-check

/** @type {string} */
let user_name = "Alice";

/** @type {number} */
let user_age = 30;

// user_name = 123; // Error!
// user_age = "thirty"; // Error!
```
Common types include `string`, `number`, `boolean`, `Array<T>`, `object`, `any`, `unknown`, `null`, `undefined`.

### Objects and Interfaces (`@typedef`) <a name="jsdoc-typedef"></a>
You can define complex object shapes or type aliases using `@typedef`.
```javascript
// @ts-check

/**
 * @typedef {object} UserProfile
 * @property {number} id
 * @property {string} username
 * @property {boolean} [isActive] - Optional property
 */

/** @type {UserProfile} */
const user1 = {
  id: 1,
  username: "bob_the_builder",
  isActive: true
};

// const user2 = { id: 2, name: "Carol" }; // Error: Property 'username' is missing...

// You can also import types from other JSDoc @typedefs or even .d.ts files
/**
 * @typedef {import('./another-file').ExternalType} MyExternalType
 */
```

### Functions (`@param`, `@returns`, `@type`) <a name="jsdoc-functions"></a>
Annotate function parameters and return values.
```javascript
// @ts-check

/**
 * Greets a person.
 * @param {string} name - The name of the person.
 * @param {number} [age] - Optional age of the person.
 * @returns {string} The greeting message.
 */
function greet(name, age) {
  if (age) {
    return `Hello, ${name}! You are ${age} years old.`;
  }
  return `Hello, ${name}!`;
}

const greeting = greet("Charlie");
// const invalidGreeting = greet(123); // Error: Argument of type 'number' is not assignable to parameter of type 'string'.

/**
 * A function type definition
 * @typedef {(value: number) => string} NumberToStringConverter
 */

/** @type {NumberToStringConverter} */
const convert = (val) => String(val);
```

### Generics (`@template`) <a name="jsdoc-generics"></a>
JSDoc supports generic type parameters using `@template`.
```javascript
// @ts-check

/**
 * @template T
 * @param {T} arg - The argument of generic type T.
 * @returns {T}
 */
function identity(arg) {
  return arg;
}

const s = identity("hello"); // s is string
const n = identity(123);   // n is number
```

### Casting (`@type`) <a name="jsdoc-casting"></a>
You can use `@type` within parentheses for casting, similar to TypeScript's `as` keyword.
```javascript
// @ts-check

let someValue = "this is a string";

// Pretend we know it's a string of a specific format, though TS might infer 'string'
/** @type {string} */
let strLength = (/** @type {string} */ (someValue)).length;

// More common for complex types or 'any'
/** @type {any} */
let looselyTypedValue = { message: "Hello" };

/** @type {{ message: string }} */
let specificTypedValue = (/** @type {{ message: string }} */ (looselyTypedValue));
console.log(specificTypedValue.message);
```

### Importing Types (`@import`) <a name="jsdoc-import-types"></a>
While JSDoc itself doesn't have a direct `@import` for values like ES6 modules, TypeScript allows you to import types defined in `.d.ts` files or other JSDoc `@typedef`s for use in your JSDoc annotations.

```javascript
// types.d.ts
export interface ExternalUser {
  id: number;
  email: string;
}

// main.js
// @ts-check

/**
 * @typedef {import("./types").ExternalUser} User
 */

/** @type {User} */
const importedTypeUser = {
  id: 101,
  email: "user@example.com"
};

// console.log(importedTypeUser.name); // Error: Property 'name' does not exist on type 'User'.
```
This `import()` type syntax in JSDoc is a TypeScript-specific feature that makes JSDoc much more powerful for type checking.

## 4. How TypeScript Infers Types in JavaScript <a name="ts-inference-js"></a>
Even without explicit JSDoc annotations, TypeScript's type inference works in `.js` files (when `checkJs` is enabled):
*   **Variable Initialization:** `let x = 10;` (x is inferred as `number`)
*   **Function Return Values:** `function getNum() { return 5; }` (return type inferred as `number`)
*   **Object Properties:** Based on assignment.
*   **Control Flow Analysis:** TypeScript can narrow types within `if` blocks, `switch` cases, etc., just like in `.ts` files.

JSDoc annotations help where inference isn't enough or when you want to be more explicit.

## 5. Limitations <a name="limitations-js-checking"></a>
*   **Verbosity:** Adding JSDoc types can make JavaScript code more verbose compared to TypeScript's concise syntax.
*   **Complexity:** Advanced TypeScript features (like complex mapped types, conditional types, decorators) might be difficult or impossible to express accurately with JSDoc.
*   **Tooling Support:** While good, editor support for JSDoc types might not always be as seamless as for native TypeScript syntax.
*   **No Code Emission Changes:** TypeScript only checks `.js` files; it doesn't transform or transpile them in the same way it does `.ts` files (unless you are also using Babel with TypeScript presets, for example).

## 6. Example: Type Checking a Simple JavaScript File <a name="example-js-check"></a>

`math.js`:
```javascript
// @ts-check

/**
 * Adds two numbers.
 * @param {number} a The first number.
 * @param {number} b The second number.
 * @returns {number} The sum of a and b.
 */
function add(a, b) {
  return a + b;
}

/** @type {string} */
let resultMessage;

resultMessage = `The sum is: ${add(5, 10)}`;
console.log(resultMessage);

// resultMessage = add(3, 2); // Error: Type 'number' is not assignable to type 'string'.
// add("5", 10); // Error: Argument of type 'string' is not assignable to parameter of type 'number'.
```

`tsconfig.json`:
```json
{
  "compilerOptions": {
    "allowJs": true,
    "checkJs": true,
    "noEmit": true // Often used if you're just type-checking JS, not compiling it
  },
  "include": ["**/*.js"] // Ensure .js files are included
}
```
Running `tsc` in the terminal would report the errors commented out above.

## 7. Benefits of Using TypeScript with JavaScript <a name="benefits-ts-with-js"></a>
*   **Low Barrier to Entry:** Start getting type safety benefits without rewriting code in TypeScript.
*   **Documentation:** JSDoc types serve as excellent documentation for your code's API.
*   **Improved Maintainability:** Makes refactoring and understanding legacy JavaScript codebases easier.

## 8. Key Takeaways <a name="key-takeaways-js-check"></a>
*   TypeScript can type-check `.js` files using the `allowJs` and `checkJs` compiler options or `// @ts-check` comment.
*   **JSDoc annotations** are used to provide explicit type information in JavaScript code (`@type`, `@param`, `@returns`, `@typedef`, `@template`).
*   TypeScript's type inference also works in `.js` files.
*   This feature is excellent for gradually adopting TypeScript or improving the robustness of existing JavaScript projects.
*   While powerful, it has limitations compared to full TypeScript syntax for complex types.

---

⬅️ [Back to Triple-Slash Directives and Declaration Files](../12-ts-triple-slash-directives-declaration-files/README.md) | ➡️ [Next: Utility Types](../14-ts-utility-types/README.md)
