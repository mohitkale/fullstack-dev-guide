# 29. TypeScript for Full-Stack Development (Node.js, Deno)

TypeScript's benefits of static typing, improved code organization, and enhanced developer experience are not limited to frontend development. It's increasingly popular for building robust and scalable backend applications, primarily with Node.js, and natively with Deno, a modern runtime for JavaScript and TypeScript.

This tutorial explores how to leverage TypeScript for full-stack development, focusing on setting up projects, common patterns, popular frameworks, and comparing Node.js with Deno for TypeScript backend services.

## Table of Contents
1.  [Why TypeScript for Backend Development?](#why-ts-backend)
    *   Type Safety for APIs and Data Models
    *   Improved Refactoring and Maintainability
    *   Better Collaboration in Teams
    *   Access to Modern JavaScript Features
2.  [TypeScript with Node.js](#ts-with-nodejs)
    *   [Setting up a Node.js TypeScript Project](#setup-nodejs-ts)
        *   Initializing `package.json`
        *   Installing TypeScript and Node.js types (`typescript`, `@types/node`)
        *   Configuring `tsconfig.json` (e.g., `module: "CommonJS"` or `"ESNext"`, `outDir`, `rootDir`)
        *   Scripts for building and running (`tsc`, `nodemon`, `ts-node`)
    *   [Writing HTTP Servers](#writing-http-servers-nodejs)
        *   Using the built-in `http` module with TypeScript
        *   Popular Frameworks: Express.js, NestJS, Fastify
    *   [Working with Databases](#databases-nodejs-ts)
        *   Type-safe ORMs (e.g., TypeORM, Prisma, Sequelize with typings)
    *   [Modules and Dependency Management](#modules-nodejs-ts)
        *   CommonJS vs. ES Modules in Node.js with TypeScript
    *   [Environment Variables and Configuration](#env-config-nodejs-ts)
    *   [Testing Strategies](#testing-nodejs-ts)
3.  [TypeScript with Deno](#ts-with-deno)
    *   [Introduction to Deno](#intro-deno)
        *   Secure by Default (Explicit Permissions)
        *   TypeScript Support Out-of-the-Box (No separate `tsc` step for running)
        *   Standard Library
        *   Decentralized Package Management (URL imports)
    *   [Setting up a Deno TypeScript Project](#setup-deno-ts)
        *   No `package.json` or `node_modules` by default
        *   `deno.jsonc` or `deno.json` for configuration (optional, but recommended for larger projects)
        *   Import Maps for managing dependencies
    *   [Writing HTTP Servers](#writing-http-servers-deno)
        *   Using Deno's standard library `std/http`
        *   Popular Frameworks: Oak, Aleph.js (full-stack)
    *   [Working with Databases](#databases-deno-ts)
        *   Deno-compatible ORMs and database drivers
    *   [Permissions Model](#permissions-deno)
    *   [Testing in Deno (`Deno.test`)](#testing-deno)
4.  [Comparing Node.js (with TypeScript) and Deno](#comparing-nodejs-deno)
    *   **TypeScript Integration:** Built-in (Deno) vs. Tooling Setup (Node.js)
    *   **Security:** Explicit Permissions (Deno) vs. Full Access by Default (Node.js)
    *   **Module System:** ES Modules by default, URL imports (Deno) vs. CommonJS/ES Modules, npm (Node.js)
    *   **Standard Library:** Comprehensive (Deno) vs. Minimal, rely on npm (Node.js)
    *   **Ecosystem & Maturity:** Vast, mature (Node.js/npm) vs. Growing, newer (Deno)
    *   **Tooling:** Built-in formatter, linter, test runner (Deno) vs. Rely on external tools (Node.js)
5.  [Full-Stack TypeScript: Shared Code and Types](#fullstack-ts-shared-code)
    *   Defining shared interfaces/types for frontend and backend (e.g., API request/response shapes)
    *   Monorepo strategies (e.g., using Yarn Workspaces, Lerna, Nx, Turborepo)
6.  [Popular Backend Frameworks for TypeScript](#popular-backend-frameworks-ts)
    *   **Express.js (with `@types/express`):** Minimalist and flexible, widely adopted.
    *   **NestJS:** Progressive Node.js framework for building efficient, reliable, and scalable server-side applications. Heavily uses TypeScript and decorators.
    *   **Fastify:** Fast and low overhead web framework, for Node.js. Has good TypeScript support.
    *   **TypeORM/Prisma:** ORMs that provide excellent TypeScript integration for database interactions.
    *   **Oak (Deno):** A middleware framework for Deno's http server, inspired by Koa.
7.  [Key Considerations for Backend TypeScript Development](#key-considerations-backend-ts)
    *   Compiler Configuration (`tsconfig.json`)
    *   Build Process and Development Workflow
    *   Error Handling and Logging
    *   Asynchronous Programming (`async/await`)
8.  [Key Takeaways](#key-takeaways-fullstack-ts)

## 1. Why TypeScript for Backend Development? <a name="why-ts-backend"></a>
Using TypeScript on the backend offers significant advantages:
*   **Type Safety:** Catch errors at compile time, especially crucial for defining API contracts, data models, and service interactions.
*   **Improved Refactoring:** Types make it safer and easier to refactor large codebases.
*   **Better Collaboration:** Clear type definitions serve as documentation and make it easier for teams to understand and integrate code.
*   **Access to Modern JavaScript Features:** TypeScript allows you to use the latest ECMAScript features and compile them down to compatible versions for your target runtime.
*   **Enhanced Developer Experience:** Autocompletion, type checking, and better tooling in IDEs.

## 2. TypeScript with Node.js <a name="ts-with-nodejs"></a>
Node.js is the most established JavaScript runtime for backend development. Adding TypeScript requires a setup process.

### Setting up a Node.js TypeScript Project <a name="setup-nodejs-ts"></a>
1.  **Initialize `package.json`:**
    ```bash
    npm init -y
    # or
    yarn init -y
    ```
2.  **Install Dependencies:**
    ```bash
    npm install typescript @types/node --save-dev
    # or
    yarn add typescript @types/node --dev
    ```
    *   `typescript`: The TypeScript compiler.
    *   `@types/node`: Type definitions for Node.js built-in modules.
3.  **Configure `tsconfig.json`:** Create a `tsconfig.json` file in your project root:
    ```bash
    npx tsc --init
    # or manually create it
    ```
    A typical configuration might look like:
    ```json
    {
      "compilerOptions": {
        "target": "ES2020", // Or newer, depending on your Node.js version
        "module": "CommonJS", // Or "ESNext" if using ES Modules with Node.js
        "rootDir": "./src",
        "outDir": "./dist",
        "esModuleInterop": true,
        "forceConsistentCasingInFileNames": true,
        "strict": true,
        "skipLibCheck": true,
        "resolveJsonModule": true
      },
      "include": ["src/**/*"],
      "exclude": ["node_modules", "**/*.spec.ts"]
    }
    ```
4.  **Scripts for Building and Running:** Add scripts to `package.json`:
    ```json
    "scripts": {
      "build": "tsc",
      "start": "node dist/index.js",
      "dev": "nodemon src/index.ts", // Needs nodemon and ts-node
      "dev:ts-node": "ts-node src/index.ts" // Needs ts-node
    }
    ```
    Install development tools like `nodemon` and `ts-node`:
    ```bash
    npm install nodemon ts-node --save-dev
    ```
    *   `ts-node`: Executes TypeScript files directly without pre-compiling.
    *   `nodemon`: Monitors for file changes and automatically restarts the server.

### Writing HTTP Servers <a name="writing-http-servers-nodejs"></a>
You can use Node.js's built-in `http` module or popular frameworks.
*   **Express.js:**
    ```typescript
    // src/index.ts
    import express, { Request, Response, NextFunction } from 'express';

    const app = express();
    const port = process.env.PORT || 3000;

    app.get('/', (req: Request, res: Response) => {
      res.send('Hello from Express with TypeScript!');
    });

    app.listen(port, () => {
      console.log(`Server running at http://localhost:${port}`);
    });
    ```
    Install Express and its types: `npm install express @types/express`
*   **NestJS:** A framework built with TypeScript, heavily inspired by Angular. Provides a more structured approach with modules, controllers, services, and dependency injection.
*   **Fastify:** Known for its speed and low overhead, with good TypeScript support.

### Working with Databases <a name="databases-nodejs-ts"></a>
ORMs (Object-Relational Mappers) or query builders with TypeScript support enhance type safety:
*   **TypeORM:** A mature ORM that can run in Node.js, browser, mobile, etc. Uses decorators for entity definitions.
*   **Prisma:** A modern database toolkit with a type-safe query builder generated from your schema.
*   **Sequelize:** Another popular ORM, ensure you install `@types/sequelize`.

### Modules and Dependency Management <a name="modules-nodejs-ts"></a>
Node.js traditionally used CommonJS modules (`require`/`module.exports`). Modern Node.js versions support ES Modules (`import`/`export`).
*   If using ES Modules with Node.js and TypeScript, set `"module": "ESNext"` or similar in `tsconfig.json` and `"type": "module"` in `package.json` or use `.mts` file extensions.

## 3. TypeScript with Deno <a name="ts-with-deno"></a>
Deno is a modern and secure runtime for JavaScript and TypeScript created by Ryan Dahl (also the creator of Node.js).

### Introduction to Deno <a name="intro-deno"></a>
*   **Secure by Default:** No file, network, or environment access unless explicitly permitted via command-line flags.
*   **TypeScript Out-of-the-Box:** Deno can execute TypeScript files directly without a separate compilation step by you (it still compiles it internally).
*   **Standard Library:** Provides a comprehensive standard library, reviewed and audited, for common tasks (e.g., HTTP, file system, testing).
*   **Decentralized Package Management:** Imports modules via URLs (e.g., from `deno.land/x`, GitHub, or your own server). No central npm-like registry by default, though it can interop with npm modules.

### Setting up a Deno TypeScript Project <a name="setup-deno-ts"></a>
No `package.json` or `node_modules` folder is strictly necessary.
1.  Simply create your `.ts` files.
2.  **Configuration (Optional but Recommended):** Create `deno.json` or `deno.jsonc` for tasks, linting, formatting, and compiler options.
    ```jsonc
    // deno.jsonc
    {
      "tasks": {
        "start": "deno run --allow-net --allow-read src/main.ts",
        "dev": "deno run --watch --allow-net --allow-read src/main.ts"
      },
      "compilerOptions": {
        "lib": ["deno.window"]
      },
      "lint": {
        "files": {
          "include": ["src/"],
          "exclude": ["src/testdata/"]
        }
      },
      "fmt": {
        "files": {
          "include": ["src/"],
          "exclude": ["src/testdata/"]
        },
        "options": {
          "useTabs": false,
          "lineWidth": 80
        }
      }
    }
    ```
3.  **Import Maps (Optional):** For managing dependencies and using bare specifiers. Create an `import_map.json` file:
    ```json
    // import_map.json
    {
      "imports": {
        "oak/": "https://deno.land/x/oak@v12.6.0/",
        "std/": "https://deno.land/std@0.200.0/"
      }
    }
    ```
    Run with `--import-map=./import_map.json` or specify in `deno.jsonc`.

### Writing HTTP Servers <a name="writing-http-servers-deno"></a>
*   **Using Deno's Standard Library (`std/http`):**
    ```typescript
    // src/main.ts
    import { serve } from "std/http/server.ts";

    const port = 8000;
    console.log(`HTTP server running on http://localhost:${port}`);

    serve((_req: Request) => {
      return new Response("Hello from Deno with TypeScript!", {
        headers: { "content-type": "text/plain" },
      });
    }, { port });
    ```
    Run: `deno run --allow-net src/main.ts`
*   **Popular Frameworks:**
    *   **Oak:** A middleware framework inspired by Koa (Node.js).
    *   **Aleph.js:** A full-stack framework for Deno.

### Permissions Model <a name="permissions-deno"></a>
Deno requires explicit flags for permissions:
*   `--allow-net`: Allow network access.
*   `--allow-read`: Allow file system read access.
*   `--allow-write`: Allow file system write access.
*   `--allow-env`: Allow environment access.
*   `--allow-run`: Allow running subprocesses.
*   `-A` or `--allow-all`: Allow all permissions (use with caution).

### Testing in Deno <a name="testing-deno"></a>
Deno has a built-in test runner.
```typescript
// src/math.test.ts
import { assertEquals } from "std/testing/asserts.ts";
import { add } from "./math.ts"; // Assuming add function in math.ts

Deno.test("add function", () => {
  assertEquals(add(2, 3), 5);
});
```
Run tests: `deno test`

## 4. Comparing Node.js (with TypeScript) and Deno <a name="comparing-nodejs-deno"></a>
| Feature                | Node.js (with TypeScript)                      | Deno                                            |
| ---------------------- | ---------------------------------------------- | ----------------------------------------------- |
| **TypeScript Setup**   | Manual (install `typescript`, `ts-node`, etc.) | Built-in, runs `.ts` files directly          |
| **Security**           | Full access by default                         | Secure by default (explicit permissions)        |
| **Module System**      | CommonJS / ES Modules (npm packages)           | ES Modules only, URL imports (decentralized)    |
| **Standard Library**   | Minimal, relies on external npm packages       | Comprehensive, built-in standard library        |
| **Package Management** | npm / yarn (centralized `package.json`)        | URL imports, `import_map.json` (optional)       |
| **Ecosystem**          | Vast, mature (npm has millions of packages)    | Growing, newer, can use npm via CDN or tools    |
| **Tooling**            | Relies on external tools (ESLint, Prettier)    | Built-in formatter, linter, test runner         |
| **Async/Promises**     | Callback-heavy std lib (historically)          | Promise-based standard library                  |
| **`node_modules`**     | Yes                                            | No (modules are cached globally)                |

## 5. Full-Stack TypeScript: Shared Code and Types <a name="fullstack-ts-shared-code"></a>
One of the biggest advantages of using TypeScript across the stack is the ability to share code and types between your frontend and backend.
*   **Define common interfaces/types:** For API request/response payloads, data models, utility functions.
*   **Monorepos:** Manage frontend and backend code in a single repository using tools like:
    *   Yarn Workspaces
    *   Lerna
    *   Nx
    *   Turborepo
    This simplifies dependency management and code sharing.

## 6. Popular Backend Frameworks for TypeScript <a name="popular-backend-frameworks-ts"></a>
*   **Express.js (Node.js):** Highly flexible, vast middleware ecosystem. Use with `@types/express`.
*   **NestJS (Node.js):** Opinionated, modular framework built with TypeScript. Excellent for large applications.
*   **Fastify (Node.js):** Performance-focused with good TypeScript support.
*   **TypeORM / Prisma (Node.js/Deno):** Powerful ORMs for database interaction with strong type safety.
*   **Oak (Deno):** Koa-like middleware framework for Deno.
*   **AdonisJS (Node.js):** A fully featured MVC framework with first-class TypeScript support.

## 7. Key Considerations for Backend TypeScript Development <a name="key-considerations-backend-ts"></a>
*   **`tsconfig.json`:** Tailor compiler options for your backend environment (e.g., `target`, `module`, `outDir`).
*   **Build Process:** Decide whether to pre-compile with `tsc` or use a runtime like `ts-node` (Node.js) or Deno's built-in capabilities.
*   **Error Handling:** Implement robust error handling and logging strategies.
*   **Async Programming:** Master `async/await` for non-blocking I/O operations.
*   **Environment Configuration:** Manage environment-specific settings securely.

## 8. Key Takeaways <a name="key-takeaways-fullstack-ts"></a>
*   TypeScript significantly enhances backend development by providing type safety, better maintainability, and improved developer experience.
*   **Node.js with TypeScript** is a mature and widely adopted solution, requiring a build setup but benefiting from the vast npm ecosystem.
*   **Deno** offers a modern, secure, TypeScript-first backend runtime with a comprehensive standard library and built-in tooling.
*   Choosing between Node.js and Deno depends on project requirements, team familiarity, and ecosystem needs.
*   Full-stack TypeScript enables code and type sharing between frontend and backend, improving consistency and reducing errors.

By leveraging TypeScript for your backend, you can build more reliable, scalable, and maintainable applications, regardless of whether you choose the established Node.js ecosystem or the modern approach of Deno.

---

⬅️ [Back to TypeScript and WebAssembly (Wasm)](../28-ts-and-webassembly/README.md) | ➡️ [Next: TypeScript with GraphQL (Client and Server)](../30-ts-with-graphql/README.md) *(Placeholder)*
