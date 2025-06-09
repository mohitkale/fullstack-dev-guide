# 07. TypeScript Enums and Literal Types

TypeScript provides powerful features like Enums and Literal Types to create more expressive and precise type definitions. Enums allow you to define a set of named constants, while Literal Types let you specify the exact values a variable can hold.

This tutorial explores these concepts in detail.

## Table of Contents
1.  [Enums Revisited](#enums-revisited)
    *   [Numeric Enums](#numeric-enums-deep-dive)
        *   [Auto-incrementing](#auto-incrementing)
        *   [Reverse Mappings](#reverse-mappings)
    *   [String Enums](#string-enums-deep-dive)
    *   [Heterogeneous Enums](#heterogeneous-enums-recap)
    *   [Computed and Constant Members](#computed-constant-members-recap)
    *   [Enums at Runtime](#enums-at-runtime)
    *   [Const Enums](#const-enums)
    *   [Ambient Enums](#ambient-enums)
    *   [When to Use Enums](#when-to-use-enums)
2.  [Literal Types](#literal-types)
    *   [String Literal Types](#string-literal-types)
    *   [Numeric Literal Types](#numeric-literal-types)
    *   [Boolean Literal Types](#boolean-literal-types)
3.  [Using Literal Types with Union Types](#literal-union-types)
4.  [Type Aliases with Literal Types](#type-aliases-literal)
5.  [Discriminated Unions with Literal Types](#discriminated-unions-literal)
6.  [Key Takeaways](#key-takeaways-enums-literals)

## 1. Enums Revisited <a name="enums-revisited"></a>
Enums (enumerations) allow us to define a collection of related values that can be referred to by a common name. They are useful for representing a set of distinct cases or states.

### Numeric Enums <a name="numeric-enums-deep-dive"></a>
Numeric enums store string values as numbers. By default, the first enumerator is initialized to `0`, and each subsequent enumerator is incremented by 1.

#### Auto-incrementing <a name="auto-incrementing"></a>
```typescript
enum Direction {
  Up,    // 0
  Down,  // 1
  Left,  // 2
  Right  // 3
}

let dir: Direction = Direction.Left;
console.log(dir); // 2

// You can also initialize the first member:
enum ResponseStatus {
  Success = 200,
  NotFound, // 201
  Error     // 202
}
console.log(ResponseStatus.NotFound); // 201
```

#### Reverse Mappings <a name="reverse-mappings"></a>
Numeric enums have a reverse mapping from enum values to enum names. This means you can access the string name of an enum member given its numeric value.

```typescript
enum Weekend {
  Saturday, // 0
  Sunday    // 1
}

console.log(Weekend.Saturday);   // 0
console.log(Weekend[0]);         // "Saturday"
console.log(Weekend[Weekend.Saturday]); // "Saturday"
```
This reverse mapping is not generated for string enums.

### String Enums <a name="string-enums-deep-dive"></a>
In a string enum, each member must be constant-initialized with a string literal or another string enum member. String enums are often preferred for their readability and debugging benefits, as their values are human-readable strings.

```typescript
enum LogLevel {
  ERROR = "ERROR_LEVEL",
  WARN = "WARN_LEVEL",
  INFO = "INFO_LEVEL",
  DEBUG = "DEBUG_LEVEL"
}

let logLevel: LogLevel = LogLevel.INFO;
console.log(logLevel); // "INFO_LEVEL"
// console.log(LogLevel["INFO_LEVEL"]); // No reverse mapping for string enums
```
String enums do not have auto-incrementing behavior or reverse mappings.

### Heterogeneous Enums <a name="heterogeneous-enums-recap"></a>
Enums can technically contain a mix of string and numeric members, but this is generally discouraged as it can lead to confusion.

```typescript
enum MixedBag {
  Num = 0,
  Str = "STRING_VALUE"
}
```

### Computed and Constant Members <a name="computed-constant-members-recap"></a>
Enum members can have values that are computed at runtime. However, if an enum contains computed members, any uninitialized members that follow it must be initialized.

```typescript
function getComputedValue() {
  return 5;
}

enum FileAccess {
  None,
  Read    = 1 << 1,       // Constant member (2)
  Write   = 1 << 2,       // Constant member (4)
  ReadWrite = Read | Write, // Constant member (6)
  Computed = getComputedValue() // Computed member
  // AfterComputed // Error! Enum member must have initializer if preceded by computed member.
}
console.log(FileAccess.ReadWrite); // 6
console.log(FileAccess.Computed);  // 5
```

### Enums at Runtime <a name="enums-at-runtime"></a>
Enums are real objects that exist at runtime. When TypeScript compiles an enum, it generates JavaScript code that defines an object with the specified members.

```typescript
// Example: enum Direction { Up, Down }
// Compiled JavaScript (simplified):
/*
var Direction;
(function (Direction) {
    Direction[Direction["Up"] = 0] = "Up";
    Direction[Direction["Down"] = 1] = "Down";
})(Direction || (Direction = {}));
*/
```
This runtime object allows for features like reverse mappings.

### Const Enums <a name="const-enums"></a>
For performance, you can use `const enum`. Const enums are completely removed during compilation, and their usages are inlined. This means they don't generate any JavaScript code for the enum object itself.

```typescript
const enum Colors {
  Red,  // 0
  Green, // 1
  Blue  // 2
}

let myColor: Colors = Colors.Green;
console.log(myColor); // In compiled JS, this might become console.log(1);

// console.log(Colors[1]); // Error: A const enum member can only be accessed using a string literal.
                         // Const enums don't have reverse mappings at runtime because they don't exist.
```
Const enums are useful when you need the type safety of enums but want to avoid the overhead of a runtime object, especially in performance-critical code.

### Ambient Enums <a name="ambient-enums"></a>
Ambient enums (declared with `declare enum`) are used to describe the shape of already existing enum types, typically from JavaScript libraries or runtime environments.

```typescript
declare enum ExistingEnum {
  A = 1,
  B,
  C = 2
}
// let e: ExistingEnum = ExistingEnum.A; // Assumes ExistingEnum is defined elsewhere
```

### When to Use Enums <a name="when-to-use-enums"></a>
Enums are great for representing a fixed set of related constants, such as:
*   States (e.g., `OrderStatus.Pending`, `OrderStatus.Shipped`)
*   Options (e.g., `LogLevel.Error`, `LogLevel.Info`)
*   Modes (e.g., `EditorMode.View`, `EditorMode.Edit`)

However, for simpler cases, string literal union types (covered next) can sometimes be a more lightweight alternative.

## 2. Literal Types <a name="literal-types"></a>
Literal types allow you to specify the exact value a string, number, or boolean must have. They are incredibly useful for creating more precise and self-documenting types.

### String Literal Types <a name="string-literal-types"></a>
You can define a type that only accepts a specific string.

```typescript
let httpMethod: "GET" | "POST" | "PUT" | "DELETE";

httpMethod = "GET"; // OK
httpMethod = "POST"; // OK
// httpMethod = "PATCH"; // Error: Type '"PATCH"' is not assignable to type '"GET" | "POST" | "PUT" | "DELETE"'.

function setAlignment(align: "left" | "center" | "right"): void {
  console.log(`Alignment set to: ${align}`);
}

setAlignment("center"); // OK
// setAlignment("justify"); // Error
```

### Numeric Literal Types <a name="numeric-literal-types"></a>
Similarly, you can define types that accept only specific numbers.

```typescript
let dieRoll: 1 | 2 | 3 | 4 | 5 | 6;

dieRoll = 1; // OK
// dieRoll = 7; // Error: Type '7' is not assignable to type '1 | 2 | 3 | 4 | 5 | 6'.

function setDiceValue(value: 1 | 2 | 3 | 4 | 5 | 6): void {
  console.log(`Dice value: ${value}`);
}
```

### Boolean Literal Types <a name="boolean-literal-types"></a>
You can also have literal types for booleans, though this is less common as `boolean` itself is usually sufficient. However, it can be useful in specific scenarios, like discriminating unions.

```typescript
interface Config {
  isProduction: true; // This property must be true
  // ... other properties
}

interface DevConfig {
  isProduction: false;
  debugMode: boolean;
}

let prodSettings: Config = { isProduction: true };
// let devSettings: Config = { isProduction: false }; // Error
```

## 3. Using Literal Types with Union Types <a name="literal-union-types"></a>
The real power of literal types shines when combined with union types. This allows you to define a type that can be one of several specific string, number, or boolean values.

```typescript
type CardinalDirection = "North" | "East" | "South" | "West";

function move(direction: CardinalDirection, distance: number): void {
  console.log(`Moving ${distance} units ${direction}.`);
}

move("North", 10);
// move("Northeast", 5); // Error: Argument of type '"Northeast"' is not assignable to parameter of type 'CardinalDirection'.


type AnimationTiming = "ease-in" | "ease-out" | "ease-in-out" | number;

function animate(timing: AnimationTiming): void {
  if (typeof timing === "string") {
    console.log(`Animating with preset: ${timing}`);
  } else {
    console.log(`Animating with duration: ${timing}ms`);
  }
}

animate("ease-in");
animate(500);
// animate("linear"); // Error
```
This provides excellent type safety and autocompletion in editors.

## 4. Type Aliases with Literal Types <a name="type-aliases-literal"></a>
It's common practice to use type aliases to give meaningful names to literal union types, as seen with `CardinalDirection` and `AnimationTiming` above. This improves code readability and reusability.

```typescript
type ButtonSize = "small" | "medium" | "large";
type ButtonVariant = "primary" | "secondary" | "danger";

interface ButtonProps {
  size: ButtonSize;
  variant: ButtonVariant;
  onClick: () => void;
}

function createButton(props: ButtonProps) {
  // ... create button logic
  console.log(`Button created: Size - ${props.size}, Variant - ${props.variant}`);
}

createButton({ size: "medium", variant: "primary", onClick: () => console.log("Clicked!") });
```

## 5. Discriminated Unions with Literal Types <a name="discriminated-unions-literal"></a>
Literal types are crucial for creating discriminated unions (also known as tagged unions or algebraic data types). This pattern involves having a common literal property (the discriminant) in several types, allowing TypeScript to narrow down the type within a conditional block.

```typescript
interface Square {
  kind: "square"; // Discriminant property
  size: number;
}

interface Rectangle {
  kind: "rectangle"; // Discriminant property
  width: number;
  height: number;
}

interface Circle {
  kind: "circle"; // Discriminant property
  radius: number;
}

type Shape = Square | Rectangle | Circle;

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "square":
      return shape.size * shape.size; // shape is Square here
    case "rectangle":
      return shape.width * shape.height; // shape is Rectangle here
    case "circle":
      return Math.PI * shape.radius ** 2; // shape is Circle here
    default:
      // Optional: Exhaustiveness check
      const _exhaustiveCheck: never = shape;
      return _exhaustiveCheck;
  }
}

console.log(getArea({ kind: "square", size: 10 })); // 100
console.log(getArea({ kind: "circle", radius: 5 })); // approx 78.53
```
Discriminated unions are a very powerful pattern for handling different but related types in a type-safe way.

## 6. Key Takeaways <a name="key-takeaways-enums-literals"></a>
*   **Enums** define a set of named constants, which can be numeric or string-based.
    *   Numeric enums have auto-incrementing and reverse mappings.
    *   String enums offer better readability for their values.
    *   `const enum` are inlined for performance, losing runtime representation.
*   **Literal Types** restrict a variable to specific string, number, or boolean values.
*   Combining literal types with **union types** creates powerful and precise type definitions.
*   **Type aliases** make literal union types more readable and reusable.
*   Literal types are essential for building **discriminated unions**, a robust pattern for type-safe state management and object handling.

Understanding and using enums and literal types effectively can significantly improve the clarity, safety, and maintainability of your TypeScript code.

---

⬅️ [Back to Generics](../06-ts-generics/README.md) | ➡️ [Next: Advanced Types (Intersection, Union, Type Guards, etc.)](../08-ts-advanced-types/README.md)
