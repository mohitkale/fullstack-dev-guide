# 26. TypeScript Advanced Types & Concepts

Building on the fundamentals of TypeScript, this tutorial delves into more advanced features of its powerful type system. These features enable developers to write highly expressive, flexible, and robust type definitions, which are crucial for complex applications and libraries.

## Table of Contents
1.  [Recap: Core TypeScript Concepts](#recap-core-ts)
2.  [Intersection Types (`&`) Revisited](#intersection-types-revisited)
3.  [Union Types (`|`) Revisited](#union-types-revisited)
    *   [Discriminated Unions (Tagged Unions)](#discriminated-unions)
4.  [Type Guards and Narrowing](#type-guards-narrowing)
    *   [`typeof` Type Guards](#typeof-guards)
    *   [`instanceof` Type Guards](#instanceof-guards)
    *   [`in` Operator Type Guard](#in-operator-guard)
    *   [Equality Narrowing](#equality-narrowing)
    *   [User-Defined Type Guards (Type Predicates)](#user-defined-type-guards)
5.  [Nullable Types and `strictNullChecks`](#nullable-types)
    *   [Optional Chaining (`?.`) Recap](#optional-chaining-recap)
    *   [Nullish Coalescing (`??`) Recap](#nullish-coalescing-recap)
    *   [Non-null Assertion Operator (`!`)](#non-null-assertion)
6.  [Type Aliases (`type`) Deep Dive](#type-aliases-deep-dive)
7.  [Literal Types (String, Number, Boolean)](#literal-types-revisited)
8.  [Index Types](#index-types)
    *   [`keyof` Type Operator](#keyof-operator)
    *   [Indexed Access Types (Lookup Types) `T[K]`](#indexed-access-types)
9.  [Mapped Types](#mapped-types)
    *   [Basic Mapped Types](#basic-mapped-types)
    *   [Mapping Modifiers (`readonly`, `?`)](#mapping-modifiers)
    *   [Key Remapping via `as`](#key-remapping-as)
10. [Conditional Types](#conditional-types)
    *   [Syntax: `T extends U ? X : Y`](#conditional-type-syntax)
    *   [Distributive Conditional Types](#distributive-conditional-types)
    *   [Inferring Types with `infer`](#infer-keyword)
11. [Built-in Utility Types](#utility-types)
    *   [`Partial<T>`](#partial-t)
    *   [`Required<T>`](#required-t)
    *   [`Readonly<T>`](#readonly-t)
    *   [`Record<K, T>`](#record-k-t)
    *   [`Pick<T, K>`](#pick-t-k)
    *   [`Omit<T, K>`](#omit-t-k)
    *   [`Exclude<T, U>`](#exclude-t-u)
    *   [`Extract<T, U>`](#extract-t-u)
    *   [`NonNullable<T>`](#nonnullable-t)
    *   [`Parameters<T>`](#parameters-t)
    *   [`ConstructorParameters<T>`](#constructorparameters-t)
    *   [`ReturnType<T>`](#returntype-t)
    *   [`InstanceType<T>`](#instancetype-t)
    *   [`ThisParameterType<T>`](#thisparametertype-t)
    *   [`OmitThisParameter<T>`](#omitthisparameter-t)
    *   [`ThisType<T>`](#thistype-t)
    *   [String Manipulation Types (`Uppercase`, `Lowercase`, `Capitalize`, `Uncapitalize`)](#string-manipulation-types)
12. [Template Literal Types](#template-literal-types)
13. [Symbols and Unique Symbols](#symbols-unique-symbols-ts)
14. [Nominal Typing (Emulation)](#nominal-typing-emulation)
    *   [Branding / Tagging](#branding-tagging)
15. [Decorators (Brief Overview - Experimental)](#decorators-ts)
16. [Best Practices for Advanced Types](#best-practices-advanced-ts)
17. [Key Takeaways](#key-takeaways-advanced-ts)

## 1. Recap: Core TypeScript Concepts <a name="recap-core-ts"></a>
Before diving into advanced topics, ensure you have a solid understanding of:
*   Basic types (string, number, boolean, array, tuple, enum, etc.)
*   Interfaces (defining object shapes, function types, class contracts)
*   Classes (inheritance, access modifiers, abstract classes)
*   Functions (typed parameters, return types, overloads)
*   Generics (reusable typed components, constraints)
*   Modules (ES6 import/export syntax)

(Refer to [Tutorial 25: Introduction to TypeScript](../25-js-intro-to-typescript/README.md) for a refresher.)

## 2. Intersection Types (`&`) Revisited <a name="intersection-types-revisited"></a>
Intersection types combine multiple types into one. An object of an intersection type will have all members of all the types being combined.

```typescript
interface Colorful {
  color: string;
}
interface CircleShape {
  radius: number;
}

type ColorfulCircle = Colorful & CircleShape;

const myCircle: ColorfulCircle = {
  color: "red",
  radius: 10
};
```
Useful for mixins or combining properties from different sources.

## 3. Union Types (`|`) Revisited <a name="union-types-revisited"></a>
Union types allow a value to be one of several types. TypeScript only allows operations valid for *every* member of the union, unless narrowed.

```typescript
function printId(id: number | string) {
  // console.log(id.toUpperCase()); // Error: 'toUpperCase' does not exist on type 'number'
  if (typeof id === "string") {
    console.log(id.toUpperCase()); // OK
  } else {
    console.log(id); // OK
  }
}
```

### Discriminated Unions (Tagged Unions) <a name="discriminated-unions"></a>
A common pattern for union types where you use a common, literal property (the discriminant or tag) to differentiate between members of the union.

```typescript
interface Square {
  kind: "square"; // Discriminant
  size: number;
}

interface Rectangle {
  kind: "rectangle"; // Discriminant
  width: number;
  height: number;
}

interface Circle {
  kind: "circle"; // Discriminant
  radius: number;
}

type Shape = Square | Rectangle | Circle;

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "square":
      return shape.size * shape.size;
    case "rectangle":
      return shape.width * shape.height;
    case "circle":
      return Math.PI * shape.radius ** 2;
    default:
      // Exhaustiveness checking with 'never'
      const _exhaustiveCheck: never = shape;
      return _exhaustiveCheck;
  }
}
```
Discriminated unions make type narrowing (e.g., in `switch` statements) very effective and type-safe.

## 4. Type Guards and Narrowing <a name="type-guards-narrowing"></a>
Type guards are expressions that perform a runtime check that guarantees the type in some scope.

### `typeof` Type Guards <a name="typeof-guards"></a>
For primitive types like `string`, `number`, `boolean`, `symbol`, `bigint`, `undefined`, `function`.
```typescript
function processValue(value: string | number) {
  if (typeof value === 'string') {
    return value.toUpperCase(); // value is string here
  } else {
    return value.toFixed(2); // value is number here
  }
}
```

### `instanceof` Type Guards <a name="instanceof-guards"></a>
For checking if an object is an instance of a particular class or constructor function.
```typescript
class Fish { swim() { console.log('swimming'); } }
class Bird { fly() { console.log('flying'); } }

function move(pet: Fish | Bird) {
  if (pet instanceof Fish) {
    pet.swim(); // pet is Fish here
  } else {
    pet.fly(); // pet is Bird here
  }
}
```

### `in` Operator Type Guard <a name="in-operator-guard"></a>
Checks if an object has a property with a specific name.
```typescript
interface Admin {
  name: string;
  privileges: string[];
}

interface Employee {
  name: string;
  startDate: Date;
}

type UnknownEmployee = Admin | Employee;

function printEmployeeInformation(emp: UnknownEmployee) {
  console.log("Name: " + emp.name);
  if ("privileges" in emp) {
    console.log("Privileges: " + emp.privileges); // emp is Admin here
  }
  if ("startDate" in emp) {
    console.log("Start Date: " + emp.startDate); // emp is Employee here
  }
}
```

### Equality Narrowing <a name="equality-narrowing"></a>
Using `===`, `!==`, `==`, `!=` can narrow types when comparing to literal values.
```typescript
function example(x: string | number, y: string | boolean) {
  if (x === y) {
    // x and y are both 'string' here
    x.toUpperCase();
    y.toLowerCase();
  }
}
```

### User-Defined Type Guards (Type Predicates) <a name="user-defined-type-guards"></a>
Functions that return a boolean and have a special return type signature: `parameterName is Type`.
```typescript
interface Cat {
  meow(): void;
}
interface Dog {
  bark(): void;
}

function isCat(animal: Cat | Dog): animal is Cat {
  return (animal as Cat).meow !== undefined;
}

function makeSound(animal: Cat | Dog) {
  if (isCat(animal)) {
    animal.meow(); // animal is Cat here
  } else {
    animal.bark(); // animal is Dog here
  }
}
```

## 5. Nullable Types and `strictNullChecks` <a name="nullable-types"></a>
When `strictNullChecks` is enabled in `tsconfig.json` (highly recommended), `null` and `undefined` are not assignable to other types unless explicitly included in a union type (e.g., `string | null`).

### Optional Chaining (`?.`) Recap <a name="optional-chaining-recap"></a>
Safely access properties of potentially null or undefined objects.
```typescript
const user = { address: { street: '123 Main St' } };
const street = user?.address?.street; // string | undefined
```

### Nullish Coalescing (`??`) Recap <a name="nullish-coalescing-recap"></a>
Provides a default value when an expression is `null` or `undefined`.
```typescript
let userInput = null;
const storedData = userInput ?? 'DEFAULT'; // 'DEFAULT'
```

### Non-null Assertion Operator (`!`) <a name="non-null-assertion"></a>
Tells the TypeScript compiler that a value is not `null` or `undefined`, even if its type suggests it could be. Use with caution, as it can lead to runtime errors if the assertion is incorrect.
```typescript
function process(value: string | null | undefined) {
  const upper = value!.toUpperCase(); // Asserts value is not null/undefined
}
```

## 6. Type Aliases (`type`) Deep Dive <a name="type-aliases-deep-dive"></a>
Type aliases create a new name for a type. They can name primitives, unions, intersections, tuples, and any other types.
Unlike interfaces, type aliases can also represent utility types, mapped types, conditional types, etc.

```typescript
type Point = { x: number; y: number };
type ID = string | number;
type StringOrNumberArray = (string | number)[];
type Tree<T> = {
  value: T;
  left?: Tree<T>;
  right?: Tree<T>;
};
```

## 7. Literal Types (String, Number, Boolean) <a name="literal-types-revisited"></a>
Allows you to specify the exact values a string, number, or boolean must have.
```typescript
type Alignment = "left" | "right" | "center";
type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;
type Result = true | "Error";

let textAlign: Alignment = "left";
// textAlign = "justify"; // Error
```
Often used with union types for a set of allowed literal values.

## 8. Index Types <a name="index-types"></a>
TypeScript provides index types to work with properties of an object in a more dynamic yet type-safe way.

### `keyof` Type Operator <a name="keyof-operator"></a>
Takes an object type and produces a string or numeric literal union of its keys.
```typescript
interface Person {
  name: string;
  age: number;
  location: string;
}

type PersonKeys = keyof Person; // "name" | "age" | "location"

function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const person: Person = { name: "Alice", age: 30, location: "Wonderland" };
const personName = getProperty(person, "name"); // string
const personAge = getProperty(person, "age");   // number
```

### Indexed Access Types (Lookup Types) `T[K]` <a name="indexed-access-types"></a>
Allows you to look up the type of a property on another type.
```typescript
type PersonNameType = Person["name"]; // string
type PersonAgeType = Person["age"];   // number

type PropertyTypes = Person[keyof Person]; // string | number (union of all property types)
```

## 9. Mapped Types <a name="mapped-types"></a>
Mapped types allow you to create new object types based on an existing object type by transforming its properties.

### Basic Mapped Types <a name="basic-mapped-types"></a>
Syntax: `[P in K]: NewType` where `K` is a union of literal types (often `keyof T`).

```typescript
// Makes all properties of T optional
type Partial<T> = {
  [P in keyof T]?: T[P];
};

// Makes all properties of T readonly
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

interface User {
  id: number;
  name: string;
  email?: string;
}

type PartialUser = Partial<User>; // { id?: number; name?: string; email?: string; }
type ReadonlyUser = Readonly<User>; // { readonly id: number; readonly name: string; readonly email?: string; }
```
(Note: `Partial` and `Readonly` are built-in utility types, shown here for illustration.)

### Mapping Modifiers <a name="mapping-modifiers"></a>
You can add or remove `readonly` and `?` (optional) modifiers during mapping using `+` or `-` prefixes.
```typescript
// Removes 'readonly' from all properties of T
type Mutable<T> = {
  -readonly [P in keyof T]: T[P];
};

// Makes all properties of T required (removes '?')
type Concrete<T> = {
  [P in keyof T]-?: T[P];
};

interface Config {
  readonly host: string;
  port?: number;
}

type MutableConfig = Mutable<Config>; // { host: string; port?: number; }
type ConcreteConfig = Concrete<Config>; // { readonly host: string; port: number; }
```

### Key Remapping via `as` <a name="key-remapping-as"></a>
Allows remapping property keys to new names during the mapping process.
```typescript
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K]
};

interface Person {
  name: string;
  age: number;
}

type PersonGetters = Getters<Person>;
/*
PersonGetters = {
  getName: () => string;
  getAge: () => number;
}
*/
```

## 10. Conditional Types <a name="conditional-types"></a>
Conditional types allow you to choose one of two possible types based on a condition expressed as a type relationship test.

### Syntax: `T extends U ? X : Y` <a name="conditional-type-syntax"></a>
If `T` is assignable to `U`, the type is `X`; otherwise, it's `Y`.
```typescript
interface IdLabel { id: number; /* ... */ }
interface NameLabel { name: string; /* ... */ }

type LabelType<T> = T extends number ? IdLabel : NameLabel;

function createLabel<T extends string | number>(idOrName: T): LabelType<T> {
  throw "unimplemented";
}

let a = createLabel("typescript"); // Type is NameLabel
let b = createLabel(2.8);       // Type is IdLabel
let c = createLabel(Math.random() ? "hello" : 42); // Type is IdLabel | NameLabel
```

### Distributive Conditional Types <a name="distributive-conditional-types"></a>
When the checked type `T` in `T extends U ? X : Y` is a naked type parameter (not wrapped in `Array<T>`, `Promise<T>`, etc.), the conditional type becomes distributive. This means if `T` is a union type, the conditional type is applied to each member of the union.

```typescript
type ToArray<Type> = Type extends any ? Type[] : never;
type StrArrOrNumArr = ToArray<string | number>; // string[] | number[]

// To prevent distribution, wrap T in a tuple:
type ToArrayNonDist<Type> = [Type] extends [any] ? Type[] : never;
type StrOrNumArr = ToArrayNonDist<string | number>; // (string | number)[]
```

### Inferring Types with `infer` <a name="infer-keyword"></a>
The `infer` keyword can be used within the `extends` clause of a conditional type to declare a type variable that will be inferred from the "true" branch.

```typescript
// Extracts the type of an array's elements
type Flatten<T> = T extends Array<infer Item> ? Item : T;

type Str = Flatten<string[]>; // string
type Num = Flatten<number>;   // number

// Extracts the return type of a function
type GetReturnType<Type> = Type extends (...args: never[]) => infer Return ? Return : never;

type NumFuncReturn = GetReturnType<() => number>; // number
type StrFuncReturn = GetReturnType<(x: string) => string>; // string
```

## 11. Built-in Utility Types <a name="utility-types"></a>
TypeScript provides several utility types to facilitate common type transformations.

### `Partial<T>` <a name="partial-t"></a>
Constructs a type with all properties of `T` set to optional.

### `Required<T>` <a name="required-t"></a>
Constructs a type with all properties of `T` set to required (removes `?`).

### `Readonly<T>` <a name="readonly-t"></a>
Constructs a type with all properties of `T` set to `readonly`.

### `Record<K, T>` <a name="record-k-t"></a>
Constructs an object type whose property keys are `K` and property values are `T`.
`K` must be a string, number, symbol, or a union of these.
```typescript
interface CatInfo {
  age: number;
  breed: string;
}
type CatName = "miffy" | "boris" | "mordred";
const cats: Record<CatName, CatInfo> = {
  miffy: { age: 10, breed: "Persian" },
  boris: { age: 5, breed: "Maine Coon" },
  mordred: { age: 16, breed: "British Shorthair" }
};
```

### `Pick<T, K>` <a name="pick-t-k"></a>
Constructs a type by picking the set of properties `K` (string literal or union of string literals) from `T`.
```typescript
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}
type TodoPreview = Pick<Todo, "title" | "completed">;
// { title: string; completed: boolean; }
```

### `Omit<T, K>` <a name="omit-t-k"></a>
Constructs a type by picking all properties from `T` and then removing `K`.
```typescript
type TodoInfo = Omit<Todo, "description">;
// { title: string; completed: boolean; }
```

### `Exclude<T, U>` <a name="exclude-t-u"></a>
Constructs a type by excluding from `T` all union members that are assignable to `U`.
```typescript
type T0 = Exclude<"a" | "b" | "c", "a">; // "b" | "c"
type T1 = Exclude<string | number | (() => void), Function>; // string | number
```

### `Extract<T, U>` <a name="extract-t-u"></a>
Constructs a type by extracting from `T` all union members that are assignable to `U`.
```typescript
type T0 = Extract<"a" | "b" | "c", "a" | "f">; // "a"
type T1 = Extract<string | number | (() => void), Function>; // () => void
```

### `NonNullable<T>` <a name="nonnullable-t"></a>
Constructs a type by excluding `null` and `undefined` from `T`.
```typescript
type T0 = NonNullable<string | number | undefined>; // string | number
type T1 = NonNullable<string[] | null | undefined>; // string[]
```

### `Parameters<T>` <a name="parameters-t"></a>
Constructs a tuple type from the types used in the parameters of a function type `T`.
```typescript
declare function f1(arg: { a: number; b: string }): void;
type T0 = Parameters<() => string>; // []
type T1 = Parameters<(s: string) => void>; // [s: string]
type T2 = Parameters<<T>(arg: T) => T>; // [arg: unknown]
type T3 = Parameters<typeof f1>; // [arg: { a: number; b: string }]
```

### `ConstructorParameters<T>` <a name="constructorparameters-t"></a>
Constructs a tuple or array type from the types of a constructor function type.
```typescript
class MyClass {
  constructor(public name: string, public age: number) {}
}
type T0 = ConstructorParameters<ErrorConstructor>; // [message?: string]
type T1 = ConstructorParameters<typeof MyClass>; // [name: string, age: number]
```

### `ReturnType<T>` <a name="returntype-t"></a>
Constructs a type consisting of the return type of function `T`.
```typescript
type T0 = ReturnType<() => string>; // string
type T1 = ReturnType<(s: string) => void>; // void
```

### `InstanceType<T>` <a name="instancetype-t"></a>
Constructs a type consisting of the instance type of a constructor function type `T`.
```typescript
class C { x = 0; y = 0; }
type T0 = InstanceType<typeof C>; // C
type T1 = InstanceType<any>; // any
```

### `ThisParameterType<T>` <a name="thisparametertype-t"></a>
Extracts the type of the `this` parameter for a function type, or `unknown` if the function type has no `this` parameter.

### `OmitThisParameter<T>` <a name="omitthisparameter-t"></a>
Removes the `this` parameter from a function type `T`.

### `ThisType<T>` <a name="thistype-t"></a>
This utility does not return a new type. Instead, it serves as a marker for a contextual `this` type. Note that you need to enable `noImplicitThis` to use this utility.

### String Manipulation Types <a name="string-manipulation-types"></a>
`Uppercase<S>`, `Lowercase<S>`, `Capitalize<S>`, `Uncapitalize<S>`: Transform string literal types.
```typescript
type Greeting = "hello world";
type ShoutyGreeting = Uppercase<Greeting>; // "HELLO WORLD"
type QuietGreeting = Lowercase<ShoutyGreeting>; // "hello world"
```

## 12. Template Literal Types <a name="template-literal-types"></a>
Template literal types build on string literal types and have the ability to expand into many strings via unions. They have the same syntax as template literals in JavaScript but are used in type positions.

```typescript
type EmailLocaleIDs = "welcome_email" | "email_heading";
type FooterLocaleIDs = "footer_title" | "footer_sendoff";

type AllLocaleIDs = `${EmailLocaleIDs | FooterLocaleIDs}_id`;
// "welcome_email_id" | "email_heading_id" | "footer_title_id" | "footer_sendoff_id"

type Lang = "en" | "ja" | "pt";
type LocaleMessageIDs = `${Lang}_${AllLocaleIDs}`;
// "en_welcome_email_id" | "en_email_heading_id" | ...
// "ja_welcome_email_id" | "ja_email_heading_id" | ...
```
Can be combined with generic types and inference for powerful string manipulation at the type level.

## 13. Symbols and Unique Symbols <a name="symbols-unique-symbols-ts"></a>
TypeScript supports `symbol` and `unique symbol` types.
`unique symbol` is a subtype of `symbol` and is produced only from calling `Symbol()` or `Symbol.for()`, or from explicit type annotations.
They are useful for creating unique property keys that avoid name collisions.

```typescript
const sym1 = Symbol();
const sym2 = Symbol("key"); // optional string key

// unique symbol
const uniqueSym: unique symbol = Symbol();

interface MyObject {
  [uniqueSym]: string;
}
```

## 14. Nominal Typing (Emulation) <a name="nominal-typing-emulation"></a>
TypeScript uses structural typing by default. Sometimes, nominal typing (where types are distinguished by their names, not just their structure) is desired. This can be emulated using techniques like "branding" or "tagging".

### Branding / Tagging <a name="branding-tagging"></a>
Add a unique property (the "brand" or "tag") to a type to make it nominally distinct.
```typescript
type Brand<K, T> = K & { __brand: T };

type UserID = Brand<string, "UserID">;
type ProductID = Brand<string, "ProductID">;

function getUser(id: UserID) { /* ... */ }
function getProduct(id: ProductID) { /* ... */ }

let uid = "user-123" as UserID;
let pid = "prod-456" as ProductID;

getUser(uid);
// getUser(pid); // Error: Type 'ProductID' is not assignable to type 'UserID'.
```

## 15. Decorators (Brief Overview - Experimental) <a name="decorators-ts"></a>
Decorators provide a way to add annotations and meta-programming syntax for class declarations and members. They are an experimental feature that requires enabling the `experimentalDecorators` compiler option in `tsconfig.json`.

Common use cases:
*   Modifying or replacing class definitions, methods, accessors, properties, or parameters.
*   Used extensively in frameworks like Angular and NestJS.

Example (conceptual):
```typescript
/*
@sealed
class Greeter {
  @format("Hello, %s")
  greeting: string;
  constructor(message: string) {
    this.greeting = message;
  }
  greet() {
    // ...
  }
}
*/
```
Due to their experimental nature and evolving specification, a detailed discussion is beyond this scope.

## 16. Best Practices for Advanced Types <a name="best-practices-advanced-ts"></a>
*   **Prefer `interface` for object shapes and `type` for aliases, unions, intersections, primitives, etc.** However, they are often interchangeable for object shapes.
*   **Enable `strictNullChecks`**: This is crucial for leveraging TypeScript's null safety.
*   **Use `unknown` instead of `any`** when the type is truly unknown. `unknown` is type-safe as it requires type checking before operations.
*   **Keep types simple and readable**: Complex types can be hard to understand and debug.
*   **Leverage utility types**: Don't reinvent the wheel.
*   **Use discriminated unions** for modeling state or variants effectively.
*   **Be cautious with type assertions (`as`) and non-null assertions (`!`)**: They bypass type checking and can lead to runtime errors.

## 17. Key Takeaways <a name="key-takeaways-advanced-ts"></a>
*   TypeScript's advanced type system provides powerful tools for creating precise and flexible type definitions.
*   **Type guards** (typeof, instanceof, in, user-defined) are essential for working with union types.
*   **Index types** (`keyof`, `T[K]`) allow for type-safe manipulation of object properties.
*   **Mapped types** transform existing types into new ones (e.g., making properties optional or readonly).
*   **Conditional types** (`T extends U ? X : Y`, `infer`) enable type logic based on conditions.
*   **Utility types** (`Partial`, `Pick`, `Omit`, `Record`, etc.) offer convenient type transformations.
*   Techniques like **discriminated unions** and **template literal types** enhance type expressiveness.
*   Understanding these advanced concepts allows for more robust and maintainable TypeScript code.

---

⬅️ [Back to Introduction to TypeScript](../25-js-intro-to-typescript/README.md) | ➡️ [Next Section: TypeScript Configuration & Compiler Options In-Depth](../27-ts-config-compiler-options/README.md) (Placeholder for future TypeScript series)
