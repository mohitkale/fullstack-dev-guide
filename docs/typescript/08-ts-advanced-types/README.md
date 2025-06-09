# 08. TypeScript Advanced Types

TypeScript offers a rich set of advanced type features that allow for sophisticated type manipulation and modeling complex scenarios. These features enhance type safety and code expressiveness.

This tutorial covers several key advanced type concepts.

## Table of Contents
1.  [Intersection Types (`&`)](#intersection-types)
2.  [Union Types (`|`) Revisited](#union-types-revisited)
3.  [Type Guards](#type-guards)
    *   [`typeof` Type Guards](#typeof-guards)
    *   [`instanceof` Type Guards](#instanceof-guards)
    *   [`in` Operator Type Guard](#in-guards)
    *   [User-Defined Type Guards (Type Predicates)](#user-defined-type-guards)
4.  [Nullable Types](#nullable-types)
    *   [`strictNullChecks`](#strictnullchecks)
    *   [Optional Chaining (`?.`)](#optional-chaining)
    *   [Nullish Coalescing Operator (`??`)](#nullish-coalescing)
5.  [Type Aliases Revisited](#type-aliases-revisited)
6.  [Index Types](#index-types)
    *   [`keyof` Type Operator](#keyof-operator)
    *   [Indexed Access Types (`T[K]`)](#indexed-access-types)
7.  [Mapped Types](#mapped-types)
    *   [Basic Mapped Types](#basic-mapped-types)
    *   [Mapping Modifiers (`readonly`, `?`)](#mapping-modifiers)
    *   [Key Remapping via `as`](#key-remapping-as)
8.  [Conditional Types](#conditional-types)
    *   [Basic Conditional Types](#basic-conditional-types)
    *   [Distributive Conditional Types](#distributive-conditional-types)
    *   [Inferring Types with `infer`](#inferring-with-infer)
    *   [Predefined Conditional Types (`Exclude`, `Extract`, `NonNullable`, `ReturnType`, `InstanceType`)](#predefined-conditional-types)
9.  [Key Takeaways](#key-takeaways-advanced-types)

## 1. Intersection Types (`&`) <a name="intersection-types"></a>
Intersection types combine multiple types into one. This means an object of an intersection type will have all members of all the types being combined.

```typescript
interface Person {
  name: string;
  age: number;
}

interface Employee {
  employeeId: string;
  startDate: Date;
}

type PersonEmployee = Person & Employee;

let emp: PersonEmployee = {
  name: "Alice",
  age: 30,
  employeeId: "E123",
  startDate: new Date()
};

console.log(emp.name, emp.employeeId);
```
This is useful for mixing existing types to create a new type that has all the features of its components.

## 2. Union Types (`|`) Revisited <a name="union-types-revisited"></a>
Union types allow a value to be one of several types. We've seen them with literal types, but they are broadly applicable.

```typescript
function padLeft(value: string, padding: string | number): string {
  if (typeof padding === "number") {
    return Array(padding + 1).join(" ") + value;
  }
  if (typeof padding === "string") {
    return padding + value;
  }
  throw new Error(`Expected string or number, got '${typeof padding}'.`);
}

console.log(padLeft("Hello", 4));      // "    Hello"
console.log(padLeft("Hello", "---")); // "---Hello"
// console.log(padLeft("Hello", true)); // Error
```
When using union types, you can only access members that are common to all types in the union. To access type-specific members, you need to use type guards.

## 3. Type Guards <a name="type-guards"></a>
Type guards are expressions that perform a runtime check that guarantees the type in some scope. They allow TypeScript to narrow down a variable's type from a broader type (like a union type or `any`) to a more specific type.

### `typeof` Type Guards <a name="typeof-guards"></a>
The `typeof` operator can check for primitive types: `"string"`, `"number"`, `"bigint"`, `"boolean"`, `"symbol"`, `"undefined"`, `"object"`, `"function"`.

```typescript
function processInput(input: string | number | string[]) {
  if (typeof input === "string") {
    console.log(input.toUpperCase()); // input is string here
  } else if (typeof input === "number") {
    console.log(input.toFixed(2)); // input is number here
  } else {
    console.log(input.join(", ")); // input is string[] here
  }
}
processInput("test");
processInput(123.456);
processInput(["a", "b"]);
```

### `instanceof` Type Guards <a name="instanceof-guards"></a>
The `instanceof` operator checks if an object is an instance of a particular class or constructor function. It's useful for narrowing types of objects created with `new`.

```typescript
class Bird {
  fly() { console.log("Flying..."); }
  layEggs() { console.log("Laying eggs..."); }
}

class Fish {
  swim() { console.log("Swimming..."); }
  layEggs() { console.log("Laying eggs..."); }
}

function getPet(): Bird | Fish {
  return Math.random() > 0.5 ? new Bird() : new Fish();
}

let pet = getPet();

if (pet instanceof Bird) {
  pet.fly(); // pet is Bird here
} else {
  pet.swim(); // pet is Fish here
}
pet.layEggs(); // Common property, accessible outside guards
```

### `in` Operator Type Guard <a name="in-guards"></a>
The `in` operator checks if an object has a specific property. This can be used to distinguish between types in a union if they have unique properties.

```typescript
interface Car {
  drive(): void;
  wheels: number;
}

interface Bicycle {
  ride(): void;
  wheels: number;
}

function moveVehicle(vehicle: Car | Bicycle) {
  if ("drive" in vehicle) {
    vehicle.drive(); // vehicle is Car here
  } else {
    vehicle.ride(); // vehicle is Bicycle here
  }
  console.log(`Wheels: ${vehicle.wheels}`);
}

moveVehicle({ drive: () => console.log("Car driving"), wheels: 4 });
moveVehicle({ ride: () => console.log("Bicycle riding"), wheels: 2 });
```

### User-Defined Type Guards (Type Predicates) <a name="user-defined-type-guards"></a>
Sometimes, `typeof` and `instanceof` aren't enough. You can define your own type guard functions. A user-defined type guard is a function whose return type is a **type predicate** of the form `parameterName is Type`.

```typescript
interface Cat {
  meow(): void;
  purr?: boolean;
}

interface Dog {
  bark(): void;
  wagTail?: boolean;
}

// User-defined type guard
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

makeSound({ meow: () => console.log("Meow!") });
makeSound({ bark: () => console.log("Woof!") });
```

## 4. Nullable Types <a name="nullable-types"></a>
TypeScript has two special types, `null` and `undefined`, which represent the absence of a value.

### `strictNullChecks` <a name="strictnullchecks"></a>
When the `strictNullChecks` compiler option is `true` (recommended), `null` and `undefined` are distinct types and cannot be assigned to variables of other types unless explicitly included in a union type.

```typescript
// With strictNullChecks: true in tsconfig.json
let s: string = "hello";
// s = null; // Error: Type 'null' is not assignable to type 'string'.
// s = undefined; // Error: Type 'undefined' is not assignable to type 'string'.

let sn: string | null = "world";
sn = null; // OK

function greet(name: string | null) {
  if (name === null) {
    console.log("Hello, guest!");
  } else {
    console.log(`Hello, ${name.toUpperCase()}!`); // name is string here
  }
}
greet("Alice");
greet(null);
```

### Optional Chaining (`?.`) <a name="optional-chaining"></a>
Optional chaining allows you to safely access properties and call methods on potentially `null` or `undefined` values. If the value before `?.` is `null` or `undefined`, the expression short-circuits and evaluates to `undefined`.

```typescript
interface UserProfile {
  address?: {
    street?: string;
    city: string;
  };
  getName?: () => string;
}

let user1: UserProfile = { address: { city: "New York" } };
let user2: UserProfile = {};

console.log(user1.address?.street?.toUpperCase()); // undefined (street is undefined)
console.log(user2.address?.city); // undefined (address is undefined)
console.log(user1.getName?.()); // undefined (getName is undefined)
```

### Nullish Coalescing Operator (`??`) <a name="nullish-coalescing"></a>
The nullish coalescing operator (`??`) provides a default value when an expression evaluates to `null` or `undefined`. It's similar to `||` but only triggers for `null` or `undefined`, not for other falsy values like `0`, `''`, or `false`.

```typescript
let username: string | null = null;
let displayName = username ?? "Guest";
console.log(displayName); // "Guest"

let count: number | undefined = 0;
let displayCount = count ?? 10;
console.log(displayCount); // 0 (because 0 is not null or undefined)

let displayCountWithOr = count || 10;
console.log(displayCountWithOr); // 10 (because 0 is falsy)
```

## 5. Type Aliases Revisited <a name="type-aliases-revisited"></a>
Type aliases (`type Name = ...`) can name any type, not just object types. They are particularly useful for naming union types, intersection types, tuples, or complex generic types.

```typescript
type Point = { x: number; y: number };
type ID = string | number;
type StringOrNumberArray = (string | number)[];
type Tree<T> = {
  value: T;
  left?: Tree<T>;
  right?: Tree<T>;
};

let p: Point = { x: 10, y: 20 };
let id: ID = "abc123";
let arr: StringOrNumberArray = ["hello", 10];
let numTree: Tree<number> = { value: 1, left: { value: 2 }, right: { value: 3 } };
```
Unlike interfaces, type aliases cannot be reopened to add new properties (they are not "declaration mergable").

## 6. Index Types <a name="index-types"></a>
Index types allow you to create types that describe objects where property names are not known beforehand, or to manipulate properties of existing types.

### `keyof` Type Operator <a name="keyof-operator"></a>
The `keyof` operator takes an object type and produces a string or numeric literal union of its keys.

```typescript
interface Coords {
  x: number;
  y: number;
  z?: number;
}

type CoordKeys = keyof Coords; // Type is "x" | "y" | "z"

let key: CoordKeys = "x";
// key = "a"; // Error

function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

let coords: Coords = { x: 1, y: 2 };
console.log(getProperty(coords, "x")); // 1
// console.log(getProperty(coords, "z")); // Error if z is not present and strictNullChecks is on, unless T[K] includes undefined
```

### Indexed Access Types (`T[K]`) <a name="indexed-access-types"></a>
Also known as lookup types, `T[K]` allows you to look up the type of a property `K` on type `T`.

```typescript
interface Car {
  make: string;
  model: string;
  year: number;
}

type MakeType = Car["make"]; // string
type ModelType = Car["model"]; // string
type YearType = Car["year"]; // number

type CarPropertyType = Car[keyof Car]; // string | number (union of all property types)

let carMake: MakeType = "Toyota";
```

## 7. Mapped Types <a name="mapped-types"></a>
Mapped types allow you to create new object types by transforming properties from an existing type. The syntax is `[P in K]: T`, where `K` is a union of string/number literals (often from `keyof`) and `P` iterates over these keys.

### Basic Mapped Types <a name="basic-mapped-types"></a>
```typescript
interface Point {
  x: number;
  y: number;
}

// Creates a type with the same properties as Point, but all are readonly
type ReadonlyPoint = { readonly [P in keyof Point]: Point[P] };

let p1: ReadonlyPoint = { x: 10, y: 20 };
// p1.x = 5; // Error: Cannot assign to 'x' because it is a read-only property.

// Creates a type with the same properties as Point, but all are optional
type PartialPoint = { [P in keyof Point]?: Point[P] };

let p2: PartialPoint = { x: 10 }; // y is optional

// Generic versions of these are very common (e.g., Readonly<T>, Partial<T>)
```
TypeScript provides built-in `Readonly<T>`, `Partial<T>`, `Pick<T, K>`, and `Record<K, T>` utility types that use mapped types.

### Mapping Modifiers (`readonly`, `?`) <a name="mapping-modifiers"></a>
You can add or remove `readonly` and `?` (optional) modifiers during mapping using `+` or `-` prefixes.

```typescript
interface MutableConfig {
  id: string;
  name?: string;
}

// Remove readonly (if it was there) and make all properties required
type Concrete<T> = { -readonly [P in keyof T]-?: T[P] };

type RequiredConfig = Concrete<MutableConfig>;
// type RequiredConfig = {
//   id: string;
//   name: string;
// }

let config: RequiredConfig = { id: "123", name: "App Config" };
// config.name = undefined; // Error: Type 'undefined' is not assignable to type 'string'.
```

### Key Remapping via `as` <a name="key-remapping-as"></a>
You can remap keys in a mapped type using an `as` clause.

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

// Creates a type where each property key of User gets 'Getter' appended
// e.g., { getId: () => number; getName: () => string; ... }
type Getters<T> = {
  [P in keyof T as `get${Capitalize<string & P>}`]: () => T[P]
};

type UserGetters = Getters<User>;
/*
UserGetters is equivalent to:
{
  getId: () => number;
  getName: () => string;
  getEmail: () => string;
}
*/

// Example usage (implementation would be separate)
// let userAccessors: UserGetters = {
//   getId: () => 1,
//   getName: () => "Alice",
//   getEmail: () => "alice@example.com"
// };
```
`Capitalize` is a built-in string manipulation type.

## 8. Conditional Types <a name="conditional-types"></a>
Conditional types choose one of two possible types based on a condition expressed as a type relationship test: `SomeType extends OtherType ? TrueType : FalseType;`

### Basic Conditional Types <a name="basic-conditional-types"></a>
```typescript
interface IdLabel {
  id: number /* some fields */;
}
interface NameLabel {
  name: string /* other fields */;
}

type NameOrId<T extends number | string> = T extends number
  ? IdLabel
  : NameLabel;

function createLabel<T extends number | string>(idOrName: T): NameOrId<T> {
  throw "unimplemented";
}

let a = createLabel("typescript"); // Type of a is NameLabel
let b = createLabel(2.8);       // Type of b is IdLabel
let c = createLabel(Math.random() > 0.5 ? "hello" : 42); // Type of c is NameLabel | IdLabel
```

### Distributive Conditional Types <a name="distributive-conditional-types"></a>
When the type being checked in a conditional type is a naked type parameter (e.g., `T extends U ? X : Y`, where `T` is just `T` and not `T[]` or `Promise<T>`), and the conditional type is instantiated with a union type, it becomes distributive. This means the conditional type is applied to each member of the union.

```typescript
type ToArray<T> = T extends any ? T[] : never;

// Type is string[] | number[] because T is a naked type parameter
type StrOrNumArray = ToArray<string | number>;
// StrOrNumArray is (string extends any ? string[] : never) | (number extends any ? number[] : never)
// which simplifies to string[] | number[]

// To prevent distribution, wrap the checked type and the extends constraint in square brackets:
type ToArrayNonDist<T> = [T] extends [any] ? T[] : never;

// Type is (string | number)[] because distribution is prevented
type StrOrNumArrayNonDist = ToArrayNonDist<string | number>;
```

### Inferring Types with `infer` <a name="inferring-with-infer"></a>
The `infer` keyword can be used in conditional types within an `extends` clause to declare a type variable that will be inferred from the "true" branch.

```typescript
// Infers the element type of an array
type Flatten<T> = T extends Array<infer Item> ? Item : T;

type Str = Flatten<string[]>;   // string
type Num = Flatten<number[]>;   // number
type NotAnArray = Flatten<boolean>; // boolean

// Infers the return type of a function
type GetReturnType<T> = T extends (...args: any[]) => infer Return ? Return : never;

type NumReturn = GetReturnType<() => number>; // number
type StrReturn = GetReturnType<(x: string) => string>; // string
type BoolReturn = GetReturnType<(a: boolean, b: number) => boolean>; // boolean
```

### Predefined Conditional Types <a name="predefined-conditional-types"></a>
TypeScript provides several built-in utility types that are implemented using conditional types:
*   `Exclude<T, U>`: Computes a type by excluding from `T` all properties that are assignable to `U`.
    ```typescript
    type T0 = Exclude<"a" | "b" | "c", "a">; // "b" | "c"
    ```
*   `Extract<T, U>`: Computes a type by extracting from `T` all properties that are assignable to `U`.
    ```typescript
    type T1 = Extract<"a" | "b" | "c", "a" | "f">; // "a"
    ```
*   `NonNullable<T>`: Computes a type by excluding `null` and `undefined` from `T`.
    ```typescript
    type T2 = NonNullable<string | number | undefined | null>; // string | number
    ```
*   `ReturnType<T>`: Obtains the return type of a function type.
    ```typescript
    type T3 = ReturnType<() => string>; // string
    ```
*   `InstanceType<T>`: Obtains the instance type of a constructor function type.
    ```typescript
    class C { x = 0; y = 0; }
    type T4 = InstanceType<typeof C>; // C (the class instance type)
    type T5 = InstanceType<any>; // any
    // type T6 = InstanceType<string>; // Error
    ```

## 9. Key Takeaways <a name="key-takeaways-advanced-types"></a>
*   **Intersection Types (`&`)** combine multiple types into one.
*   **Type Guards** (`typeof`, `instanceof`, `in`, user-defined) narrow types within a scope.
*   **Nullable Types** (`null`, `undefined`) are handled robustly with `strictNullChecks`, optional chaining (`?.`), and nullish coalescing (`??`).
*   **Type Aliases** provide names for any type, enhancing readability.
*   **Index Types** (`keyof`, `T[K]`) allow manipulation of properties based on their keys.
*   **Mapped Types** create new types by transforming properties of existing types, often used for utility types like `Readonly<T>` or `Partial<T>`.
*   **Conditional Types** (`T extends U ? X : Y`) enable type selection based on conditions, with powerful features like `infer` and distributivity.

These advanced type features are fundamental to writing expressive, maintainable, and type-safe TypeScript code, especially when dealing with complex data structures and APIs.

---

⬅️ [Back to Enums and Literal Types](../07-ts-enums-literal-types/README.md) | ➡️ [Next: Modules and Namespaces](../09-ts-modules-namespaces/README.md)
