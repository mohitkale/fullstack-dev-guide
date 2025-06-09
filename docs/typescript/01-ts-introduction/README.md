# 01. Introduction to TypeScript (What, Why, Setup)

Welcome to the first tutorial in our dedicated TypeScript series! TypeScript is a powerful, open-source language developed and maintained by Microsoft. It builds on JavaScript by adding static type definitions, which can significantly improve code quality, developer productivity, and the maintainability of projects, especially as they grow in scale and complexity.

This tutorial will introduce you to the core concepts of TypeScript, explain its benefits, and guide you through setting up your development environment.

## Table of Contents
1.  [What is TypeScript?](#what-is-typescript)
    *   [Superset of JavaScript](#superset-of-js)
    *   [Static Typing](#static-typing)
    *   [The TypeScript Compiler (TSC)](#tsc-compiler)
2.  [Why Use TypeScript? (Benefits)](#why-use-typescript)
    *   [Early Error Detection](#early-error-detection)
    *   [Improved Code Readability and Maintainability](#improved-readability)
    *   [Enhanced Developer Experience & Tooling](#enhanced-tooling)
    *   [Better Team Collaboration](#better-collaboration)
    *   [Scalability for Large Projects](#scalability)
    *   [Gradual Adoption](#gradual-adoption)
3.  [Setting Up Your TypeScript Environment](#setting-up-environment)
    *   [Prerequisites: Node.js and npm/yarn](#prerequisites)
    *   [Installing TypeScript](#installing-typescript)
        *   [Global Installation](#global-installation)
        *   [Local (Project-based) Installation](#local-installation)
    *   [Your First TypeScript File](#first-ts-file)
    *   [Compiling TypeScript (`tsc`)](#compiling-with-tsc)
    *   [Introduction to `tsconfig.json`](#intro-tsconfig)
    *   [Running TypeScript Code](#running-ts-code)
        *   [Compile and Run JavaScript](#compile-and-run-js)
        *   [Using `ts-node` (for development)](#using-ts-node)
4.  [Key Takeaways](#key-takeaways-intro)

## 1. What is TypeScript? <a name="what-is-typescript"></a>

### Superset of JavaScript <a name="superset-of-js"></a>
TypeScript is often described as a **superset of JavaScript**. This means that any valid JavaScript code is also valid TypeScript code. TypeScript adds optional static types and other features on top of JavaScript, which are then compiled down to plain JavaScript that can run in any browser or Node.js environment.

### Static Typing <a name="static-typing"></a>
The core feature of TypeScript is **static typing**. In JavaScript (a dynamically typed language), variable types are checked at runtime. In TypeScript, you can declare the types of variables, function parameters, and return values. These types are checked by the TypeScript compiler during development (at compile time), before the code is even run.

```typescript
// JavaScript (dynamic typing)
let myVariable = "Hello";
myVariable = 10; // No error until runtime, potentially

// TypeScript (static typing)
let myTypedVariable: string = "Hello TypeScript";
// myTypedVariable = 10; // Error: Type 'number' is not assignable to type 'string'.
```

### The TypeScript Compiler (TSC) <a name="tsc-compiler"></a>
TypeScript code itself cannot be directly executed by browsers or Node.js. It needs to be **compiled** into standard JavaScript. This is done using the TypeScript Compiler, commonly known as `tsc`.

## 2. Why Use TypeScript? (Benefits) <a name="why-use-typescript"></a>

### Early Error Detection <a name="early-error-detection"></a>
Static typing allows you to catch many common errors during development, such as typos, incorrect function arguments, or invalid assignments, long before they reach production or even testing.

### Improved Code Readability and Maintainability <a name="improved-readability"></a>
Explicit types make code easier to understand. When you see a function signature with types, you immediately know what kind of data it expects and what it returns. This makes codebases easier to navigate, refactor, and maintain over time.

### Enhanced Developer Experience & Tooling <a name="enhanced-tooling"></a>
TypeScript powers rich tooling in code editors (like VS Code, WebStorm, etc.). This includes:
*   **Intelligent Autocompletion**: More accurate and context-aware suggestions.
*   **Refactoring**: Safer renaming and code modifications.
*   **Interface Information**: Hovering over variables or functions shows their types.
*   **Error Highlighting**: Instant feedback on type errors.

### Better Team Collaboration <a name="better-collaboration"></a>
Types act as contracts between different parts of the code or between different developers. When an API or a function has clear type definitions, it reduces misunderstandings and integration issues.

### Scalability for Large Projects <a name="scalability"></a>
As projects grow, managing a large JavaScript codebase can become challenging. TypeScript's structure and type safety help maintain control and reduce complexity, making it easier to scale applications.

### Gradual Adoption <a name="gradual-adoption"></a>
Since TypeScript is a superset of JavaScript, you can introduce it into existing JavaScript projects gradually. You can start by typing a few files and expand its usage over time.

## 3. Setting Up Your TypeScript Environment <a name="setting-up-environment"></a>

### Prerequisites: Node.js and npm/yarn <a name="prerequisites"></a>
To use TypeScript, you'll need Node.js installed on your system. Node.js comes with npm (Node Package Manager). You can also use Yarn, another popular package manager.
*   Download Node.js: [https://nodejs.org/](https://nodejs.org/)

Verify installation:
```bash
node -v
npm -v
# or if you use yarn
yarn -v
```

### Installing TypeScript <a name="installing-typescript"></a>
You can install TypeScript globally or locally per project.

#### Global Installation <a name="global-installation"></a>
Installing TypeScript globally makes the `tsc` command available anywhere on your system.
```bash
npm install -g typescript
```
Verify installation:
```bash
tsc -v
```

#### Local (Project-based) Installation <a name="local-installation"></a>
It's often recommended to install TypeScript as a development dependency for each project. This ensures that all team members use the same TypeScript version and makes your project self-contained.

1.  Navigate to your project directory (or create a new one):
    ```bash
    mkdir my-ts-project
    cd my-ts-project
    npm init -y  # Initialize a new npm project (creates package.json)
    ```
2.  Install TypeScript locally:
    ```bash
    npm install --save-dev typescript
    # or
    yarn add --dev typescript
    ```
    When installed locally, you can run `tsc` using `npx tsc` or by adding it to your `package.json` scripts.

### Your First TypeScript File <a name="first-ts-file"></a>
Create a new file named `hello.ts` (TypeScript files use the `.ts` extension, or `.tsx` for files containing JSX).

```typescript
// hello.ts
function greet(name: string): string {
  return `Hello, ${name}!`;
}

let user: string = "TypeScript User";
console.log(greet(user));
```

### Compiling TypeScript (`tsc`) <a name="compiling-with-tsc"></a>
To compile your `hello.ts` file into JavaScript, run the `tsc` command:

If installed globally:
```bash
tsc hello.ts
```
If installed locally:
```bash
npx tsc hello.ts
```
This will generate a `hello.js` file in the same directory:
```javascript
// hello.js (compiled output)
function greet(name) {
    return "Hello, ".concat(name, "!");
}
var user = "TypeScript User";
console.log(greet(user));
```
You can then run this JavaScript file using Node.js: `node hello.js`.

### Introduction to `tsconfig.json` <a name="intro-tsconfig"></a>
For projects with more than one file, or to customize compiler behavior, you use a `tsconfig.json` file. This file specifies the root files and compiler options for your project.

To generate a `tsconfig.json` file with default options, run:
```bash
tsc --init # If tsc is global
# or
npx tsc --init # If tsc is local
```
This creates a `tsconfig.json` file with many commented-out options. We'll explore this file in detail in a later tutorial. For now, know that if `tsconfig.json` exists, running `tsc` (without specifying input files) will compile all files included by the configuration.

### Running TypeScript Code <a name="running-ts-code"></a>

#### Compile and Run JavaScript <a name="compile-and-run-js"></a>
The standard way is to compile your `.ts` files to `.js` using `tsc`, and then run the resulting `.js` files with Node.js or in a browser.

```bash
tsc # Compiles based on tsconfig.json or specific files
node dist/yourOutputFile.js # Assuming output is in 'dist' directory
```

#### Using `ts-node` (for development) <a name="using-ts-node"></a>
For development, `ts-node` is a useful tool. It's a TypeScript execution engine and REPL for Node.js that allows you to run TypeScript files directly without pre-compiling them to JavaScript.

1.  Install `ts-node` (usually as a dev dependency):
    ```bash
    npm install --save-dev ts-node
    # or
    yarn add --dev ts-node
    ```
2.  Run your TypeScript file:
    ```bash
    npx ts-node hello.ts
    ```
    `ts-node` compiles and runs the code in one step, making it convenient for development and running scripts.

## 4. Key Takeaways <a name="key-takeaways-intro"></a>
*   TypeScript is a superset of JavaScript that adds optional static typing.
*   It helps catch errors early, improves code readability, and enhances developer tooling.
*   TypeScript code is compiled to plain JavaScript using the `tsc` compiler.
*   You can install TypeScript globally or locally per project (recommended).
*   A `tsconfig.json` file configures the TypeScript compiler for your project.
*   `ts-node` is a handy tool for running TypeScript files directly during development.

This introduction should give you a good starting point. In the next tutorial, we'll dive into TypeScript's basic types.

---

➡️ [Next: Basic Types](../02-ts-basic-types/README.md)
