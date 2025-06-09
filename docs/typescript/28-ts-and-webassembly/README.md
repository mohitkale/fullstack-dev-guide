# 28. TypeScript and WebAssembly (Wasm)

WebAssembly (Wasm) is a binary instruction format for a stack-based virtual machine. It's designed as a portable compilation target for high-level languages like C++, Rust, Go, and more recently, languages specifically designed for Wasm like AssemblyScript. Wasm enables near-native performance for web applications and can also be used in non-web environments.

This tutorial explores how TypeScript can interoperate with WebAssembly modules, leveraging Wasm's performance benefits for computationally intensive tasks within a TypeScript application.

## Table of Contents
1.  [What is WebAssembly?](#what-is-webassembly)
    *   Key Characteristics: Binary Format, Performance, Safety, Portability
    *   Why Wasm? Performance, Language Diversity, Existing Codebases
2.  [Benefits of Using Wasm with TypeScript/JavaScript](#benefits-wasm-ts)
    *   Performance-Critical Operations (e.g., image/video processing, physics engines, cryptography)
    *   Reusing Code from Other Languages (C++, Rust, Go)
    *   Reducing JavaScript Bundle Size (for certain types of code)
3.  [How WebAssembly Works in the Browser](#how-wasm-works-browser)
    *   Loading `.wasm` Files
    *   Compilation and Instantiation
    *   JavaScript API for Wasm (`WebAssembly` object)
4.  [Integrating Wasm Modules with TypeScript](#integrating-wasm-ts)
    *   Loading a `.wasm` Module (`WebAssembly.instantiateStreaming` or `WebAssembly.instantiate`)
    *   Calling Wasm Functions from TypeScript
    *   Understanding Wasm Exports and Imports
5.  [Data Exchange Between TypeScript and Wasm](#data-exchange-ts-wasm)
    *   Passing Numbers (Integers, Floats)
    *   Working with Wasm Memory (`WebAssembly.Memory`)
    *   Strategies for Passing Strings
    *   Handling Complex Data Structures (Arrays, Objects)
        *   Serialization (e.g., JSON, Protocol Buffers)
        *   Direct Memory Manipulation (more complex, higher performance)
    *   Memory Management Considerations
6.  [Tools and Languages for Creating Wasm Modules](#tools-languages-wasm)
    *   **C/C++ with Emscripten:** A mature toolchain for compiling C/C++ to Wasm.
    *   **Rust:** Excellent Wasm support, often favored for safety and performance.
    *   **Go:** Official support for compiling to Wasm (though with some limitations on direct DOM access).
    *   **AssemblyScript:** A TypeScript-like language that compiles directly to Wasm. Offers a familiar syntax for TypeScript developers.
7.  [Introduction to AssemblyScript](#introduction-assemblyscript)
    *   What is AssemblyScript?
    *   Syntax Similarities and Differences with TypeScript
    *   Basic Example: Writing a simple function in AssemblyScript and compiling to Wasm.
    *   Using the AssemblyScript Wasm module in TypeScript.
8.  [Practical Example: A Simple Wasm Math Utility in TypeScript](#practical-example-wasm)
    *   Writing a simple math function (e.g., factorial) in C++ or Rust or AssemblyScript.
    *   Compiling it to `.wasm`.
    *   Loading and using it from a TypeScript application.
9.  [Advanced Topics and Considerations](#advanced-topics-wasm)
    *   WASI (WebAssembly System Interface): For running Wasm outside the browser.
    *   Threading and SIMD (Single Instruction, Multiple Data)
    *   Debugging Wasm
    *   Security Implications
10. [Limitations and When to Consider Wasm](#limitations-when-to-use-wasm)
    *   Not a Replacement for JavaScript/TypeScript
    *   Overhead of JS-Wasm Interop
    *   Complexity for Simple Tasks
    *   Best suited for CPU-bound tasks, not DOM manipulation.
11. [Key Takeaways](#key-takeaways-wasm)

## 1. What is WebAssembly? <a name="what-is-webassembly"></a>
WebAssembly (Wasm) is a low-level, assembly-like language with a compact binary format. It runs in modern web browsers and provides a way to run code written in multiple languages on the web at near-native speed.

### Key Characteristics:
*   **Binary Format:** `.wasm` files are small and load quickly.
*   **Performance:** Designed to be fast to parse and execute, often significantly faster than JavaScript for CPU-intensive tasks.
*   **Safety:** Runs in a sandboxed environment, adhering to the browser's security policies.
*   **Portability:** Runs on all modern browsers and can be used in non-browser environments (e.g., Node.js, serverless functions) via runtimes like Wasmer or Wasmtime.

### Why Wasm?
*   **Performance:** To accelerate parts of web applications that are computationally demanding.
*   **Language Diversity:** Allows developers to write web components in languages like C++, Rust, Go, C#, etc.
*   **Existing Codebases:** Enables porting existing desktop applications or libraries to the web.

## 2. Benefits of Using Wasm with TypeScript/JavaScript <a name="benefits-wasm-ts"></a>
While TypeScript (and JavaScript) is incredibly versatile, Wasm complements it by offering:
*   **Performance-Critical Operations:** For tasks like complex calculations, 3D graphics rendering, game physics, image/audio/video processing, and cryptography, Wasm can provide substantial speedups.
*   **Reusing Code:** Leverage mature and highly optimized libraries written in C++, Rust, or other Wasm-compatible languages directly in your web application.
*   **Reducing JavaScript Bundle Size (Potentially):** For certain types of algorithms, a compiled Wasm module might be smaller than its JavaScript equivalent, especially if the original code was in a language that compiles efficiently.

## 3. How WebAssembly Works in the Browser <a name="how-wasm-works-browser"></a>
1.  **Fetching:** The `.wasm` file is fetched from the server like any other resource.
2.  **Compilation:** The browser compiles the Wasm bytecode into machine code specific to the user's device. This compilation is very fast.
3.  **Instantiation:** A Wasm module instance is created. This involves setting up its memory and linking any imported functions (e.g., JavaScript functions it needs to call).
4.  **Execution:** JavaScript/TypeScript can then call exported Wasm functions.

### JavaScript API for Wasm
The global `WebAssembly` object in JavaScript provides the core API:
*   `WebAssembly.compile()` / `WebAssembly.compileStreaming()`: Compiles Wasm bytecode into a `WebAssembly.Module`.
*   `WebAssembly.instantiate()` / `WebAssembly.instantiateStreaming()`: Compiles (if needed) and instantiates a Wasm module, returning both a `WebAssembly.Module` and a `WebAssembly.Instance`.
*   `WebAssembly.Module`: Represents compiled Wasm code.
*   `WebAssembly.Instance`: Represents a runnable instance of a Wasm module, with its own memory and exported functions.
*   `WebAssembly.Memory`: Represents the linear memory used by a Wasm instance.
*   `WebAssembly.Table`: Represents a table of function references (used for indirect function calls).

## 4. Integrating Wasm Modules with TypeScript <a name="integrating-wasm-ts"></a>

### Loading a `.wasm` Module
The preferred way to load and instantiate a Wasm module is using `WebAssembly.instantiateStreaming` as it's more efficient:

```typescript
async function loadWasmModule(wasmPath: string, importObject?: WebAssembly.Imports): Promise<WebAssembly.WebAssemblyInstantiatedSource> {
  try {
    const response = await fetch(wasmPath);
    if (!response.ok) {
      throw new Error(`Failed to fetch Wasm module: ${response.statusText}`);
    }
    // For instantiateStreaming, the response must have the correct MIME type (application/wasm)
    // or be from the same origin. Otherwise, use response.arrayBuffer() and WebAssembly.instantiate.
    const source = await WebAssembly.instantiateStreaming(response, importObject);
    return source;
  } catch (error) {
    console.error("Error loading Wasm module:", error);
    // Fallback for environments or configurations where streaming instantiation might fail
    // (e.g., incorrect MIME type, or if fetch response isn't directly usable)
    console.log("Attempting fallback to ArrayBuffer instantiation...");
    const response = await fetch(wasmPath);
    const arrayBuffer = await response.arrayBuffer();
    const source = await WebAssembly.instantiate(arrayBuffer, importObject);
    return source;
  }
}

// Usage:
interface MyWasmExports extends WebAssembly.Exports {
  add: (a: number, b: number) => number;
  // Define other exported functions
}

async function main() {
  const importObject = {
    env: {
      // Define any JavaScript functions Wasm needs to import
      // js_log: (value: number) => console.log(value)
    }
  };

  try {
    const { instance } = await loadWasmModule('./my_module.wasm', importObject);
    const wasmExports = instance.exports as MyWasmExports;
    
    const sum = wasmExports.add(5, 7);
    console.log(`Sum from Wasm: ${sum}`); // Output: Sum from Wasm: 12
  } catch (err) {
    console.error("Wasm initialization failed:", err);
  }
}

main();
```

### Calling Wasm Functions from TypeScript
Once instantiated, exported Wasm functions are available on `instance.exports`. You can call them like regular JavaScript functions, but be mindful of the data types they expect and return (usually numbers: `i32`, `i64`, `f32`, `f64`).

### Understanding Wasm Exports and Imports
*   **Exports:** Functions, memory, tables, or global variables defined within the Wasm module that are made accessible to the JavaScript/TypeScript host.
*   **Imports:** Functions or other values that the Wasm module requires from the JavaScript/TypeScript host environment to run.

## 5. Data Exchange Between TypeScript and Wasm <a name="data-exchange-ts-wasm"></a>
This is often the most complex part of Wasm integration.

*   **Passing Numbers:** Simple numeric types (`i32`, `i64`, `f32`, `f64`) can be passed directly as arguments and returned as results.
*   **Working with Wasm Memory (`WebAssembly.Memory`):** Wasm modules operate on a linear block of memory (`ArrayBuffer`) that is accessible from both Wasm and JavaScript. To pass complex data like strings or arrays, you typically write the data into this shared memory from the JavaScript side, pass a pointer (an integer offset) and length to the Wasm function, and then the Wasm function reads from that memory location.
    ```typescript
    // Assuming 'memory' is an exported WebAssembly.Memory instance
    // const memory = instance.exports.memory as WebAssembly.Memory;
    // const buffer = new Uint8Array(memory.buffer);
    // // Write data to buffer[offset]...
    // wasmExports.process_data(offset, length);
    ```
*   **Strategies for Passing Strings:**
    1.  Write the string (UTF-8 encoded) into Wasm memory from JS.
    2.  Pass the pointer and byte length to Wasm.
    3.  Wasm reads the bytes and reconstructs the string.
    4.  For returning strings from Wasm, Wasm writes to memory, returns pointer/length, JS reads.
    *   Helper functions are often needed on both sides.
*   **Handling Complex Data Structures:**
    *   **Serialization:** Convert objects/arrays to a format like JSON or Protocol Buffers, pass the serialized byte array through Wasm memory.
    *   **Direct Memory Manipulation:** Define a clear memory layout for your structures. This is more performant but much more complex and error-prone. Tools like AssemblyScript or Rust's `wasm-bindgen` abstract much of this.
*   **Memory Management Considerations:**
    *   Wasm memory can grow but doesn't shrink automatically.
    *   If Wasm allocates memory for data it returns to JS (e.g., a string), JS needs a way to tell Wasm to deallocate that memory once it's done to prevent memory leaks. This often involves exporting `allocate` and `deallocate` functions from the Wasm module.

## 6. Tools and Languages for Creating Wasm Modules <a name="tools-languages-wasm"></a>
*   **C/C++ with Emscripten:** A powerful toolchain that compiles C/C++ code to Wasm. It can also generate JavaScript glue code for easier integration.
*   **Rust:** Has first-class Wasm support. The `wasm-pack` tool and crates like `wasm-bindgen` make it relatively easy to build Wasm modules that interoperate smoothly with JavaScript.
*   **Go:** The Go compiler has a `js/wasm` target. It's functional but might produce larger Wasm binaries and has some specific interop patterns.
*   **AssemblyScript:** (See next section)

## 7. Introduction to AssemblyScript <a name="introduction-assemblyscript"></a>
AssemblyScript is a variant of TypeScript that compiles to WebAssembly. It offers a familiar syntax for TypeScript/JavaScript developers, making the entry into Wasm development smoother.

### What is AssemblyScript?
*   Statically-typed language, very similar to TypeScript.
*   Compiles to lean and efficient Wasm.
*   Manages its own memory (garbage collection or manual, depending on configuration).
*   Provides built-in types and standard library optimized for Wasm.

### Syntax Similarities and Differences
*   **Similar:** Classes, functions, variables, basic types (`i32`, `f64`, `bool`, `string`).
*   **Different:** No `any` or `unknown` in the same way. Stricter typing. No direct DOM access (Wasm is sandboxed). Different standard library.

### Basic Example (AssemblyScript - `assembly/index.ts`)
```typescript
// This is AssemblyScript code
export function add(a: i32, b: i32): i32 {
  return a + b;
}

export function greet(name: string): string {
  return "Hello, " + name + " from AssemblyScript!";
}
```
Compile using the AssemblyScript compiler (`asc`):
`npx asc assembly/index.ts --target release --exportRuntime --outFile build/optimized.wasm`

### Using the AssemblyScript Wasm module in TypeScript
Loading an AssemblyScript module is similar to other Wasm modules. AssemblyScript often provides loader helpers to simplify string passing and other interop tasks.

```typescript
// In your main TypeScript file (e.g., main.ts)
import { AsbindInstance, Asbind } from "as-bind"; // A popular loader for AssemblyScript

interface MyAssemblyScriptExports {
  add: (a: number, b: number) => number;
  greet: (name: string) => string;
}

async function runAssemblyScript() {
  try {
    const wasm = await fetch("./build/optimized.wasm");
    const asbindInstance = await Asbind.instantiate(wasm, {
      // imports if any
    });
    const exports = asbindInstance.exports as MyAssemblyScriptExports;

    console.log("AS Add:", exports.add(10, 20)); // Output: AS Add: 30
    console.log("AS Greet:", exports.greet("TypeScript")); // Output: AS Greet: Hello, TypeScript from AssemblyScript!

  } catch (err) {
    console.error("AssemblyScript Wasm failed:", err);
  }
}
runAssemblyScript();
```
*(Note: `as-bind` is one option; other loaders or manual interop are also possible.)*

## 8. Practical Example: A Simple Wasm Math Utility in TypeScript <a name="practical-example-wasm"></a>
Let's imagine we have a `math.wasm` module (created from C++, Rust, or AssemblyScript) that exports a `factorial` function.

**AssemblyScript (`assembly/math.ts`):**
```typescript
export function factorial(n: i32): i64 {
  if (n < 0) return -1; // Or throw an error
  if (n === 0) return 1;
  let result: i64 = 1;
  for (let i: i32 = 1; i <= n; ++i) {
    result *= i;
  }
  return result;
}
```
Compile it: `npx asc assembly/math.ts -b build/math.wasm -t build/math.wat --optimize`

**TypeScript (`main.ts`):**
```typescript
async function useMathWasm() {
  try {
    const response = await fetch('./build/math.wasm');
    const { instance } = await WebAssembly.instantiateStreaming(response);
    const exports = instance.exports as { factorial: (n: number) => BigInt }; // Note: i64 maps to BigInt in JS

    const num = 5;
    const result = exports.factorial(num);
    console.log(`Factorial of ${num} (from Wasm) is ${result}`); // Factorial of 5 (from Wasm) is 120n

  } catch (err) {
    console.error("Math Wasm failed:", err);
  }
}
useMathWasm();
```

## 9. Advanced Topics and Considerations <a name="advanced-topics-wasm"></a>
*   **WASI (WebAssembly System Interface):** An API standard for Wasm modules to interact with the underlying system (e.g., file system, network) in a portable way, enabling Wasm usage outside browsers.
*   **Threading:** Wasm has experimental support for threads.
*   **SIMD (Single Instruction, Multiple Data):** Allows performing the same operation on multiple data points simultaneously, boosting performance for certain tasks.
*   **Debugging Wasm:** Browser developer tools are improving Wasm debugging support (e.g., stepping through `.wat` text format, source maps).
*   **Security Implications:** Wasm runs sandboxed, but care must be taken with data passed between JS and Wasm, and with the capabilities granted to Wasm modules (e.g., via imports).

## 10. Limitations and When to Consider Wasm <a name="limitations-when-to-use-wasm"></a>
*   **Not a Replacement for JavaScript/TypeScript:** Wasm is best for specific, performance-critical modules, not for general application logic or DOM manipulation (which it can't do directly).
*   **Overhead of JS-Wasm Interop:** Calling Wasm functions from JS and passing complex data has some overhead. For very small, frequent calls, this overhead might negate performance gains.
*   **Complexity for Simple Tasks:** For tasks that JS handles well, introducing Wasm adds complexity (compilation, interop code).
*   **Best Suited For:**
    *   CPU-bound tasks (algorithms, data processing).
    *   Porting existing C++/Rust/Go libraries.
    *   Graphics, games, scientific computing.

## 11. Key Takeaways <a name="key-takeaways-wasm"></a>
*   WebAssembly provides a way to run high-performance code written in various languages on the web and beyond.
*   TypeScript can integrate with Wasm modules to offload computationally intensive tasks.
*   Data exchange between TypeScript and Wasm, especially for complex types, requires careful management of Wasm's linear memory.
*   Tools like Emscripten (for C/C++), `wasm-pack` (for Rust), and AssemblyScript (TypeScript-like syntax) facilitate Wasm development.
*   Wasm is a powerful complement to TypeScript for specific use cases, not a replacement.

By understanding how to bridge TypeScript and WebAssembly, you can unlock new levels of performance and capability for your web applications.

---

⬅️ [Back to TypeScript Decorators: Real-world Use Cases and Patterns](../27-ts-decorators-real-world/README.md) | ➡️ [Next: TypeScript for Full-Stack Development (Node.js, Deno)](../29-ts-fullstack-dev/README.md) *(Placeholder)*
