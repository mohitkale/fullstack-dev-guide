# 43. Advanced TypeScript with Deno

Deno is a modern and secure runtime for JavaScript and TypeScript, built with V8, Rust, and Tokio. It aims to provide a productive and secure scripting environment. Unlike Node.js, Deno has first-class support for TypeScript without requiring a separate compilation step (most of the time) and comes with a comprehensive standard library and built-in tooling.

This tutorial explores advanced concepts and practical usage of TypeScript with Deno.

## Table of Contents
1.  [Introduction to Deno](#intro-deno)
    *   Core Philosophy: Secure by default, TypeScript as a first-class citizen, single executable.
    *   Key Features: Built-in TypeScript support, comprehensive standard library, explicit permissions, browser-compatible module system.
2.  [Deno vs. Node.js for TypeScript Developers](#deno-vs-nodejs-ts)
    *   TypeScript Handling: Built-in in Deno vs. `tsc` + `ts-node` in Node.
    *   Module System: ES modules with URL imports vs. CommonJS/ESM with `node_modules`.
    *   Standard Library: Deno's `std` vs. Node's built-in modules.
    *   Package Management: No central package manager (like npm) by default; imports via URLs.
    *   Security: Explicit permissions vs. full access by default.
    *   APIs: Browser-compatible APIs where possible.
3.  [Getting Started with Deno and TypeScript](#getting-started-deno-ts)
    *   Installation.
    *   Running TypeScript files: `deno run my_script.ts`.
    *   Deno's caching mechanism for modules.
4.  [Deno's Module System](#deno-module-system)
    *   Importing from URLs (HTTP/HTTPS).
    *   Versioning modules via URLs.
    *   The `deps.ts` convention for managing dependencies.
    *   Import Maps (`import_map.json`) for aliasing and managing dependency versions.
    *   Dynamic imports with TypeScript.
5.  [Deno Standard Library (`std`) with TypeScript](#deno-std-lib-ts)
    *   Overview of commonly used modules:
        *   `std/http`: Building HTTP servers and clients.
        *   `std/fs`: File system operations (async and sync).
        *   `std/path`: Path manipulation.
        *   `std/io`: I/O utilities (readers, writers, buffers).
        *   `std/testing`: Assertion library for testing.
        *   `std/datetime`: Date and time utilities.
        *   `std/flags`: Command-line argument parsing.
    *   Type safety within the standard library.
6.  [Deno's Built-in Tooling](#deno-tooling-ts)
    *   `deno run`: Execute scripts (with permission flags).
    *   `deno fmt`: Code formatter (Prettier-like).
    *   `deno lint`: Code linter (built-in, configurable).
    *   `deno test`: Test runner (supports filtering, coverage).
    *   `deno doc`: Documentation generator.
    *   `deno compile`: Compile scripts into self-contained executables.
    *   `deno info`: Display dependency tree and module information.
    *   `deno repl`: Read-Eval-Print-Loop.
    *   `deno lsp`: Language Server Protocol for IDE integration.
7.  [Security Model in Deno](#deno-security-ts)
    *   Explicit permissions: `--allow-net`, `--allow-read`, `--allow-write`, `--allow-env`, `--allow-run`, `--allow-ffi`, `--allow-hrtime`.
    *   Granting specific permissions (e.g., `--allow-net=deno.land`).
    *   Security implications for TypeScript applications.
8.  [Building HTTP Servers with TypeScript in Deno](#deno-http-server-ts)
    *   Using `std/http`'s `serve` function.
    *   Handling requests and responses with type safety.
    *   Example: A simple REST API.
    *   Third-party routing libraries (e.g., Oak, Hono).
9.  [File System Operations with TypeScript](#deno-fs-ops-ts)
    *   Reading and writing files (`Deno.readFile`, `Deno.writeFile`, `Deno.readTextFile`, `Deno.writeTextFile`).
    *   Directory manipulation.
    *   Using `std/fs` for more advanced operations.
10. [Testing TypeScript Code in Deno](#deno-testing-ts)
    *   Writing tests using `Deno.test` and `std/testing/asserts.ts`.
    *   Async tests.
    *   Test organization and naming conventions.
    *   Running tests: `deno test --allow-all` (or specific permissions).
    *   Generating test coverage.
11. [Configuration and `deno.json(c)`](#deno-config-file)
    *   Using `deno.json` or `deno.jsonc` for project configuration.
    *   Specifying `compilerOptions` (subset of `tsconfig.json`).
    *   Defining `lint` and `fmt` options.
    *   Setting up `tasks` (scripts).
    *   Managing `imports` (import maps).
12. [Interoperability with Node.js and npm](#deno-node-compat-ts)
    *   Using npm packages via `npm:` specifiers (e.g., `import express from "npm:express@4";`).
    *   Deno's Node.js compatibility layer.
    *   Limitations and considerations.
13. [WebAssembly (WASM) with Deno and TypeScript](#deno-wasm-ts)
    *   Loading and instantiating WASM modules.
    *   Interacting with WASM functions type-safely.
14. [Foreign Function Interface (FFI)](#deno-ffi-ts)
    *   Calling native libraries (C, Rust, etc.) from Deno TypeScript.
    *   Using `Deno.dlopen`.
    *   Defining symbols and calling native functions with type safety.
    *   Replaces the older Deno `plugins` system.
15. [Advanced Deno Concepts](#deno-advanced-concepts-ts)
    *   Workers for parallel processing.
    *   Custom Deno runtimes (embedding Deno).
    *   Deploying Deno applications (Deno Deploy, Docker).
16. [Key Takeaways](#key-takeaways-deno-ts)

## 1. Introduction to Deno <a name="intro-deno"></a>
Deno, created by Ryan Dahl (also the creator of Node.js), addresses some of the regrets he had with Node.js. It prioritizes security, modern JavaScript features, and a better developer experience, especially for TypeScript users.

## 2. Deno vs. Node.js for TypeScript Developers <a name="deno-vs-nodejs-ts"></a>
| Feature           | Deno                                       | Node.js (with TypeScript)                     |
|-------------------|--------------------------------------------|-----------------------------------------------|
| TypeScript        | Built-in, first-class support              | Requires `typescript` & `tsc` or `ts-node`    |
| Modules           | ES Modules, URL imports                    | CommonJS by default, ES Modules support, `node_modules` |
| Security          | Explicit permissions (sandboxed)           | Full access by default                        |
| Standard Library  | Comprehensive, audited (`std`)             | Built-in modules, often need external packages |
| Package Manager   | None by default (uses URLs), `npm:` specifiers | npm/yarn/pnpm, `package.json`               |
| APIs              | Browser-compatible where possible          | Node.js specific APIs                         |
| Tooling           | Built-in (fmt, lint, test, compile, doc) | Relies on external tools (ESLint, Prettier, Jest) |

## 3. Getting Started with Deno and TypeScript <a name="getting-started-deno-ts"></a>
Install Deno from [deno.land](https://deno.land/#installation).
To run a TypeScript file:
```bash
# hello.ts
const message: string = "Hello, Deno with TypeScript!";
console.log(message);

deno run hello.ts
```
Deno type-checks and transpiles TypeScript on the fly. Compiled output is cached globally.

## 4. Deno's Module System <a name="deno-module-system"></a>
Modules are imported directly via URLs or file paths.
```typescript
// Import from URL (Deno standard library)
import { serve } from "https://deno.land/std@0.200.0/http/server.ts";
// Import from local file
import { add } from "./utils.ts";
```
**`deps.ts`:** A common convention to centralize external dependencies.
```typescript
// deps.ts
export { Application, Router } from "https://deno.land/x/oak@v12.6.0/mod.ts";
export { assertEquals } from "https://deno.land/std@0.200.0/testing/asserts.ts";

// main.ts
import { Application } from "./deps.ts";
```
**Import Maps (`deno.jsonc` or `import_map.json`):**
```jsonc
// deno.jsonc
{
  "imports": {
    "oak/": "https://deno.land/x/oak@v12.6.0/",
    "std/": "https://deno.land/std@0.200.0/"
  }
}
// main.ts
import { Application } from "oak/mod.ts";
import { readLines } from "std/io/read_lines.ts";

deno run --import-map=deno.jsonc main.ts // or Deno automatically finds deno.jsonc
```

## 5. Deno Standard Library (`std`) with TypeScript <a name="deno-std-lib-ts"></a>
Deno's standard library is a set of high-quality, audited modules for common tasks. They are versioned and imported via URLs.
```typescript
import { readTextFile } from "https://deno.land/std@0.200.0/fs/read_text_file.ts";

async function GreetFileContent(filePath: string): Promise<void> {
  try {
    const content: string = await readTextFile(filePath);
    console.log(content);
  } catch (error) {
    console.error("Error reading file:", error);
  }
}

GreetFileContent("./myText.txt");
```

## 6. Deno's Built-in Tooling <a name="deno-tooling-ts"></a>
*   `deno fmt [files]`: Formats code.
*   `deno lint [files]`: Lints code.
*   `deno test [files]`: Runs tests.
*   `deno compile script.ts`: Creates a standalone executable.
*   `deno doc script.ts`: Generates documentation.

## 7. Security Model in Deno <a name="deno-security-ts"></a>
Scripts run in a sandbox by default. Permissions must be explicitly granted.
```bash
# Needs network access to fetch the module and file read access
deno run --allow-net=deno.land --allow-read=./data.json main.ts

# Grant all permissions (use with caution)
deno run --allow-all main.ts
```

## 8. Building HTTP Servers with TypeScript in Deno <a name="deno-http-server-ts"></a>
Using `std/http`:
```typescript
// httpServer.ts
import { serve } from "https://deno.land/std@0.200.0/http/server.ts";

console.log("HTTP server running on http://localhost:8000/");

serve(handler, { port: 8000 });

function handler(req: Request): Response {
  const url = new URL(req.url);
  if (url.pathname === "/greet") {
    const name = url.searchParams.get("name") || "World";
    return new Response(`Hello, ${name}!`, { 
      headers: { "content-type": "text/plain" }
    });
  }
  return new Response("Not Found", { status: 404 });
}
```
Run with `deno run --allow-net=:8000 httpServer.ts`.
Frameworks like [Oak](https://deno.land/x/oak) provide more features (routing, middleware).

## 9. File System Operations with TypeScript <a name="deno-fs-ops-ts"></a>
```typescript
// fsExample.ts
async function manageFile() {
  const filePath = "./example.txt";
  const content: string = "Hello from Deno FS with TypeScript!";

  // Write to file
  await Deno.writeTextFile(filePath, content);
  console.log(`Wrote to ${filePath}`);

  // Read from file
  const readContent: string = await Deno.readTextFile(filePath);
  console.log(`Read from ${filePath}: ${readContent}`);

  // Get file info
  const fileInfo: Deno.FileInfo = await Deno.stat(filePath);
  console.log(`File size: ${fileInfo.size} bytes`);
}

manageFile();
```
Run with `deno run --allow-read --allow-write fsExample.ts`.

## 10. Testing TypeScript Code in Deno <a name="deno-testing-ts"></a>
```typescript
// math.ts
export function add(a: number, b: number): number {
  return a + b;
}

// math.test.ts
import { assertEquals } from "https://deno.land/std@0.200.0/testing/asserts.ts";
import { add } from "./math.ts";

Deno.test("add function should sum two numbers", () => {
  const result = add(2, 3);
  assertEquals(result, 5);
});

Deno.test({ 
  name: "add function with negative numbers",
  fn: () => {
    assertEquals(add(-1, -1), -2);
  }
});
```
Run tests: `deno test` (will discover `*_test.ts`, `*.test.ts`, `test.ts` files).

## 11. Configuration and `deno.json(c)` <a name="deno-config-file"></a>
`deno.json` or `deno.jsonc` can configure Deno's behavior for a project.
```jsonc
// deno.jsonc
{
  "compilerOptions": {
    "lib": ["deno.window"],
    "strict": true
  },
  "lint": {
    "files": {
      "exclude": ["data/"]
    },
    "rules": {
      "tags": ["recommended"],
      "include": ["ban-untagged-todo"]
    }
  },
  "fmt": {
    "files": {
      "exclude": ["fixtures/"]
    },
    "options": {
      "useTabs": false,
      "lineWidth": 80,
      "indentWidth": 2,
      "singleQuote": false
    }
  },
  "tasks": {
    "start": "deno run --allow-net --allow-read main.ts",
    "dev": "deno run --watch --allow-net --allow-read main.ts"
  },
  "imports": {
    "std/": "https://deno.land/std@0.200.0/"
  }
}
```
Run tasks: `deno task start`.

## 12. Interoperability with Node.js and npm <a name="deno-node-compat-ts"></a>
Deno supports importing npm packages using `npm:` specifiers.
```typescript
import express, { Request, Response } from "npm:express@4.18.2";

const app = express();

app.get("/", (_req: Request, res: Response) => {
  res.send("Hello from Express in Deno!");
});

app.listen(3000, () => {
  console.log("Express server running on http://localhost:3000");
});
```
Run with `deno run --allow-net --allow-env --allow-read main.ts` (permissions needed by Express).
Node.js built-in modules can be imported using `node:` specifiers (e.g., `import path from "node:path";`).

## 13. WebAssembly (WASM) with Deno and TypeScript <a name="deno-wasm-ts"></a>
```typescript
// wasm_example.ts
async function runWasm() {
  // Assume add.wasm exports an 'add' function: (a: i32, b: i32) => i32
  const wasmBytes = await Deno.readFile("./add.wasm");
  const wasmModule = new WebAssembly.Module(wasmBytes);
  const wasmInstance = new WebAssembly.Instance(wasmModule);

  // Type assertion for safety
  const add = wasmInstance.exports.add as (a: number, b: number) => number;
  
  console.log("WASM add(5, 7) =", add(5, 7));
}

runWasm();
```
Run with `deno run --allow-read=./add.wasm wasm_example.ts`.

## 14. Foreign Function Interface (FFI) <a name="deno-ffi-ts"></a>
FFI allows calling native code (e.g., Rust, C, C++) from Deno.
```typescript
// ffi_example.ts (assuming a native library `libnative.dylib` or `.so` or `.dll`)
// Native library might have a function: `int add_native(int a, int b);`

async function callNativeAdd() {
  const libPath = "./libnative.dylib"; // Adjust for your OS and library
  try {
    const dylib = Deno.dlopen(
      libPath,
      {
        "add_native": { parameters: ["i32", "i32"], result: "i32" }
      } as const // Use 'as const' for better type inference
    );

    const result: number = dylib.symbols.add_native(10, 20) as number;
    console.log(`Native add_native(10, 20) = ${result}`);

    dylib.close();
  } catch (e) {
    console.error("FFI Error:", e);
  }
}

callNativeAdd();
```
Run with `deno run --allow-ffi --unstable ffi_example.ts` (FFI is currently unstable).

## 15. Advanced Deno Concepts <a name="deno-advanced-concepts-ts"></a>
*   **Workers:** `new Worker('./worker.ts', { type: 'module' })` for multi-threaded computation, similar to Web Workers.
*   **Deno Deploy:** A distributed system for running JavaScript, TypeScript, and WebAssembly at the edge, globally.
*   **Custom Runtimes:** Embed Deno into your applications.

## 16. Key Takeaways <a name="key-takeaways-deno-ts"></a>
*   Deno offers a modern, secure, and TypeScript-first runtime environment.
*   Its built-in tooling, standard library, and URL-based module system streamline development.
*   The explicit permission model enhances security.
*   Deno provides good interoperability with Node.js/npm and supports WebAssembly and FFI for extending capabilities.
*   Using `deno.jsonc` is key for managing project configurations, tasks, and import maps.

Deno presents a compelling alternative to Node.js for many TypeScript projects, especially those valuing security, modern ESM, and integrated tooling.

---

⬅️ [Back to TypeScript in Real-time Applications](../42-ts-real-time-apps/README.md) | ➡️ [Next: TypeScript with WebAssembly (WASM) - In-Depth](../44-ts-wasm-deep-dive/README.md) *(Placeholder)*
