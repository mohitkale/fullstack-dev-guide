# 11. JavaScript Modules: Organizing and Reusing Code

As applications grow in size and complexity, managing code in a single file becomes impractical. JavaScript modules provide a way to split your code into separate, reusable pieces. Each module can define its own scope, export specific functionalities (variables, functions, classes), and import functionalities from other modules. This leads to better organization, maintainability, and reusability.

## Table of Contents
1.  [What are Modules?](#what-are-modules)
    *   [Why Use Modules?](#why-use-modules)
2.  [ES6 Modules (Standard)](#es6-modules)
    *   [Basic Concepts](#es6-module-concepts)
        *   Strict Mode by Default
        *   Module Scope
        *   File per Module
    *   [Exporting from Modules](#exporting-modules)
        *   [Named Exports](#named-exports)
        *   [Default Exports](#default-exports)
        *   [Re-exporting](#re-exporting)
    *   [Importing into Modules](#importing-modules)
        *   [Importing Named Exports](#importing-named)
        *   [Importing Default Exports](#importing-default)
        *   [Importing Both Named and Default](#importing-both)
        *   [Importing All as an Object (Namespace Import)](#importing-namespace)
        *   [Aliasing Imports (`as`)](#aliasing-imports)
        *   [Side-effect Imports](#side-effect-imports)
    *   [Using ES6 Modules in Browsers](#modules-in-browsers)
        *   [`<script type="module">`](#script-type-module)
        *   [Module Specifiers (Paths)](#module-specifiers)
        *   [CORS Requirements for Cross-Origin Modules](#module-cors)
    *   [ES6 Modules in Node.js](#modules-in-nodejs)
        *   [Enabling ES6 Modules (`"type": "module"` or `.mjs`)](#node-esm-enable)
        *   [Differences from CommonJS](#node-esm-vs-cjs)
    *   [Dynamic Imports (`import()`)](#dynamic-imports)
3.  [Brief Overview of Older Module Systems (for context)](#older-module-systems)
    *   [CommonJS (CJS) - Primarily Node.js](#commonjs)
    *   [Asynchronous Module Definition (AMD) - Primarily Browsers (e.g., RequireJS)](#amd)
    *   [Universal Module Definition (UMD)](#umd)
4.  [Best Practices for Writing Modules](#module-best-practices)
5.  [Key Takeaways](#key-takeaways-modules)

## 1. What are Modules? <a name="what-are-modules"></a>
A module is a self-contained piece of code that encapsulates related functionality. It can define variables, functions, and classes. Modules can choose what parts of their code to expose (export) to other modules and what parts to keep private.

### Why Use Modules? <a name="why-use-modules"></a>
*   **Organization**: Break down complex code into smaller, manageable files.
*   **Reusability**: Write code once and use it in multiple places.
*   **Maintainability**: Easier to understand, update, and debug smaller, focused pieces of code.
*   **Encapsulation**: Hide internal implementation details and expose only a public API.
*   **Dependency Management**: Clearly define dependencies between different parts of your application.
*   **Avoid Naming Collisions**: Each module has its own scope, preventing global namespace pollution.

## 2. ES6 Modules (Standard) <a name="es6-modules"></a>
ES6 (ECMAScript 2015) introduced a standard module system for JavaScript, which is now widely supported in modern browsers and Node.js.

### Basic Concepts <a name="es6-module-concepts"></a>
*   **Strict Mode by Default**: Code inside ES6 modules automatically runs in strict mode (`'use strict';`). There's no need to declare it.
*   **Module Scope**: Each module has its own top-level scope. Variables, functions, and classes declared in a module are local to that module by default and not visible outside unless explicitly exported.
*   **File per Module**: Typically, each file represents a module.

### Exporting from Modules <a name="exporting-modules"></a>
The `export` statement is used to make functionalities available to other modules.

#### Named Exports <a name="named-exports"></a>
You can export multiple values from a module by name. Exports can be declared inline or separately.

**Example (`utils.js`):**
```javascript
// Inline named exports
export const PI = 3.14159;

export function add(a, b) {
  return a + b;
}

export class User {
  constructor(name) {
    this.name = name;
  }
  greet() {
    console.log(`Hello, ${this.name}!`);
  }
}

// Separate named exports (can be useful at the end of the file)
const subtract = (a, b) => a - b;
const version = "1.0";
// export { subtract, version }; // Also valid
export { subtract as minus, version }; // Can also alias exports
```

#### Default Exports <a name="default-exports"></a>
A module can have at most one default export. This is often used for the primary functionality of the module, like a class or a main function.

**Example (`mainComponent.js`):**
```javascript
// Exporting a class as default
export default class MainComponent {
  constructor(id) {
    this.id = id;
  }
  render() {
    console.log(`Rendering component ${this.id}`);
  }
}

// Or, export a function as default:
// export default function mainFunction() { ... }

// Or, an expression:
// const config = { setting: true };
// export default config;
```

#### Re-exporting <a name="re-exporting"></a>
You can import from one module and immediately export it from another. This is useful for creating a single entry point for multiple modules (barrel files).

**Example (`index.js` acting as a barrel):**
```javascript
// Re-export named exports from utils.js
export { PI, add, User } from './utils.js';

// Re-export default export from mainComponent.js (needs aliasing if re-exported as named)
// export { default as MyMainComponent } from './mainComponent.js';

// Or re-export everything (named exports) from another module
// export * from './anotherModule.js';

// Re-export default as default (less common for barrels, usually named re-exports are preferred)
// export { default } from './mainComponent.js';
```

### Importing into Modules <a name="importing-modules"></a>
The `import` statement is used to bring exported functionalities into the current module's scope.

#### Importing Named Exports <a name="importing-named"></a>
Use curly braces `{}` to import specific named exports.

**Example (`app.js` importing from `utils.js`):**
```javascript
import { PI, add, User, minus as subtractAlias } from './utils.js';

console.log(PI); // 3.14159
console.log(add(5, 3)); // 8

const user = new User("Alice");
user.greet(); // Hello, Alice!

console.log(subtractAlias(10, 4)); // 6
```

#### Importing Default Exports <a name="importing-default"></a>
Import the default export without curly braces. You can choose any name for the imported default.

**Example (`app.js` importing from `mainComponent.js`):**
```javascript
import MyCustomComponentName from './mainComponent.js';
// The name 'MyCustomComponentName' can be anything you choose here.

const component = new MyCustomComponentName('comp123');
component.render(); // Rendering component comp123
```

#### Importing Both Named and Default <a name="importing-both"></a>
You can import both default and named exports from the same module in a single statement.

**Example (`moduleWithBoth.js`):**
```javascript
export const version = '1.0';
export default function greet() { console.log('Hello!'); }
```
**Example (`importer.js`):**
```javascript
import myGreet, { version as moduleVersion } from './moduleWithBoth.js';

myGreet(); // Hello!
console.log(moduleVersion); // 1.0
```

#### Importing All as an Object (Namespace Import) <a name="importing-namespace"></a>
Import all named exports from a module as properties of a single object.

**Example (`app.js` importing from `utils.js`):**
```javascript
import * as Utils from './utils.js';

console.log(Utils.PI); // 3.14159
console.log(Utils.add(10, 20)); // 30
const anotherUser = new Utils.User("Bob");
anotherUser.greet(); // Hello, Bob!
```
This does *not* include the default export (if any). The default export would need to be imported separately.

#### Aliasing Imports (`as`) <a name="aliasing-imports"></a>
You can rename imports to avoid naming conflicts or for clarity.
```javascript
import { add as sumNumbers, PI as MathPI } from './utils.js';
import MyDefaultComponent as PrimaryUI from './mainComponent.js';

console.log(sumNumbers(1, 2)); // 3
console.log(MathPI);
const ui = new PrimaryUI('main-ui');
ui.render();
```

#### Side-effect Imports <a name="side-effect-imports"></a>
Sometimes, you might want to import a module just for its side effects (e.g., it modifies global variables, runs some setup code), without importing any specific values.
```javascript
import './setup-globals.js'; // Runs code in setup-globals.js
```
Use this sparingly, as it can make dependencies less clear.

### Using ES6 Modules in Browsers <a name="modules-in-browsers"></a>

#### `<script type="module">` <a name="script-type-module"></a>
To use ES6 modules in a browser, you include your main script file with `type="module"`:
```html
<!DOCTYPE html>
<html>
<head>
  <title>JS Modules Example</title>
</head>
<body>
  <script type="module" src="./app.js"></script>
  <!-- Or inline module script -->
  <!--
  <script type="module">
    import { someFunction } from './myModule.js';
    someFunction();
  </script>
  -->
</body>
</html>
```
**Key behaviors of `type="module"` scripts:**
*   **Deferred by default**: Module scripts are executed after the HTML document has been parsed, similar to scripts with the `defer` attribute.
*   **CORS required**: Scripts fetched from other origins must have appropriate CORS headers (e.g., `Access-Control-Allow-Origin: *`).
*   **Strict mode**: Modules run in strict mode by default.
*   **Module scope**: Top-level variables are local to the module, not global.

#### Module Specifiers (Paths) <a name="module-specifiers"></a>
When importing, you need to provide a path (module specifier) to the module file.
*   **Relative paths**: Start with `./` or `../` (e.g., `./utils.js`, `../lib/helper.js`).
*   **Absolute paths**: Start with `/` (e.g., `/modules/myModule.js`). Relative to the site root.
*   **Full URLs**: Can be used to import modules from other domains (CORS applies) (e.g., `https://example.com/js/module.js`).
*   **Bare specifiers (e.g., `import 'lodash'`):** These are not directly supported by browsers. They typically require a build step or import maps to resolve to actual paths.

File extensions (like `.js`) are usually required in browser import paths unless import maps are used.

### ES6 Modules in Node.js <a name="modules-in-nodejs"></a>
Node.js traditionally used the CommonJS module system. However, it now has robust support for ES6 modules.

#### Enabling ES6 Modules <a name="node-esm-enable"></a>
There are two main ways to tell Node.js to treat `.js` files as ES6 modules:
1.  **`"type": "module"` in `package.json`**: Add this to your project's `package.json` file. All `.js` files in that directory (and subdirectories, unless overridden) will be treated as ES modules.
    ```json
    // package.json
    {
      "name": "my-node-app",
      "version": "1.0.0",
      "type": "module",
      "main": "app.js"
    }
    ```
2.  **`.mjs` extension**: Use the `.mjs` extension for your ES module files. Node.js will always treat these as ES modules, regardless of the `package.json` setting.

If you're using `"type": "module"` and need to use CommonJS for a specific file, you can name it with a `.cjs` extension.

#### Differences from CommonJS <a name="node-esm-vs-cjs"></a>
*   Use `import`/`export` instead of `require()`/`module.exports`.
*   `__dirname` and `__filename` are not directly available in ES modules. Use `import.meta.url` to get the current module's path.
    ```javascript
    import { fileURLToPath } from 'url';
    import { dirname } from 'path';

    const __filename = fileURLToPath(import.meta.url);
    const __dirname = dirname(__filename);
    console.log('Current directory:', __dirname);
    ```
*   JSON files can be imported directly in CommonJS (`require('./data.json')`). In ES modules, you might need to use `fs.readFile` or a dynamic import with an assertion (experimental).
    ```javascript
    // Experimental JSON module import (Node.js v17.5+)
    // import jsonData from './data.json' assert { type: 'json' };
    ```

### Dynamic Imports (`import()`) <a name="dynamic-imports"></a>
ES2020 introduced dynamic imports. The `import()` syntax looks like a function call and returns a Promise. This allows you to load modules on demand (conditionally or lazily).

```javascript
async function loadModuleConditionally() {
  if (someCondition) {
    try {
      const myModule = await import('./optionalModule.js');
      myModule.doSomething();
      if (myModule.default) {
        myModule.default(); // If it has a default export
      }
    } catch (error) {
      console.error("Failed to load module:", error);
    }
  } else {
    console.log("Module not loaded.");
  }
}

// Example usage for lazy loading a component on button click
/*
document.getElementById('loadButton').addEventListener('click', async () => {
  const { ChartComponent } = await import('./charting.js');
  const chart = new ChartComponent('myChart');
  chart.render();
});
*/
```
`import()` can be used in both browser and Node.js environments.

## 3. Brief Overview of Older Module Systems (for context) <a name="older-module-systems"></a>
Before ES6 modules became standard, other module systems were popular.

### CommonJS (CJS) <a name="commonjs"></a>
*   Primarily used in **Node.js**.
*   Synchronous: `require()` loads modules synchronously.
*   Exports are done via `module.exports` or by assigning to `exports`.
    ```javascript
    // myCJSModule.js
    const fs = require('fs');
    function greet(name) { return `Hello, ${name}!`; }
    module.exports = { greet, version: '1.0' };

    // main.js
    // const myModule = require('./myCJSModule.js');
    // console.log(myModule.greet('World'));
    ```

### Asynchronous Module Definition (AMD) <a name="amd"></a>
*   Designed for **browsers**, addressing the synchronous nature of CommonJS which isn't ideal for browsers (blocking rendering).
*   Asynchronous loading of modules.
*   Popularized by libraries like **RequireJS**.
    ```javascript
    // define(['dependency1', 'dependency2'], function(dep1, dep2) {
    //   // Module code using dependencies
    //   return { apiMethod: function() {} };
    // });
    ```

### Universal Module Definition (UMD) <a name="umd"></a>
*   Tries to be compatible with both AMD and CommonJS, and also work with global variable definitions.
*   Often used by libraries that need to work in various environments.

While ES6 modules are the standard now, you might encounter these older systems in legacy codebases or some Node.js projects.

## 4. Best Practices for Writing Modules <a name="module-best-practices"></a>
*   **Single Responsibility Principle**: Each module should focus on a specific piece of functionality.
*   **Explicit Exports**: Export only what's necessary to be used by other modules. Keep internal details private.
*   **Clear Naming**: Use clear and descriptive names for modules and their exports.
*   **Prefer Named Exports for Utilities**: Makes it clear what is being imported. Default exports are often better for the main export of a module (e.g., a class or primary function).
*   **Avoid Circular Dependencies**: Where module A imports B, and B imports A. This can lead to issues and usually indicates a design problem.
*   **Use File Extensions in Browser Imports**: Be explicit (e.g., `./utils.js`) unless using import maps.
*   **Consider Barrel Files (`index.js`) Carefully**: They can simplify imports from a directory but can also lead to larger initial loads if not managed with tree-shaking by bundlers.
*   **Keep Side Effects Minimal**: Modules that primarily cause side effects (e.g., modifying globals) can be harder to test and reason about.

## 5. Key Takeaways <a name="key-takeaways-modules"></a>
*   Modules help organize code into reusable, maintainable, and encapsulated pieces.
*   **ES6 Modules** are the standard in modern JavaScript, using `export` and `import` statements.
*   Modules run in **strict mode** by default and have their own **scope**.
*   **Named exports** allow multiple exports; **default exports** provide a single main export.
*   In browsers, use `<script type="module">` to load ES6 modules.
*   Node.js supports ES6 modules via `"type": "module"` in `package.json` or by using the `.mjs` extension.
*   **Dynamic `import()`** allows for on-demand module loading.
*   Understanding modules is crucial for building scalable JavaScript applications.

---

⬅️ [Back to Asynchronous JavaScript](../10-js-async/README.md) | ➡️ [Next Section: Error Handling](../12-js-error-handling/README.md)
