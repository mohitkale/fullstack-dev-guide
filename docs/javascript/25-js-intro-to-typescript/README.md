# 25. Introduction to TypeScript (Superset of JavaScript)

TypeScript is an open-source language developed and maintained by Microsoft. It is a strict syntactical superset of JavaScript, which means that any valid JavaScript code is also valid TypeScript code. TypeScript adds optional static typing, classes, interfaces, and other features to JavaScript, enabling developers to build large-scale, robust applications more effectively.

This tutorial provides a basic introduction to TypeScript, its core concepts, and how it can enhance your JavaScript development experience.

## Table of Contents
1.  [What is TypeScript?](#what-is-typescript)
    *   [Why Use TypeScript? Benefits](#why-typescript-benefits)
    *   [Relationship with JavaScript](#ts-js-relationship)
2.  [Setting Up a TypeScript Project](#setting-up-typescript)
    *   [Installing TypeScript](#installing-typescript)
    *   [TypeScript Compiler (`tsc`)](#typescript-compiler)
    *   [Configuration File (`tsconfig.json`)](#tsconfig-json)
3.  [Basic Types](#basic-types-ts)
    *   [Boolean](#type-boolean)
    *   [Number](#type-number)
    *   [String](#type-string)
    *   [Array](#type-array)
    *   [Tuple](#type-tuple)
    *   [Enum](#type-enum)
    *   [Any](#type-any)
    *   [Void](#type-void)
    *   [Null and Undefined](#type-null-undefined)
    *   [Never](#type-never)
    *   [Object](#type-object)
    *   [Type Assertions (Casting)](#type-assertions)
4.  [Interfaces](#interfaces-ts)
    *   [Defining an Interface](#defining-interface)
    *   [Optional Properties](#optional-properties)
    *   [Readonly Properties](#readonly-properties)
    *   [Function Types](#function-types-interface)
    *   [Indexable Types](#indexable-types-interface)
    *   [Class Types (Implementing an Interface)](#class-types-interface)
    *   [Extending Interfaces](#extending-interfaces)
5.  [Classes](#classes-ts)
    *   [Basic Class Definition](#basic-class-definition-ts)
    *   [Inheritance](#inheritance-ts)
    *   [Access Modifiers (`public`, `private`, `protected`)](#access-modifiers-ts)
    *   [Readonly Modifier for Properties](#readonly-modifier-class)
    *   [Static Properties and Methods](#static-properties-methods-ts)
    *   [Abstract Classes](#abstract-classes-ts)
6.  [Functions in TypeScript](#functions-ts)
    *   [Typed Parameters and Return Types](#typed-parameters-return)
    *   [Optional and Default Parameters](#optional-default-parameters-ts)
    *   [Rest Parameters](#rest-parameters-ts)
    *   [Function Overloads](#function-overloads-ts)
7.  [Enums (Recap and More)](#enums-ts-more)
    *   [Numeric Enums](#numeric-enums)
    *   [String Enums](#string-enums)
    *   [Heterogeneous Enums](#heterogeneous-enums)
    *   [Computed and Constant Members](#computed-constant-enums)
8.  [Generics](#generics-ts)
    *   [What are Generics? (Motivation)](#what-are-generics)
    *   [Generic Functions](#generic-functions)
    *   [Generic Interfaces](#generic-interfaces)
    *   [Generic Classes](#generic-classes)
    *   [Generic Constraints](#generic-constraints)
9.  [Type Inference](#type-inference-ts)
10. [Type Compatibility (Structural Typing)](#type-compatibility-ts)
11. [Advanced Types (Brief Overview)](#advanced-types-overview-ts)
    *   [Union Types (`|`)](#union-types)
    *   [Intersection Types (`&`)](#intersection-types)
    *   [Type Aliases (`type`)](#type-aliases)
    *   [Literal Types](#literal-types)
12. [Modules in TypeScript](#modules-ts)
    *   [Follows ES6 Module Syntax](#ts-es6-modules)
13. [Key Takeaways](#key-takeaways-typescript)

## 1. What is TypeScript? <a name="what-is-typescript"></a>
TypeScript is JavaScript with syntax for types. It's a strongly typed programming language that builds on JavaScript, giving you better tooling at any scale.

### Why Use TypeScript? Benefits <a name="why-typescript-benefits"></a>
*   **Static Typing**: Catch errors during development (at compile time) rather than at runtime. This leads to fewer bugs in production.
*   **Improved Code Readability and Maintainability**: Types make code easier to understand and reason about, especially in large codebases and when working in teams.
*   **Better Tooling and IDE Support**: Enhanced autocompletion, refactoring, and error checking in code editors.
*   **Scalability**: Helps manage complexity as projects grow.
*   **Access to Modern JavaScript Features**: TypeScript supports the latest ECMAScript features and can compile them down to older JavaScript versions for broader compatibility.
*   **Gradual Adoption**: Since JavaScript is valid TypeScript, you can gradually introduce TypeScript into existing JavaScript projects.

### Relationship with JavaScript <a name="ts-js-relationship"></a>
*   TypeScript is a superset of JavaScript.
*   All JavaScript code is valid TypeScript code.
*   TypeScript code needs to be compiled (transpiled) into plain JavaScript to run in browsers or Node.js environments that don't natively support TypeScript execution.

## 2. Setting Up a TypeScript Project <a name="setting-up-typescript"></a>

### Installing TypeScript <a name="installing-typescript"></a>
You can install TypeScript globally or per project using npm or yarn:
```bash
# Global installation
npm install -g typescript

# Project-level installation (recommended)
npm install --save-dev typescript
# or
yarn add --dev typescript
```

### TypeScript Compiler (`tsc`) <a name="typescript-compiler"></a>
The TypeScript compiler, `tsc`, is used to compile `.ts` files into `.js` files.
```bash
# Compile a single file
tsc main.ts # Output: main.js

# Initialize a tsconfig.json file
tsc --init

# Compile project based on tsconfig.json
tsc
```

### Configuration File (`tsconfig.json`) <a name="tsconfig-json"></a>
A `tsconfig.json` file in the root of a TypeScript project specifies compiler options and project files.
Running `tsc --init` creates a basic `tsconfig.json` file.
Key options include:
*   `target`: Specifies the ECMAScript target version (e.g., `"ES5"`, `"ES2015"`, `"ESNext"`).
*   `module`: Specifies the module system (e.g., `"commonjs"`, `"es2015"`, `"esnext"`).
*   `outDir`: Redirect output structure to the specified directory.
*   `rootDir`: Specifies the root directory of input files.
*   `strict`: Enables all strict type-checking options.
*   `esModuleInterop`: Enables interoperability between CommonJS and ES Modules.
*   `sourceMap`: Generates corresponding `.map` files for debugging.

Example `tsconfig.json`:
```json
{
  "compilerOptions": {
    "target": "es6",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "./dist",
    "rootDir": "./src"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "**/*.spec.ts"]
}
```

## 3. Basic Types <a name="basic-types-ts"></a>
TypeScript adds type annotations to JavaScript's primitive types and introduces some new ones.

### Boolean <a name="type-boolean"></a>
```typescript
let isActive: boolean = true;
```

### Number <a name="type-number"></a>
All numbers in TypeScript are floating-point values.
```typescript
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
```

### String <a name="type-string"></a>
```typescript
let color: string = "blue";
color = 'red';
let fullName: string = `Bob Bobbington`;
let sentence: string = `Hello, my name is ${fullName}.`;
```

### Array <a name="type-array"></a>
Two ways to declare arrays:
```typescript
let list1: number[] = [1, 2, 3];
let list2: Array<number> = [1, 2, 3]; // Generic array type
```

### Tuple <a name="type-tuple"></a>
Tuples allow you to express an array with a fixed number of elements whose types are known, but need not be the same.
```typescript
let x: [string, number];
x = ["hello", 10]; // OK
// x = [10, "hello"]; // Error
```

### Enum <a name="type-enum"></a>
A way of giving more friendly names to sets of numeric values.
```typescript
enum Color {Red, Green, Blue}
let c: Color = Color.Green;

enum Status {Pending = 1, Approved, Rejected}
let currentStatus: Status = Status.Approved; // 2
```

### Any <a name="type-any"></a>
For variables whose type is unknown or dynamic. Opts out of type checking.
```typescript
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```
Use `any` sparingly as it defeats the purpose of TypeScript.

### Void <a name="type-void"></a>
The absence of having any type at all. Typically used as the return type of functions that do not return a value.
```typescript
function warnUser(): void {
  console.log("This is my warning message");
}
```

### Null and Undefined <a name="type-null-undefined"></a>
By default, `null` and `undefined` are subtypes of all other types. With `strictNullChecks` enabled in `tsconfig.json`, `null` and `undefined` can only be assigned to `void` or their respective types.
```typescript
let u: undefined = undefined;
let n: null = null;
```

### Never <a name="type-never"></a>
Represents the type of values that never occur. For example, functions that always throw an error or have an infinite loop.
```typescript
function error(message: string): never {
  throw new Error(message);
}

function infiniteLoop(): never {
  while (true) {}
}
```

### Object <a name="type-object"></a>
Represents the non-primitive type, i.e., anything that is not `number`, `string`, `boolean`, `bigint`, `symbol`, `null`, or `undefined`.
```typescript
let obj: object = {};
declare function create(o: object | null): void;
```

### Type Assertions (Casting) <a name="type-assertions"></a>
A way to tell the compiler "trust me, I know what I'm doing." It's like a type cast in other languages but performs no special checking or restructuring of data.
```typescript
let someValue: any = "this is a string";
let strLength1: number = (<string>someValue).length;
let strLength2: number = (someValue as string).length; // Preferred JSX-friendly syntax
```

## 4. Interfaces <a name="interfaces-ts"></a>
Interfaces define contracts for the shape of an object. They are a powerful way of defining what properties and methods an object should have.

### Defining an Interface <a name="defining-interface"></a>
```typescript
interface LabeledValue {
  label: string;
}

function printLabel(labeledObj: LabeledValue) {
  console.log(labeledObj.label);
}

let myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj); // OK, myObj has 'label' property of type string
```

### Optional Properties <a name="optional-properties"></a>
Properties can be marked as optional with a `?`.
```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}
```

### Readonly Properties <a name="readonly-properties"></a>
Properties can be marked as `readonly`. They can only be set when an object is first created.
```typescript
interface Point {
  readonly x: number;
  readonly y: number;
}
let p1: Point = { x: 10, y: 20 };
// p1.x = 5; // Error! x is readonly.
```

### Function Types <a name="function-types-interface"></a>
Interfaces can describe function types.
```typescript
interface SearchFunc {
  (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function(src, sub) {
  let result = src.search(sub);
  return result > -1;
}
```

### Indexable Types <a name="indexable-types-interface"></a>
Interfaces can describe types that we can "index into" like arrays and objects.
```typescript
interface StringArray {
  [index: number]: string;
}
let myArray: StringArray = ["Bob", "Fred"];
let first = myArray[0];
```

### Class Types (Implementing an Interface) <a name="class-types-interface"></a>
Classes can implement interfaces to ensure they adhere to a certain contract.
```typescript
interface ClockInterface {
  currentTime: Date;
  setTime(d: Date): void;
}

class Clock implements ClockInterface {
  currentTime: Date = new Date();
  setTime(d: Date) {
    this.currentTime = d;
  }
  constructor(h: number, m: number) { /* ... */ }
}
```

### Extending Interfaces <a name="extending-interfaces"></a>
Interfaces can extend other interfaces.
```typescript
interface Shape {
  color: string;
}

interface Square extends Shape {
  sideLength: number;
}

let square = {} as Square;
square.color = "blue";
square.sideLength = 10;
```

## 5. Classes <a name="classes-ts"></a>
TypeScript supports ES6 classes and adds features like access modifiers.

### Basic Class Definition <a name="basic-class-definition-ts"></a>
```typescript
class Greeter {
  greeting: string;
  constructor(message: string) {
    this.greeting = message;
  }
  greet() {
    return "Hello, " + this.greeting;
  }
}
let greeter = new Greeter("world");
```

### Inheritance <a name="inheritance-ts"></a>
```typescript
class Animal {
  name: string;
  constructor(theName: string) { this.name = theName; }
  move(distanceInMeters: number = 0) {
    console.log(`${this.name} moved ${distanceInMeters}m.`);
  }
}

class Dog extends Animal {
  constructor(name: string) { super(name); }
  bark() {
    console.log("Woof! Woof!");
  }
  move(distanceInMeters = 5) {
    console.log("Running...");
    super.move(distanceInMeters);
  }
}

const dog = new Dog("Buddy");
dog.bark();
dog.move(10);
```

### Access Modifiers (`public`, `private`, `protected`) <a name="access-modifiers-ts"></a>
*   `public`: (Default) Members are accessible from anywhere.
*   `private`: Members are only accessible within the defining class.
*   `protected`: Members are accessible within the defining class and by instances of derived classes.
```typescript
class Person {
  private secret: string;
  protected age: number;
  public name: string;

  constructor(name: string, age: number, secret: string) {
    this.name = name;
    this.age = age;
    this.secret = secret;
  }
}
```

### Readonly Modifier for Properties <a name="readonly-modifier-class"></a>
Class properties can be marked `readonly`.
```typescript
class Octopus {
  readonly name: string;
  readonly numberOfLegs: number = 8;
  constructor (theName: string) {
    this.name = theName;
  }
}
```

### Static Properties and Methods <a name="static-properties-methods-ts"></a>
Members that belong to the class itself, not to instances of the class.
```typescript
class Grid {
  static origin = {x: 0, y: 0};
  static calculateDistanceFromOrigin(point: {x: number; y: number;}) {
    let xDist = (point.x - Grid.origin.x);
    // ...
  }
}
```

### Abstract Classes <a name="abstract-classes-ts"></a>
Abstract classes are base classes from which other classes may be derived. They may not be instantiated directly. Abstract methods must be implemented in derived classes.
```typescript
abstract class Department {
  constructor(public name: string) {}
  printName(): void { console.log("Department name: " + this.name); }
  abstract printMeeting(): void; // Must be implemented in derived class
}

class AccountingDepartment extends Department {
  constructor() { super("Accounting and Auditing"); }
  printMeeting(): void { console.log("The Accounting Department meets each Monday at 10am."); }
}
```

## 6. Functions in TypeScript <a name="functions-ts"></a>
TypeScript adds type safety to JavaScript functions.

### Typed Parameters and Return Types <a name="typed-parameters-return"></a>
```typescript
function add(x: number, y: number): number {
  return x + y;
}
```

### Optional and Default Parameters <a name="optional-default-parameters-ts"></a>
```typescript
// Optional parameter
function buildName(firstName: string, lastName?: string): string {
  if (lastName) return firstName + " " + lastName;
  else return firstName;
}

// Default parameter
function greetUser(name: string, greeting: string = "Hello"): string {
  return `${greeting}, ${name}!`;
}
```

### Rest Parameters <a name="rest-parameters-ts"></a>
```typescript
function sumAll(...numbers: number[]): number {
  return numbers.reduce((total, num) => total + num, 0);
}
```

### Function Overloads <a name="function-overloads-ts"></a>
Allows defining multiple function signatures for a single function body.
```typescript
function pickCard(x: {suit: string; card: number; }[]): number;
function pickCard(x: number): {suit: string; card: number; };
function pickCard(x: any): any {
  // Implementation
  if (typeof x == "object") { /* ... */ }
  else if (typeof x == "number") { /* ... */ }
}
```

## 7. Enums (Recap and More) <a name="enums-ts-more"></a>
Enums allow a developer to define a set of named constants.

### Numeric Enums <a name="numeric-enums"></a>
By default, enums are number-based, starting at 0.
```typescript
enum Direction { Up, Down, Left, Right }
let dir: Direction = Direction.Left; // 2
```

### String Enums <a name="string-enums"></a>
Enum members can also be initialized with string values.
```typescript
enum LogLevel { Error = "ERROR", Warn = "WARN", Info = "INFO" }
```

### Heterogeneous Enums <a name="heterogeneous-enums"></a>
Enums can mix string and numeric members (generally not recommended).

### Computed and Constant Members <a name="computed-constant-enums"></a>
Enum members can have values that are computed.

## 8. Generics <a name="generics-ts"></a>
Generics allow you to write reusable code that can work over a variety of types rather than a single one.

### What are Generics? (Motivation) <a name="what-are-generics"></a>
Consider a function that returns the first element of an array. Without generics, you might use `any`, losing type information.
```typescript
function identity<T>(arg: T): T {
  return arg;
}
let output1 = identity<string>("myString"); // type of output1 will be 'string'
let output2 = identity(100); // type of output2 will be 'number' (type argument inference)
```

### Generic Functions <a name="generic-functions"></a>
As shown above with `identity`.

### Generic Interfaces <a name="generic-interfaces"></a>
```typescript
interface GenericIdentityFn<T> {
  (arg: T): T;
}
let myIdentity: GenericIdentityFn<number> = identity;
```

### Generic Classes <a name="generic-classes"></a>
```typescript
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}
let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```

### Generic Constraints <a name="generic-constraints"></a>
You can constrain the types that generics can accept.
```typescript
interface Lengthwise {
  length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length); // Now we know it has a .length property
  return arg;
}
// loggingIdentity(3); // Error, number doesn't have a .length property
loggingIdentity({length: 10, value: 3}); // OK
```

## 9. Type Inference <a name="type-inference-ts"></a>
TypeScript can often infer types even if you don't explicitly provide them.
```typescript
let x = 3; // TypeScript infers x is 'number'
// x = "hello"; // Error: Type 'string' is not assignable to type 'number'.
```

## 10. Type Compatibility (Structural Typing) <a name="type-compatibility-ts"></a>
TypeScript uses structural typing (also known as "duck typing"). If two types have the same structure (shape), they are considered compatible, regardless of their explicit declarations.
```typescript
interface Named {
  name: string;
}
class Person {
  name: string;
  constructor(name: string) { this.name = name; }
}
let p: Named;
// OK, because of structural typing
p = new Person('Alice');
```

## 11. Advanced Types (Brief Overview) <a name="advanced-types-overview-ts"></a>

### Union Types (`|`) <a name="union-types"></a>
A value can be one of several types.
```typescript
function padLeft(value: string, padding: string | number) {
  // ...
}
```

### Intersection Types (`&`) <a name="intersection-types"></a>
Combines multiple types into one.
```typescript
interface ErrorHandling { success: boolean; error?: { message: string }; }
interface ArtworksData { artworks: { title: string }[]; }
type ArtworksResponse = ArtworksData & ErrorHandling;
```

### Type Aliases (`type`) <a name="type-aliases"></a>
Create new names for types.
```typescript
type Point = { x: number; y: number; };
type StringOrNumber = string | number;
```

### Literal Types <a name="literal-types"></a>
Allows specifying exact values a string, number, or boolean must have.
```typescript
type Easing = "ease-in" | "ease-out" | "ease-in-out";
let animationSpeed: Easing = "ease-in";
```

## 12. Modules in TypeScript <a name="modules-ts"></a>

### Follows ES6 Module Syntax <a name="ts-es6-modules"></a>
TypeScript uses the ES6 module syntax (`import` and `export`). The compiler (`tsc`) can generate module code for different systems (CommonJS, AMD, ES6, etc.) based on the `module` option in `tsconfig.json`.
(Covered in detail in [Tutorial 11](../11-js-modules/README.md) and [Tutorial 23](../23-js-modules-advanced/README.md))

## 13. Key Takeaways <a name="key-takeaways-typescript"></a>
*   TypeScript adds optional static typing to JavaScript, improving code quality and maintainability.
*   It helps catch errors early in development and provides excellent tooling.
*   Core features include basic types, interfaces (for defining shapes), classes (with access modifiers), enums, and generics (for reusable typed components).
*   `tsconfig.json` is used to configure the TypeScript compiler.
*   TypeScript is a superset of JavaScript, allowing for gradual adoption.
*   Understanding types, interfaces, and generics is key to leveraging TypeScript's power.

---

⬅️ [Back to JavaScript Build Tools & Transpilers](../24-js-build-tools-transpilers/README.md) | ➡️ [Next Section: TypeScript Advanced Types & Concepts](../26-ts-advanced-types/README.md) (Placeholder for future TypeScript series)
