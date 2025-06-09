# 44. TypeScript with WebAssembly (WASM) - In-Depth

WebAssembly (WASM) is a binary instruction format for a stack-based virtual machine. It's designed as a portable compilation target for high-level languages like C, C++, Rust, and others, enabling deployment on the web for client and server applications. TypeScript can interact with WASM modules, allowing developers to leverage performance-critical code written in other languages within their JavaScript/TypeScript applications.

This tutorial provides an in-depth look at using TypeScript with WebAssembly.

## Table of Contents
1.  [Understanding WebAssembly (WASM)](#understanding-wasm)
    *   What is WASM? (Binary format, performance, portability)
    *   Why use WASM? (Speed, code reuse, language diversity)
    *   WASM Core Concepts: Modules, Instances, Memory, Tables, Imports, Exports.
2.  [TypeScript and WASM Interaction Overview](#ts-wasm-interaction)
    *   How TypeScript can load and run WASM modules.
    *   The role of JavaScript as the "glue" code.
    *   Benefits of using TypeScript for WASM interop (type safety).
3.  [Compiling Languages to WASM](#compiling-to-wasm)
    *   [**Rust to WASM**](#rust-to-wasm)
        *   Using `wasm-pack` and `wasm-bindgen`.
        *   Generating JavaScript/TypeScript bindings for Rust code.
        *   Example: A simple Rust function compiled to WASM.
    *   [**C/C++ to WASM**](#c-cpp-to-wasm)
        *   Using Emscripten SDK.
        *   Generating `.wasm` files and JavaScript glue code.
        *   Example: A C function compiled to WASM.
    *   [**AssemblyScript to WASM**](#assemblyscript-to-wasm)
        *   TypeScript-like syntax that compiles directly to WASM.
        *   Tooling and compilation process.
        *   Example: An AssemblyScript class compiled to WASM.
    *   Other languages (Go, Swift, C# via Blazor, etc.).
4.  [Loading and Instantiating WASM Modules in TypeScript](#loading-wasm-ts)
    *   [**In the Browser**](#wasm-browser-ts)
        *   Using `WebAssembly.instantiateStreaming()` (preferred) or `WebAssembly.instantiate()`.
        *   Fetching the `.wasm` file.
        *   Providing import objects.
    *   [**In Node.js**](#wasm-nodejs-ts)
        *   Reading the `.wasm` file using `fs` module.
        *   Using `WebAssembly.instantiate()` with the buffer.
    *   [**In Deno**](#wasm-deno-ts)
        *   Reading the `.wasm` file using `Deno.readFile()`.
        *   Using `WebAssembly.instantiate()`.
        *   (Covered briefly in Tutorial #43).
5.  [Type-Safe Communication Between TypeScript and WASM](#type-safe-interop-ts)
    *   [**Calling WASM Functions from TypeScript**](#call-wasm-from-ts)
        *   Accessing exported functions from the WASM instance.
        *   Type-casting exported functions for TypeScript type safety.
        *   Passing primitive types (numbers, booleans).
        *   Working with complex data types (strings, arrays, objects) via WASM memory.
    *   [**Calling TypeScript/JavaScript Functions from WASM**](#call-ts-from-wasm)
        *   Defining JavaScript functions in the import object.
        *   Calling imported functions from within the WASM module.
        *   Type considerations for imported functions.
6.  [Managing WASM Memory with TypeScript](#wasm-memory-ts)
    *   Understanding `WebAssembly.Memory`.
    *   Reading from and writing to WASM linear memory from TypeScript.
        *   Using `Uint8Array`, `Int32Array`, etc., on `memory.buffer`.
    *   Allocating and deallocating memory within the WASM module (manual memory management in C/C++/Rust without `wasm-bindgen`'s help).
    *   Strategies for passing complex data (serialization, pointers).
    *   The role of tools like `wasm-bindgen` in abstracting memory management.
7.  [Advanced Topics and Techniques](#wasm-advanced-ts)
    *   Using Tables (e.g., for function pointers).
    *   Multi-threading with WASM (experimental, requires specific browser/runtime support).
    *   SIMD (Single Instruction, Multiple Data) for parallel computation.
    *   Streaming compilation and instantiation for faster startups.
    *   Debugging WASM and TypeScript interop.
8.  [Tools and Libraries for TypeScript-WASM Development](#wasm-tools-libs-ts)
    *   `wasm-pack`: For building and packaging Rust-generated WASM.
    *   `wasm-bindgen`: Generates JS/TS bindings for Rust WASM modules.
    *   Emscripten: Toolchain for compiling C/C++ to WASM.
    *   AssemblyScript Compiler (`asc`): Compiles AssemblyScript to WASM.
    *   Wabt (WebAssembly Binary Toolkit): For inspecting and manipulating WASM files.
9.  [Practical Use Cases and Examples](#wasm-use-cases-ts)
    *   CPU-intensive tasks: Image/video processing, cryptography, physics simulations.
    *   Reusing existing C/C++/Rust libraries on the web.
    *   Game development (engines like Unity, Unreal Engine export to WASM).
    *   Portable command-line tools (via WASI - WebAssembly System Interface).
10. [Performance Considerations](#wasm-performance-ts)
    *   WASM vs. JavaScript performance characteristics.
    *   Overhead of calling between JS/TS and WASM.
    *   Optimizing data transfer.
11. [Security Considerations](#wasm-security-ts)
    *   WASM runs in the same sandbox as JavaScript.
    *   Potential vulnerabilities if interacting with untrusted WASM modules.
12. [Key Takeaways](#key-takeaways-ts-wasm)

## 1. Understanding WebAssembly (WASM) <a name="understanding-wasm"></a>
WASM is a low-level, assembly-like language with a compact binary format. It runs with near-native performance and provides a way to run code written in multiple languages on the web, and is increasingly used in server-side environments as well.

## 2. TypeScript and WASM Interaction Overview <a name="ts-wasm-interaction"></a>
TypeScript doesn't compile directly to WASM (AssemblyScript is an exception). Instead, TypeScript code acts as the host environment that loads, compiles, and instantiates WASM modules. It then interacts with the WASM module by calling its exported functions and providing imported functions that the WASM module can call.

## 3. Compiling Languages to WASM <a name="compiling-to-wasm"></a>

### Rust to WASM <a name="rust-to-wasm"></a>
Rust has excellent WASM support, largely thanks to `wasm-pack` and `wasm-bindgen`.
```rust
// lib.rs (Rust)
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
pub fn greet(name: &str) -> String {
  format!("Hello, {}! From Rust via WASM.", name)
}
```
Compile with `wasm-pack build --target web` (or `nodejs`, `deno`). This generates a `.wasm` file and JavaScript/TypeScript glue code.

### C/C++ to WASM <a name="c-cpp-to-wasm"></a>
Emscripten is the primary toolchain.
```c
// main.c
#include <stdio.h>
#include <emscripten.h>

EMSCRIPTEN_KEEPALIVE
int add(int a, int b) {
  return a + b;
}
```
Compile: `emcc main.c -o main.js -s WASM=1 -s EXPORTED_FUNCTIONS='["_add"]'`.

### AssemblyScript to WASM <a name="assemblyscript-to-wasm"></a>
AssemblyScript uses TypeScript-like syntax.
```typescript
// assembly/index.ts (AssemblyScript)
export function multiply(a: i32, b: i32): i32 {
  return a * b;
}
```
Compile with `asc assembly/index.ts --target release`.

## 4. Loading and Instantiating WASM Modules in TypeScript <a name="loading-wasm-ts"></a>

### In the Browser <a name="wasm-browser-ts"></a>
```typescript
async function loadWasmBrowser() {
  try {
    const response = await fetch('module.wasm');
    const importObject = { /* ... imports if any ... */ };
    const { instance, module } = await WebAssembly.instantiateStreaming(response, importObject);
    
    // Type assertion for exported function
    const greet = instance.exports.greet as (namePtr: number, nameLen: number) => number; // Example if greet returns pointer to string
    // Or if wasm-bindgen generated JS/TS:
    // import init, { greet } from './pkg/my_wasm_module';
    // await init();
    // console.log(greet('Browser'));

    console.log('WASM module loaded and instantiated in browser.');
  } catch (err) {
    console.error('Error loading WASM:', err);
  }
}
```

### In Node.js <a name="wasm-nodejs-ts"></a>
```typescript
import * as fs from 'fs/promises';

async function loadWasmNode() {
  try {
    const wasmBuffer = await fs.readFile('module.wasm');
    const importObject = { /* ... imports if any ... */ };
    const { instance, module } = await WebAssembly.instantiate(wasmBuffer, importObject);

    // const greet = instance.exports.greet as ...
    console.log('WASM module loaded and instantiated in Node.js.');
  } catch (err) {
    console.error('Error loading WASM:', err);
  }
}
```

## 5. Type-Safe Communication Between TypeScript and WASM <a name="type-safe-interop-ts"></a>

### Calling WASM Functions from TypeScript <a name="call-wasm-from-ts"></a>
When not using tools like `wasm-bindgen` that generate typed wrappers, you manually cast exported functions.
```typescript
interface MyWasmExports {
  add: (a: number, b: number) => number;
  // ... other exported functions
}

// After instantiation
const wasmExports = instance.exports as unknown as MyWasmExports;
const sum = wasmExports.add(5, 7);
console.log(`Sum from WASM: ${sum}`);
```
For complex types like strings, you'll often work with pointers and lengths, reading/writing directly to `WebAssembly.Memory`.

### Calling TypeScript/JavaScript Functions from WASM <a name="call-ts-from-wasm"></a>
Define functions in the `importObject` passed during instantiation.
```typescript
// TypeScript host
const importObject = {
  env: {
    log_message: (ptr: number, len: number) => {
      // Assume 'memory' is WebAssembly.Memory instance
      const messageBytes = new Uint8Array(memory.buffer, ptr, len);
      const message = new TextDecoder().decode(messageBytes);
      console.log(`Message from WASM: ${message}`);
    }
  }
};
// ... instantiate with this importObject
```
```rust
// Rust WASM module
#[wasm_bindgen]
extern "C" {
    #[wasm_bindgen(js_namespace = env)]
    fn log_message(ptr: *const u8, len: usize);
}

#[wasm_bindgen]
pub fn do_work_and_log() {
    let msg = "Hello from Rust WASM!";
    unsafe {
        log_message(msg.as_ptr(), msg.len());
    }
}
```

## 6. Managing WASM Memory with TypeScript <a name="wasm-memory-ts"></a>
WASM modules have their own linear memory, accessible from TypeScript via `instance.exports.memory` (if exported) which is a `WebAssembly.Memory` object.
```typescript
const memory: WebAssembly.Memory = instance.exports.memory as WebAssembly.Memory;

// Write a string to WASM memory for WASM to use
function writeStringToWasmMemory(str: string, offset: number): void {
  const buffer = new Uint8Array(memory.buffer, offset, str.length);
  const encodedStr = new TextEncoder().encode(str);
  buffer.set(encodedStr);
}

// Read a null-terminated string from WASM memory
function readStringFromWasmMemory(ptr: number): string {
  const bytes = new Uint8Array(memory.buffer);
  let end = ptr;
  while (bytes[end] !== 0) {
    end++;
  }
  return new TextDecoder().decode(bytes.slice(ptr, end));
}
```
Tools like `wasm-bindgen` automate much of this complex data marshalling.

## 7. Advanced Topics and Techniques <a name="wasm-advanced-ts"></a>
*   **WASI (WebAssembly System Interface):** An API for WASM to interact with the underlying system (filesystem, network, etc.) in a portable way. Useful for running WASM outside the browser.
*   **SIMD:** For data-parallel operations, can offer significant speedups.
*   **Multi-threading:** Still evolving, but allows WASM to use multiple cores.

## 8. Tools and Libraries for TypeScript-WASM Development <a name="wasm-tools-libs-ts"></a>
*   **`wasm-pack` / `wasm-bindgen` (Rust):** The de-facto standard for Rust to WASM, generates excellent TypeScript bindings.
*   **Emscripten (C/C++):** Mature toolchain, can produce JS glue code.
*   **AssemblyScript Compiler (`asc`):** Provides a TypeScript-like experience for writing WASM.

## 9. Practical Use Cases and Examples <a name="wasm-use-cases-ts"></a>
*   **Performance-Critical Libraries:** Figma (graphics editor), Google Earth (rendering).
*   **Game Engines:** Unity, Unreal Engine can target WASM.
*   **Serverless Functions:** Running WASM in cloud functions for speed and portability.
*   **Data Processing and Analysis:** Libraries like Apache Arrow.

## 10. Performance Considerations <a name="wasm-performance-ts"></a>
*   WASM generally executes faster than JS for CPU-bound tasks.
*   The cost of calling between JS/TS and WASM (the "JS-WASM boundary") can be significant if done frequently with small amounts of data. Batch operations where possible.
*   Data transfer between JS/TS and WASM memory also has overhead.

## 11. Security Considerations <a name="wasm-security-ts"></a>
WASM modules run in the same sandbox as JavaScript, with the same security restrictions. They cannot directly access arbitrary system resources unless explicitly allowed by the host environment (e.g., via imported functions or WASI).

## 12. Key Takeaways <a name="key-takeaways-ts-wasm"></a>
*   WASM allows leveraging high-performance code from languages like Rust, C++, and AssemblyScript within TypeScript applications.
*   TypeScript provides the host environment and type-safe glue for interacting with WASM modules.
*   Tooling like `wasm-bindgen` (for Rust) and Emscripten (for C/C++) are essential for bridging the gap and managing complex data types.
*   Understanding WASM memory management is key for efficient and correct interoperation, especially when not using high-level binding tools.
*   WASM is a powerful technology for performance-sensitive web and server-side applications.

---

⬅️ [Back to Advanced TypeScript with Deno](../43-ts-deno-advanced/README.md) | ➡️ [Next: Design Patterns in TypeScript with Real-World Examples](../45-ts-design-patterns-real-world/README.md) *(Placeholder)*
