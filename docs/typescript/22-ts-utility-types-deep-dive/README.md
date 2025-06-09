# 22. TypeScript Utility Types (Deep Dive)

In Tutorial #14, we introduced several of TypeScript's built-in utility types that help with common type transformations. This tutorial takes a deeper dive, exploring these and other utility types in more detail, understanding how they work (often using conditional types and `infer`), and learning how to create your own custom utility types for advanced type manipulation.

## Table of Contents
1.  [Recap: Why Utility Types?](#recap-why-utility-types)
2.  [Revisiting Common Utility Types (In-Depth)](#revisiting-common-utility-types)
    *   `Partial<Type>`
    *   `Required<Type>`
    *   `Readonly<Type>`
    *   `Pick<Type, Keys>`
    *   `Omit<Type, Keys>`
    *   `Record<Keys, Type>`
3.  [Exploring Advanced Built-in Utility Types](#exploring-advanced-utility-types)
    *   `Exclude<UnionType, ExcludedMembers>`
    *   `Extract<Type, Union>`
    *   `NonNullable<Type>`
    *   `Parameters<Type>`
    *   `ConstructorParameters<Type>`
    *   `ReturnType<Type>`
    *   `InstanceType<Type>`
4.  [String Manipulation Types](#string-manipulation-types)
    *   `Uppercase<StringType>`
    *   `Lowercase<StringType>`
    *   `Capitalize<StringType>`
    *   `Uncapitalize<StringType>`
5.  [Template Literal Types and Their Power](#template-literal-types)
    *   Creating Complex String Types
    *   Using with Utility Types
6.  [Understanding the Building Blocks: Conditional Types](#conditional-types)
    *   Syntax: `SomeType extends OtherType ? TrueType : FalseType`
    *   Distributive Conditional Types
7.  [The `infer` Keyword](#infer-keyword)
    *   Inferring Types within Conditional Types
    *   How `Parameters`, `ReturnType`, etc., are implemented
8.  [Creating Custom Utility Types](#creating-custom-utility-types)
    *   Example: `Writable<Type>`
    *   Example: `DeepPartial<Type>` (Recursive Utility Type)
    *   Example: `PromiseType<P>` (Extracting type from a Promise)
    *   Example: `Getters<Type>` (Mapping object properties to getter functions)
9.  [Mapped Types Revisited](#mapped-types-revisited)
    *   Mapping Modifiers (`readonly`, `?`)
    *   Key Remapping via `as`
    *   Combining with Conditional Types
10. [Advanced Patterns and Use Cases](#advanced-patterns-use-cases)
    *   Type Guards from Utility Types
    *   Transforming Object Keys
    *   Creating Discriminated Unions more safely
11. [Limitations and Considerations](#limitations-considerations-utility-types)
12. [Key Takeaways](#key-takeaways-utility-types-deep)

## 1. Recap: Why Utility Types? <a name="recap-why-utility-types"></a>
Utility types are pre-defined generic types that perform transformations on existing types. They help create new types based on old ones without redundant manual definitions, promoting DRY (Don't Repeat Yourself) principles and enhancing type safety.

## 2. Revisiting Common Utility Types (In-Depth) <a name="revisiting-common-utility-types"></a>
Let's briefly look at how some common utility types are often implemented (conceptually).

*   **`Partial<Type>`:** Makes all properties of `Type` optional.
    ```typescript
    type Partial<T> = {
      [P in keyof T]?: T[P];
    };
    ```
*   **`Required<Type>`:** Makes all properties of `Type` required.
    ```typescript
    type Required<T> = {
      [P in keyof T]-?: T[P]; // -? removes optionality
    };
    ```
*   **`Readonly<Type>`:** Makes all properties of `Type` readonly.
    ```typescript
    type Readonly<T> = {
      readonly [P in keyof T]: T[P];
    };
    ```
*   **`Pick<Type, Keys>`:** Constructs a type by picking a set of properties `Keys` (string literal or union of string literals) from `Type`.
    ```typescript
    type Pick<T, K extends keyof T> = {
      [P in K]: T[P];
    };
    ```
*   **`Omit<Type, Keys>`:** Constructs a type by picking all properties from `Type` and then removing `Keys`.
    ```typescript
    type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
    // Uses Exclude, which we'll cover next.
    ```
*   **`Record<Keys, Type>`:** Constructs an object type whose property keys are `Keys` and property values are `Type`.
    ```typescript
    type Record<K extends keyof any, T> = {
      [P in K]: T;
    };
    ```

## 3. Exploring Advanced Built-in Utility Types <a name="exploring-advanced-utility-types"></a>

*   **`Exclude<UnionType, ExcludedMembers>`:** Constructs a type by excluding from `UnionType` all union members that are assignable to `ExcludedMembers`.
    ```typescript
    type T0 = Exclude<"a" | "b" | "c", "a">; // "b" | "c"
    type T1 = Exclude<string | number | (() => void), Function>; // string | number
    // Implementation (conceptual):
    // type Exclude<T, U> = T extends U ? never : T; // Uses conditional types
    ```
*   **`Extract<Type, Union>`:** Constructs a type by extracting from `Type` all union members that are assignable to `Union`.
    ```typescript
    type T2 = Extract<"a" | "b" | "c", "a" | "f">; // "a"
    type T3 = Extract<string | number | (() => void), Function>; // () => void
    // Implementation (conceptual):
    // type Extract<T, U> = T extends U ? T : never;
    ```
*   **`NonNullable<Type>`:** Constructs a type by excluding `null` and `undefined` from `Type`.
    ```typescript
    type T4 = NonNullable<string | number | undefined | null>; // string | number
    // Implementation (conceptual):
    // type NonNullable<T> = T extends null | undefined ? never : T;
    // More robustly: type NonNullable<T> = T & {};
    ```
*   **`Parameters<Type>`:** Constructs a tuple type from the types used in the parameters of a function type `Type`.
    ```typescript
    declare function f1(arg: { a: number; b: string }): void;
    type T5 = Parameters<(s: string) => void>; // [s: string]
    type T6 = Parameters<typeof f1>; // [arg: { a: number; b: string }]
    type T7 = Parameters<<T>(arg: T) => T>; // [arg: unknown]
    // Implementation uses 'infer', discussed later.
    ```
*   **`ConstructorParameters<Type>`:** Constructs a tuple or array type from the types of a constructor function type. Essentially `Parameters<typeof ClassName>` for classes.
    ```typescript
    class MyClass {
      constructor(a: number, b: string) {}
    }
    type T8 = ConstructorParameters<typeof MyClass>; // [a: number, b: string]
    type T9 = ConstructorParameters<ErrorConstructor>; // [message?: string]
    ```
*   **`ReturnType<Type>`:** Constructs a type consisting of the return type of function `Type`.
    ```typescript
    type T10 = ReturnType<() => string>; // string
    type T11 = ReturnType<typeof Math.random>; // number
    ```
*   **`InstanceType<Type>`:** Constructs a type consisting of the instance type of a constructor function type `Type`.
    ```typescript
    class C { x = 0; y = 0; }
    type T12 = InstanceType<typeof C>; // C (the class instance type)
    type T13 = InstanceType<any>; // any
    type T14 = InstanceType<never>; // never
    // type T15 = InstanceType<string>; // Error: 'string' does not satisfy the constraint 'abstract new (...args: any) => any'.
    ```

## 4. String Manipulation Types <a name="string-manipulation-types"></a>
TypeScript 4.1 introduced utility types for string manipulation in type positions.
*   **`Uppercase<StringType>`:** Converts each character in the string to its uppercase equivalent.
*   **`Lowercase<StringType>`:** Converts each character in the string to its lowercase equivalent.
*   **`Capitalize<StringType>`:** Converts the first character in the string to an uppercase equivalent.
*   **`Uncapitalize<StringType>`:** Converts the first character in the string to a lowercase equivalent.

```typescript
_type Greeting = "hello world";
_type Shout = Uppercase<Greeting>; // "HELLO WORLD"
_type Whisper = Lowercase<Shout>; // "hello world"
_type Proper = Capitalize<Whisper>; // "Hello world"
_type Unproper = Uncapitalize<Proper>; // "hello world"
```
These are powerful when combined with template literal types.

## 5. Template Literal Types and Their Power <a name="template-literal-types"></a>
Template literal types allow you to create new string literal types by concatenating existing ones, similar to JavaScript template literals but in type positions.

```typescript
_type World = "World";
_type GreetingTemplate = `Hello ${World}`;
// type GreetingTemplate = "Hello World"

_type EmailLocaleIDs = "welcome_email" | "email_heading";
_type FooterLocaleIDs = "footer_title" | "footer_sendoff";
_type AllLocaleIDs = `${EmailLocaleIDs | FooterLocaleIDs}_id`;
// type AllLocaleIDs = "welcome_email_id" | "email_heading_id" | "footer_title_id" | "footer_sendoff_id"

// Can be used with string manipulation types:
_type Getters<T extends string> = `get${Capitalize<T>}`;
_type Setters<T extends string> = `set${Capitalize<T>}`;

_type NameProp = "name";
_type GetName = Getters<NameProp>; // "getName"
_type SetName = Setters<NameProp>; // "setName"
```

## 6. Understanding the Building Blocks: Conditional Types <a name="conditional-types"></a>
Conditional types select one of two possible types based on a condition expressed as a type relationship test.
*   **Syntax:** `SomeType extends OtherType ? TrueType : FalseType;`

```typescript
interface IdLabel { id: number; /* ... */ }
interface NameLabel { name: string; /* ... */ }

_type LabelType<T> = T extends number ? IdLabel : NameLabel;

_type NumLabel = LabelType<number>;   // IdLabel
_type StrLabel = LabelType<string>;  // NameLabel
_type BoolLabel = LabelType<boolean>; // NameLabel (boolean doesn't extend number)
```

*   **Distributive Conditional Types:** When the type being checked in a conditional type (`SomeType` above) is a naked type parameter (not wrapped in `[]`, `{}`, etc.), the conditional type becomes distributive. This means if `SomeType` is a union, the condition is applied to each member of the union.

```typescript
_type ToArray<Type> = Type extends any ? Type[] : never;
_type StrArrOrNumArr = ToArray<string | number>; // string[] | number[]

// If not distributive (by wrapping Type in a tuple):
_type ToArrayNonDist<Type> = [Type] extends [any] ? Type[] : never;
_type StrOrNumArr = ToArrayNonDist<string | number>; // (string | number)[]
```
This distributive behavior is how `Exclude` and `Extract` work on union types.

## 7. The `infer` Keyword <a name="infer-keyword"></a>
The `infer` keyword allows you to declare a type variable within the `extends` clause of a conditional type. This inferred type variable can then be used in the true branch of the conditional type.

This is the magic behind `Parameters`, `ReturnType`, `InstanceType`, etc.

```typescript
// Conceptual implementation of ReturnType
_type MyReturnType<T extends (...args: any) => any> =
  T extends (...args: any) => infer R ? R : any;

_type MyFunc = () => { a: number; b: string };
_type MyFuncReturn = MyReturnType<MyFunc>; // { a: number; b: string }

// Conceptual implementation of Parameters
_type MyParameters<T extends (...args: any) => any> =
  T extends (...args: infer P) => any ? P : never;

_type MyParams = MyParameters<(name: string, age: number) => void>; // [name: string, age: number]

// Inferring element type of an array
_type Flatten<T> = T extends (infer Item)[] ? Item : T;
_type Str = Flatten<string[]>; // string
_type Num = Flatten<number>;   // number
```

## 8. Creating Custom Utility Types <a name="creating-custom-utility-types"></a>
With mapped types, conditional types, and `infer`, you can create powerful custom utility types.

*   **Example: `Writable<Type>`** (Opposite of `Readonly`)
    ```typescript
    type Writable<T> = {
      -readonly [P in keyof T]: T[P];
    };

    interface User {
      readonly id: number;
      name: string;
    }
    type MutableUser = Writable<User>; // { id: number; name: string; }
    ```

*   **Example: `DeepPartial<Type>`** (Recursive Utility Type)
    ```typescript
    type DeepPartial<T> = T extends Function
      ? T
      : T extends object
      ? { [P in keyof T]?: DeepPartial<T[P]> }
      : T;

    interface Profile {
      id: number;
      details: {
        name: string;
        age: number;
        address: {
          street: string;
          city: string;
        };
      };
    }
    type PartialProfile = DeepPartial<Profile>;
    /*
    PartialProfile = {
      id?: number | undefined;
      details?: { 
        name?: string | undefined;
        age?: number | undefined;
        address?: { 
          street?: string | undefined;
          city?: string | undefined; 
        } | undefined;
      } | undefined;
    }
    */
    ```

*   **Example: `PromiseType<P>`** (Extracting type from a Promise)
    ```typescript
    type PromiseType<P extends Promise<any>> =
      P extends Promise<infer T> ? T : never;

    type Result = PromiseType<Promise<string>>; // string
    ```

*   **Example: `Getters<Type>`** (Mapping object properties to getter functions)
    ```typescript
    type Getters<Type> = {
      [Property in keyof Type as `get${Capitalize<string & Property>}`]: () => Type[Property]
    };

    interface Person {
      name: string;
      age: number;
    }
    type PersonGetters = Getters<Person>;
    // type PersonGetters = {
    //   getName: () => string;
    //   getAge: () => number;
    // }
    ```

## 9. Mapped Types Revisited <a name="mapped-types-revisited"></a>
Mapped types (`[P in keyof T]`) are fundamental.
*   **Mapping Modifiers:** Add or remove `readonly` and `?` using `+` or `-` prefixes.
    *   `+readonly`, `-readonly`, `+?`, `-?`
*   **Key Remapping via `as`:** Change the names of keys in a mapped type.
    ```typescript
    type EventConfig<Events extends { kind: string }> = {
      [E in Events as `${E["kind"]}Changed`]: (event: E) => void;
    }
    type SquareEvent = { kind: "square", x: number, y: number };
    type CircleEvent = { kind: "circle", radius: number };
    type Config = EventConfig<SquareEvent | CircleEvent>
    // type Config = {
    //   squareChanged: (event: SquareEvent) => void;
    //   circleChanged: (event: CircleEvent) => void;
    // }
    ```

## 10. Advanced Patterns and Use Cases <a name="advanced-patterns-use-cases"></a>
*   **Type Guards from Utility Types:** Some utility types (like `Extract` with a specific member) can narrow types similarly to type guards.
*   **Transforming Object Keys:** As seen with `Getters<Type>` or renaming keys based on conditions.
*   **Creating Discriminated Unions more safely:** Utility types can help build parts of discriminated unions or ensure their correctness.

## 11. Limitations and Considerations <a name="limitations-considerations-utility-types"></a>
*   **Complexity:** Advanced utility types can become very complex and hard to debug.
*   **Compiler Performance:** Highly recursive or complex types can sometimes impact TypeScript compiler performance.
*   **Readability:** Strive for clarity. Sometimes a simpler, slightly more verbose type is better than an overly clever one-liner.

## 12. Key Takeaways <a name="key-takeaways-utility-types-deep"></a>
*   Utility types are essential for flexible and robust type manipulation in TypeScript.
*   Built-in utility types cover a wide range of common transformations.
*   **Conditional Types (`extends ? :`)** and the **`infer` keyword** are the core building blocks for many utility types and for creating custom ones.
*   **Mapped Types** with key remapping (`as`) and modifiers (`readonly`, `?`) provide powerful ways to reshape object types.
*   **Template Literal Types** and **String Manipulation Types** extend type transformations to string patterns.
*   Custom utility types allow you to encapsulate complex type logic, making your codebase more maintainable and type-safe.

Mastering utility types significantly enhances your ability to model complex data structures and APIs accurately in TypeScript.

---

⬅️ [Back to TypeScript Decorators (Deep Dive)](../21-ts-decorators-deep-dive/README.md) | ➡️ [Next: TypeScript Advanced Control Flow Analysis](../23-ts-advanced-control-flow/README.md) *(Placeholder)*
