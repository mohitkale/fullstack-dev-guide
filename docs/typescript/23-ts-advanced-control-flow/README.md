# 23. TypeScript Advanced Control Flow Analysis

TypeScript's static type system is significantly enhanced by its sophisticated Control Flow Analysis (CFA). CFA allows the TypeScript compiler to understand how the types of variables change as code executes. This means TypeScript can often infer more precise types within specific blocks of code (like `if` statements, loops, or after certain operations) than the initially declared types.

This tutorial explores the mechanisms behind CFA, including type narrowing, type guards, assertion functions, and how they contribute to more robust and type-safe code.

## Table of Contents
1.  [What is Control Flow Analysis?](#what-is-cfa)
2.  [Type Narrowing](#type-narrowing)
    *   `typeof` Type Guards
    *   `instanceof` Type Guards
    *   Equality Narrowing (e.g., `===`, `!==`, `==`, `!=`)
    *   Truthiness Narrowing (e.g., `if (variable)`)
    *   `in` Operator Narrowing
    *   Literal Type Narrowing
3.  [User-Defined Type Guards](#user-defined-type-guards)
    *   Using `is` Type Predicate (e.g., `param is Type`)
    *   Example: Differentiating Shapes
4.  [Assertion Functions](#assertion-functions)
    *   Syntax: `asserts condition`
    *   Syntax: `asserts argument is Type`
    *   Use Cases: Ensuring conditions or types post-assertion
    *   Difference from `is` Type Predicates
5.  [Discriminated Unions (Recap and CFA)](#discriminated-unions-cfa)
    *   How CFA uses the discriminant property
    *   Exhaustiveness Checking with `never`
6.  [Control Flow Analysis of `this`](#cfa-this)
    *   `this` in methods and functions
    *   `this` type guards (`this is Type`)
7.  [Assignments and CFA](#assignments-cfa)
    *   How assignments affect inferred types within a scope
    *   Re-narrowing after assignments
8.  [Analysis of Unreachable Code](#unreachable-code-analysis)
    *   How `never` type plays a role
    *   Warnings for unreachable code
9.  [Limitations of CFA](#limitations-cfa)
    *   Closures and captured variables
    *   Complex aliasing
    *   Side effects in getters or other functions
10. [Interactions with `strictNullChecks`](#interactions-strictnullchecks)
11. [Key Takeaways](#key-takeaways-cfa)

## 1. What is Control Flow Analysis? <a name="what-is-cfa"></a>
Control Flow Analysis is the process by which the TypeScript compiler analyzes the sequence of statements and expressions in your code to determine the possible types of variables at different points. Based on checks (like `typeof x === "string"`) or assignments, TypeScript can narrow down a variable's type from a broader one (e.g., `string | number`) to a more specific one (e.g., `string`) within a certain code block.

This allows for more accurate type checking and enables features like autocompletion for specific types within those blocks.

## 2. Type Narrowing <a name="type-narrowing"></a>
Type narrowing is the core outcome of CFA. TypeScript uses various constructs to narrow types:

### `typeof` Type Guards
```typescript
function processInput(input: string | number) {
  if (typeof input === "string") {
    // Here, input is known to be a string
    console.log(input.toUpperCase());
  } else {
    // Here, input is known to be a number
    console.log(input.toFixed(2));
  }
}
```
`typeof` can check for `"string"`, `"number"`, `"bigint"`, `"boolean"`, `"symbol"`, `"undefined"`, `"object"`, and `"function"`.

### `instanceof` Type Guards
Used for narrowing types of objects based on their constructor function (class).
```typescript
class Animal { move() { console.log("Moving..."); } }
class Dog extends Animal { bark() { console.log("Woof!"); } }
class Cat extends Animal { meow() { console.log("Meow!"); } }

function makeSound(animal: Animal) {
  animal.move();
  if (animal instanceof Dog) {
    // animal is known to be Dog here
    animal.bark();
  } else if (animal instanceof Cat) {
    // animal is known to be Cat here
    animal.meow();
  }
}

makeSound(new Dog());
makeSound(new Cat());
```

### Equality Narrowing
Using `===`, `!==`, `==`, `!=` with literal values or unique symbols can narrow types.
```typescript
_type Status = "success" | "error";

function handleStatus(status: Status, message: string | null) {
  if (status === "success") {
    // status is "success"
    console.log("Operation successful!");
  } else {
    // status is "error"
    if (message !== null) {
      // message is string here
      console.error(`Error: ${message.toUpperCase()}`);
    }
  }
}
```

### Truthiness Narrowing
Checking if a value is "truthy" or "falsy" can narrow types, especially with `strictNullChecks`.
```typescript
function printLength(value?: string | null) {
  if (value) {
    // value is string here (not null, not undefined, not empty string)
    console.log(value.length);
  } else {
    // value is string | undefined | null | "" (empty string)
    console.log("No value or empty value provided.");
  }
}
```

### `in` Operator Narrowing
Checks if an object has a property with a specific name.
```typescript
interface Admin {
  isAdmin: true;
  manageUsers: () => void;
}

interface RegularUser {
  isUser: true;
  viewContent: () => void;
}

_type User = Admin | RegularUser;

function performAction(user: User) {
  if ("manageUsers" in user) {
    // user is Admin here
    user.manageUsers();
  } else {
    // user is RegularUser here
    user.viewContent();
  }
}
```

### Literal Type Narrowing
When a variable is assigned a literal type, and later compared, it can narrow other variables.
```typescript
_type ShapeType = "circle" | "square";

function getShape(type: ShapeType): { kind: "circle"; radius: number } | { kind: "square"; sideLength: number } {
  if (type === "circle") {
    return { kind: "circle", radius: 10 };
  }
  return { kind: "square", sideLength: 5 };
}

const myShapeType: ShapeType = "circle";
const shape = getShape(myShapeType);

if (shape.kind === "circle") {
  // shape is { kind: "circle"; radius: number } here
  console.log(shape.radius);
}
```

## 3. User-Defined Type Guards <a name="user-defined-type-guards"></a>
Sometimes, built-in guards aren't enough. You can create your own type guards using a special function signature that returns a *type predicate*: `parameterName is Type`.

```typescript
interface Fish {
  swim: () => void;
  name: string;
}

interface Bird {
  fly: () => void;
  name: string;
}

// User-defined type guard
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}

function movePet(pet: Fish | Bird) {
  if (isFish(pet)) {
    // pet is Fish here
    pet.swim();
  } else {
    // pet is Bird here
    pet.fly();
  }
}

const nemo: Fish = { swim: () => console.log("Nemo is swimming"), name: "Nemo" };
const tweety: Bird = { fly: () => console.log("Tweety is flying"), name: "Tweety" };

movePet(nemo);
movePet(tweety);
```
When `isFish` returns `true`, TypeScript narrows `pet` to `Fish` in that scope.

## 4. Assertion Functions <a name="assertion-functions"></a>
Assertion functions are functions that throw an error if a certain condition is not met. TypeScript allows you to declare that a function is an assertion function, which influences CFA.

There are two forms:
1.  `asserts condition`: Tells TypeScript that if the function returns, the `condition` must be true.
2.  `asserts argumentName is Type`: Tells TypeScript that if the function returns, `argumentName` has been narrowed to `Type`.

```typescript
function assert(condition: any, message: string): asserts condition {
  if (!condition) {
    throw new Error(message);
  }
}

function assertIsString(value: any, name: string = "value"): asserts value is string {
  if (typeof value !== "string") {
    throw new Error(`${name} must be a string, got ${typeof value}`);
  }
}

function processValue(x: string | number) {
  // assert(typeof x === "string", "x must be a string");
  // After this line, TypeScript knows x is string IF assert doesn't throw.
  // However, for type narrowing, 'asserts value is Type' is more direct.

  assertIsString(x, "x");
  // Now, TypeScript knows x is a string.
  console.log(x.toUpperCase());
}

processValue("hello");
// processValue(123); // Throws error: x must be a string, got number
```
Assertion functions are useful for ensuring invariants in your code and informing TypeScript's CFA about these invariants.

## 5. Discriminated Unions (Recap and CFA) <a name="discriminated-unions-cfa"></a>
Discriminated unions are a powerful pattern where objects in a union share a common literal property (the *discriminant*). CFA excels with this pattern.

```typescript
interface Circle {
  kind: "circle";
  radius: number;
}

interface Square {
  kind: "square";
  sideLength: number;
}

interface Triangle {
  kind: "triangle";
  base: number;
  height: number;
}

_type Shape = Circle | Square | Triangle;

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      // shape is Circle here
      return Math.PI * shape.radius ** 2;
    case "square":
      // shape is Square here
      return shape.sideLength ** 2;
    case "triangle":
      // shape is Triangle here
      return 0.5 * shape.base * shape.height;
    default:
      // Exhaustiveness checking: if a new shape is added to the Shape union
      // and not handled here, 'exhaustiveCheck' will have a type other than 'never',
      // leading to a compile-time error.
      const exhaustiveCheck: never = shape;
      return exhaustiveCheck;
  }
}
```
CFA narrows `shape` within each `case` block based on the `kind` property.

## 6. Control Flow Analysis of `this` <a name="cfa-this"></a>
TypeScript also performs CFA on the `this` keyword in classes and functions.

```typescript
class FileSystemObject {
  isFile: boolean;
  isDirectory: boolean;
  path: string;

  constructor(path: string) {
    // Placeholder logic
    this.path = path;
    this.isFile = path.includes(".");
    this.isDirectory = !this.isFile;
  }

  isFileType(): this is { isFile: true; isDirectory: false } {
    return this.isFile;
  }

  readFile() {
    if (this.isFileType()) {
      // 'this' is narrowed to { isFile: true; isDirectory: false } & FileSystemObject
      console.log(`Reading file: ${this.path}`);
    }
  }
}
```
Here, `this is { isFile: true; isDirectory: false }` acts as a type guard for `this`.

## 7. Assignments and CFA <a name="assignments-cfa"></a>
Assignments to variables can change their inferred types within a scope. CFA tracks these changes.

```typescript
let x: string | number | boolean = "hello";

if (typeof x === "string") {
  console.log(x.toUpperCase()); // x is string
}

x = 10;
// Now x is number
console.log(x.toFixed(2));

// If x were assigned inside a condition that doesn't always run,
// its type outside might revert or remain a union.
```

## 8. Analysis of Unreachable Code <a name="unreachable-code-analysis"></a>
TypeScript can detect unreachable code based on CFA, often involving the `never` type.

```typescript
function infiniteLoop(): never {
  while (true) {}
}

function processOrThrow(input: string | number): string {
  if (typeof input === "string") {
    return input.toUpperCase();
  }
  // If input is not string, it must be number here.
  // If we call a function that returns 'never':
  return infiniteLoop(); // Or throw new Error("Must be string");
  // console.log("This is unreachable"); // TypeScript will flag this
}
```

## 9. Limitations of CFA <a name="limitations-cfa"></a>
While powerful, CFA has limitations:
*   **Closures:** CFA doesn't always track type changes of variables captured in closures perfectly, especially if the closure is called later.
    ```typescript
    let val: string | number = "abc";
    function later() {
      // By the time this runs, val might have changed.
      // TypeScript might not narrow 'val' inside 'later' as aggressively.
      // console.log(val.toUpperCase()); // Potential error if val is number
    }
    if (typeof val === "string") { /* ... */ }
    val = 123;
    later(); // val is number here
    ```
*   **Complex Aliasing:** If a variable is aliased multiple times, CFA might lose track.
*   **Side Effects:** CFA assumes that type-guarding expressions don't have side effects that could change the type of the variable being checked in an unexpected way.
*   **Order of Evaluation:** CFA relies on the standard order of evaluation. Code that relies on unusual evaluation orders (e.g., through getters with side effects) can confuse CFA.

## 10. Interactions with `strictNullChecks` <a name="interactions-strictnullchecks"></a>
CFA is particularly important when `strictNullChecks` is enabled. Truthiness checks, equality checks against `null` or `undefined`, and user-defined type guards become essential for working with potentially nullable types.

```typescript
function greet(name: string | null) {
  if (name !== null) {
    // name is string here
    console.log(`Hello, ${name.toUpperCase()}!`);
  } else {
    console.log("Hello, guest!");
  }
}
```

## 11. Key Takeaways <a name="key-takeaways-cfa"></a>
*   Control Flow Analysis is a cornerstone of TypeScript's type system, enabling more precise type inference within code blocks.
*   **Type Narrowing** occurs through various constructs: `typeof`, `instanceof`, equality checks, truthiness, `in` operator.
*   **User-Defined Type Guards** (`param is Type`) allow custom narrowing logic.
*   **Assertion Functions** (`asserts condition`, `asserts arg is Type`) inform CFA about invariants.
*   **Discriminated Unions** work seamlessly with CFA for robust state management and exhaustiveness checking.
*   CFA also applies to `this` and tracks changes due to assignments.
*   Understanding CFA helps write more type-safe, readable, and maintainable TypeScript code, especially with `strictNullChecks`.

By leveraging TypeScript's CFA, you can write code that is not only safer but also more expressive, as the compiler understands the context-dependent types of your variables.

---

⬅️ [Back to TypeScript Utility Types (Deep Dive)](../22-ts-utility-types-deep-dive/README.md) | ➡️ [Next: TypeScript Symbols and Unique Symbols](../24-ts-symbols-unique-symbols/README.md) *(Placeholder)*
