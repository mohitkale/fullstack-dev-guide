# 02. TypeScript Basic Types

After understanding what TypeScript is and how to set it up, the next step is to learn about its basic types. TypeScript extends JavaScript's type system, providing more robust ways to declare and manage data types in your code. Using these types helps in catching errors early and making your code more readable and maintainable.

This tutorial covers the core basic types available in TypeScript.

## Table of Contents
1.  [Introduction to Types](#introduction-to-types)
2.  [Boolean](#boolean-type)
3.  [Number](#number-type)
4.  [String](#string-type)
5.  [Array](#array-type)
    *   [Syntax 1: `type[]`](#array-syntax-1)
    *   [Syntax 2: `Array<type>`](#array-syntax-2)
6.  [Tuple](#tuple-type)
7.  [Enum](#enum-type)
    *   [Numeric Enums](#numeric-enums)
    *   [String Enums](#string-enums)
    *   [Heterogeneous Enums](#heterogeneous-enums)
    *   [Computed and Constant Members](#computed-constant-enums)
8.  [Any](#any-type)
9.  [Unknown](#unknown-type)
10. [Void](#void-type)
11. [Null and Undefined](#null-undefined-types)
12. [Never](#never-type)
13. [Object](#object-type)
14. [Type Assertions](#type-assertions)
    *   [Angle-bracket syntax](#angle-bracket-syntax)
    *   [`as` syntax](#as-syntax)
15. [Key Takeaways](#key-takeaways-basic-types)

## 1. Introduction to Types <a name="introduction-to-types"></a>
TypeScript provides static typing, meaning you can declare the types of variables, function parameters, and return values. If you try to assign a value of a different type, the TypeScript compiler will show an error.

```typescript
let myVariable: string = "This is a string";
// myVariable = 10; // Error: Type 'number' is not assignable to type 'string'.
```

## 2. Boolean <a name="boolean-type"></a>
The most basic datatype is the simple true/false value, which JavaScript and TypeScript call a `boolean` value.

```typescript
let isActive: boolean = true;
let isEnabled: boolean = false;

// isActive = "yes"; // Error: Type 'string' is not assignable to type 'boolean'.
console.log(isActive); // true
```

## 3. Number <a name="number-type"></a>
As in JavaScript, all numbers in TypeScript are floating-point values. TypeScript supports decimal, hexadecimal, binary, and octal literals.

```typescript
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
let big: bigint = 100n; // For very large integers (target ES2020 or newer)

console.log(decimal); // 6
console.log(hex);     // 61453
```

## 4. String <a name="string-type"></a>
TypeScript, like JavaScript, uses the `string` type to refer to textual data. You can use single quotes (`'`), double quotes (`"`), or template strings (backticks `` ` ``) to define strings.

```typescript
let color: string = "blue";
color = 'red';

let fullName: string = `Bob Bobbington`;
let age: number = 37;
let sentence: string = `Hello, my name is ${fullName}.

I'll be ${age + 1} years old next month.`;

console.log(sentence);
```

## 5. Array <a name="array-type"></a>
TypeScript, like JavaScript, allows you to work with arrays of values. You can specify the type of values an array can contain using two syntaxes:

### Syntax 1: `type[]` <a name="array-syntax-1"></a>
This is the most common way to denote an array of a certain type.

```typescript
let list: number[] = [1, 2, 3];
let names: string[] = ["Alice", "Bob", "Charlie"];

// list.push("4"); // Error: Argument of type 'string' is not assignable to parameter of type 'number'.
console.log(list[0]); // 1
```

### Syntax 2: `Array<type>` (Generic array type) <a name="array-syntax-2"></a>
This uses a generic type `Array` with the element type specified in angle brackets.

```typescript
let listGeneric: Array<number> = [1, 2, 3];
let namesGeneric: Array<string> = ["Alice", "Bob", "Charlie"];

console.log(listGeneric.length); // 3
```

## 6. Tuple <a name="tuple-type"></a>
Tuple types allow you to express an array with a fixed number of elements whose types are known, but need not be the same. The order of types matters.

```typescript
// Declare a tuple type
let x: [string, number];

// Initialize it
x = ["hello", 10]; // OK

// Initialize it incorrectly
// x = [10, "hello"]; // Error: Type 'number' is not assignable to type 'string'.

console.log(x[0].substring(1)); // "ello"
console.log(x[1]);             // 10

// Accessing an element outside the set of known indices causes an error (with noImplicitAny or strictNullChecks)
// x[2] = "world"; // Error: Tuple type '[string, number]' of length '2' has no element at '2'.
```
Accessing elements beyond the defined number of elements is an error if `noImplicitAny` is on, or if `strictNullChecks` is on, it will be typed as `undefined`.

## 7. Enum <a name="enum-type"></a>
Enums are a feature added to JavaScript by TypeScript which allows for defining a set of named constants. Using enums can make it easier to document intent, or create a set of distinct cases.

### Numeric Enums <a name="numeric-enums"></a>
By default, enums initialize the first value to `0` and add 1 to each subsequent value.

```typescript
enum Direction {
  Up,    // 0
  Down,  // 1
  Left,  // 2
  Right  // 3
}

let go: Direction = Direction.Up;
console.log(go); // 0 (numeric value)
console.log(Direction[0]); // Up (reverse mapping: number to string)

// You can manually set the values
enum Status {
  Pending = 100,
  Approved, // 101
  Rejected  // 102
}
console.log(Status.Approved); // 101
```

### String Enums <a name="string-enums"></a>
In a string enum, each member has to be constant-initialized with a string literal, or with another string enum member.

```typescript
enum LogLevel {
  Error = "ERROR",
  Warning = "WARN",
  Info = "INFO",
  Debug = "DEBUG"
}

let level: LogLevel = LogLevel.Info;
console.log(level); // "INFO"
// String enums don't have reverse mappings.
// console.log(LogLevel["INFO"]); // undefined (or error depending on tsconfig)
```

### Heterogeneous Enums <a name="heterogeneous-enums"></a>
Enums can contain a mix of string and numeric members. However, this is generally not recommended due to potential confusion.

```typescript
enum Mixed {
  No = 0,
  Yes = "YES"
}
```

### Computed and Constant Members <a name="computed-constant-enums"></a>
Enum members can also have computed values, but if an enum has computed members, uninitialized members that follow it must be initialized.

```typescript
enum FileAccess {
  None,
  Read    = 1 << 1, // 2
  Write   = 1 << 2, // 4
  ReadWrite = Read | Write, // 6 (computed)
  // G = "123".length // Computed
}
console.log(FileAccess.ReadWrite); // 6
```

## 8. Any <a name="any-type"></a>
Sometimes, you might need to describe the type of variables that you do not know when you are writing an application. In these cases, you want to opt-out of type checking and let the values pass through compile-time checks. To do so, you label these values with the `any` type.

```typescript
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean

let looselyTyped: any[] = [1, true, "free"];
// looselyTyped[0] = "now a string"; // OK

// console.log(notSure.ifItExists()); // OK at compile time, but will fail at runtime
// console.log(notSure.toFixed()); // OK at compile time, but will fail if notSure is not a number
```
Using `any` can be useful when migrating JavaScript code, but it defeats the purpose of TypeScript's type safety. It should be used sparingly.

## 9. Unknown <a name="unknown-type"></a>
Like `any`, `unknown` can represent any value. However, TypeScript is stricter with `unknown`. You cannot perform operations on an `unknown` type or assign it to a more specific type without first performing a type check or type assertion.

```typescript
let value: unknown;

value = true;             // OK
value = 42;               // OK
value = "Hello World";    // OK
value = [];               // OK

let value1: unknown = value;
// let value2: boolean = value; // Error: Type 'unknown' is not assignable to type 'boolean'.

// To use an unknown value, you must first narrow its type
if (typeof value === 'string') {
  console.log(value.toUpperCase()); // OK, value is now 'string'
}

// (value as string).toUpperCase(); // OK with type assertion
```
`unknown` is a safer alternative to `any` when you don't know the type of a value.

## 10. Void <a name="void-type"></a>
`void` is a little like the opposite of `any`: the absence of having any type at all. It is commonly used as the return type of functions that do not return a value.

```typescript
function warnUser(): void {
  console.log("This is my warning message");
  // return 1; // Error: Type 'number' is not assignable to type 'void'.
}

let unusable: void = undefined; // You can only assign `null` or `undefined` to void
// unusable = null; // OK if strictNullChecks is false
```

## 11. Null and Undefined <a name="null-undefined-types"></a>
In TypeScript, both `null` and `undefined` have their own types named `null` and `undefined` respectively. By default, `null` and `undefined` are subtypes of all other types. That means you can assign `null` and `undefined` to something like `number`.

However, when using the `strictNullChecks` compiler option (which is highly recommended and part of `strict` mode), `null` and `undefined` are only assignable to `any`, `unknown`, and their respective types. This helps avoid many common errors.

```typescript
let u: undefined = undefined;
let n: null = null;

// With strictNullChecks: false (default without 'strict' in tsconfig)
// let num: number = null; // OK
// let str: string = undefined; // OK

// With strictNullChecks: true
// let numStrict: number = null; // Error: Type 'null' is not assignable to type 'number'.
// To allow null or undefined, use a union type:
let nullableNumber: number | null = null;
nulllableNumber = 10;
```

## 12. Never <a name="never-type"></a>
The `never` type represents the type of values that never occur. For instance, `never` is the return type for a function expression or an arrow function expression that always throws an exception or one that never returns.
Variables also acquire the type `never` when narrowed by any type guards that can never be true.

```typescript
// Function returning never must not have a reachable end point
function error(message: string): never {
  throw new Error(message);
}

// Inferred return type is never
function fail() {
  return error("Something failed");
}

// Function returning never must not have a reachable end point
function infiniteLoop(): never {
  while (true) {}
}
```

## 13. Object <a name="object-type"></a>
`object` is a type that represents the non-primitive type, i.e. anything that is not `number`, `string`, `boolean`, `bigint`, `symbol`, `null`, or `undefined`.

```typescript
let obj: object;
obj = { name: "Alice" }; // OK
obj = [1, 2, 3];        // OK (arrays are objects)
obj = () => {};         // OK (functions are objects)

// obj = 42; // Error: Type 'number' is not assignable to type 'object'.
// obj = "string"; // Error
// obj = null; // Error (if strictNullChecks is true)
// obj = undefined; // Error (if strictNullChecks is true)

// For more specific object shapes, use interface or type aliases (covered later)
declare function create(o: object | null): void;
create({ prop: 0 }); // OK
create(null); // OK
// create(42); // Error
// create("string"); // Error
```
It's generally better to use more specific types like `{ [key: string]: any }` or define an `interface` or `type` for objects with known properties.

## 14. Type Assertions <a name="type-assertions"></a>
Sometimes you’ll end up in a situation where you know more about a value’s type than TypeScript does. In such situations, you can use a type assertion to tell the compiler what the type should be. This is like a type cast in other languages, but it performs no special checking or restructuring of data. It has no runtime impact and is purely used by the compiler.

There are two syntaxes:

### Angle-bracket syntax <a name="angle-bracket-syntax"></a>
```typescript
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;
```

### `as` syntax <a name="as-syntax"></a>
```typescript
let anotherValue: any = "this is also a string";
let anotherStrLength: number = (anotherValue as string).length;
```
The `as` syntax is preferred when using TypeScript with JSX, as angle brackets can conflict with JSX syntax.

Type assertions should be used with caution, as you are telling the compiler to trust your knowledge of the type, which can lead to runtime errors if incorrect.

## 15. Key Takeaways <a name="key-takeaways-basic-types"></a>
*   TypeScript provides a rich set of basic types: `boolean`, `number`, `string`, `array`, `tuple`, and `enum`.
*   Special types like `any`, `unknown`, `void`, `null`, `undefined`, and `never` handle various scenarios.
*   `unknown` is a safer alternative to `any`.
*   `strictNullChecks` significantly improves code safety by making `null` and `undefined` distinct types.
*   Type assertions allow you to override TypeScript's inferred types when necessary, but should be used carefully.

Understanding these basic types is fundamental to writing effective TypeScript code.

---

⬅️ [Back to Introduction to TypeScript](../01-ts-introduction/README.md) | ➡️ [Next: Interfaces](../03-ts-interfaces/README.md)
