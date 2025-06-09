# 31. Advanced TypeScript Patterns and Idioms

Beyond the fundamentals, TypeScript offers a rich type system that enables powerful patterns and idioms. Mastering these can lead to more robust, maintainable, and expressive code. This tutorial dives into some advanced techniques used by experienced TypeScript developers.

## Table of Contents
1.  [Introduction to Advanced Patterns](#intro-advanced-patterns)
    *   Why learn advanced patterns? (Type safety, expressiveness, maintainability)
2.  [Simulating Nominal Typing: Branding/Tagging](#nominal-typing-branding)
    *   The problem: Structural typing limitations
    *   The branding/tagging pattern using intersections and unique symbols or string literals
    *   Use cases: Distinguishing between structurally identical types (e.g., `UserId` vs `ProductId`)
3.  [Advanced Conditional Types](#advanced-conditional-types)
    *   Distributive conditional types revisited
    *   Conditional types with `infer` for complex type extraction
    *   Recursive conditional types
    *   Use cases: Type manipulation, creating complex utility types
4.  [Variadic Tuple Types](#variadic-tuple-types)
    *   Working with tuples of unknown length (`...T[]` in tuple declarations)
    *   Spreading elements in tuple types
    *   Use cases: Typing functions like `concat`, `curry`, `pipe`
5.  [Template Literal Types - Advanced Usage](#template-literal-types-advanced)
    *   Generating complex string union types
    *   Parsing and manipulating string literal types
    *   Combining with conditional types and `infer`
    *   Use cases: Type-safe event names, CSS-in-JS, path parameters
6.  [Recursive Types and Self-Referential Types](#recursive-types)
    *   Defining types that refer to themselves (e.g., JSON-like structures, linked lists, trees)
    *   Limitations and considerations (e.g., type instantiation depth)
7.  [Mapped Types - Advanced Modifiers and Key Remapping](#mapped-types-advanced)
    *   Recap: `readonly`, `?`
    *   Adding/Removing modifiers: `+readonly`, `-readonly`, `+?`, `-?`
    *   Key Remapping via `as`
    *   Use cases: Creating variants of existing types (e.g., `Immutable<T>`, `Mutable<T>`, `Getter<T>`)
8.  [The `infer` Keyword - Deeper Dive](#infer-keyword-deeper-dive)
    *   Inferring types from multiple positions
    *   Inferring from nested structures and generic types
    *   Combining `infer` with other advanced types for powerful type utilities
9.  [Type-Safe Builders and Fluent Interfaces](#type-safe-builders-fluent-interfaces)
    *   Designing APIs that guide users through valid state transitions at compile time
    *   Using generics and conditional types to enforce builder step order
10. [Stateful Type Guards and Assertion Functions Revisited](#stateful-type-guards-assertion-functions)
    *   More complex scenarios for narrowing types based on runtime checks
    *   Ensuring correctness in stateful objects or complex control flow
11. [The `satisfies` Operator](#satisfies-operator)
    *   Validating that an expression matches some type, without changing the type of the expression itself
    *   Use cases: Ensuring an object conforms to a type while preserving its literal type, type-checking configurations
12. [Phantom Types](#phantom-types)
    *   Using generic type parameters that don't affect the runtime structure but add type-level information
    *   Use cases: Enforcing units of measure, state machines, preventing misuse of APIs
13. [Discriminated Unions on Steroids](#discriminated-unions-advanced)
    *   Combining with mapped types and conditional types for exhaustive checks and type manipulation based on the discriminant.
14. [Design Patterns in TypeScript with Type Safety](#design-patterns-ts)
    *   Implementing classic design patterns (Singleton, Factory, Observer, Strategy, etc.) with enhanced type safety.
15. [Common Anti-Patterns and How to Avoid Them](#common-anti-patterns-ts)
    *   Overuse of `any` or `unknown` without proper narrowing
    *   Overly complex or unreadable types
    *   Ignoring compiler errors or using `@ts-ignore` excessively
    *   Premature abstraction with generics
16. [Key Takeaways](#key-takeaways-advanced-patterns)

## 1. Introduction to Advanced Patterns <a name="intro-advanced-patterns"></a>
Advanced TypeScript patterns leverage the full power of the type system to create highly expressive, robust, and maintainable code. They often involve combining multiple TypeScript features (generics, conditional types, mapped types, `infer`, etc.) to solve complex typing challenges, improve API design, and catch more errors at compile time.

## 2. Simulating Nominal Typing: Branding/Tagging <a name="nominal-typing-branding"></a>
TypeScript has a structural type system. Sometimes, you need to distinguish types that have the same structure but represent different concepts (e.g., `UserId` and `PostId` might both be `string` or `number`).

```typescript
// Branding with a unique symbol (or string literal)
type Brand<K, T> = K & { __brand: T };

type UserId = Brand<string, "UserId">;
type PostId = Brand<string, "PostId">;

function getUserId(id: string): UserId {
  return id as UserId; // Type assertion needed at creation
}

function getPostId(id: string): PostId {
  return id as PostId;
}

let userId = getUserId("user-123");
let postId = getPostId("post-456");

// userId = postId; // Error: Type 'PostId' is not assignable to type 'UserId'.
// postId = userId; // Error: Type 'UserId' is not assignable to type 'PostId'.

function processUserId(id: UserId) {
  console.log("Processing user:", id);
}

processUserId(userId);
// processUserId(postId); // Error! Argument of type 'PostId' is not assignable to parameter of type 'UserId'.
```
**Benefits:** Prevents accidental mixing of semantically different but structurally identical types.

## 3. Advanced Conditional Types <a name="advanced-conditional-types"></a>
Conditional types become even more powerful when used recursively or with complex `infer` patterns.

```typescript
// Example: Flatten a deeply nested array type
type DeepArray<T> = Array<T | DeepArray<T>>;
type Flatten<T extends DeepArray<any>> = 
  T extends Array<infer E> 
    ? E extends DeepArray<any> 
      ? Flatten<E> 
      : E 
    : T;

type Nested = [1, [2, [3, [4]]], 5];
type Flat = Flatten<Nested>; // type Flat = 1 | 2 | 3 | 4 | 5 (if simplified, or a union of array elements)
// A more accurate Flatten for array elements:
type FlattenArrayElements<T> =
    T extends Array<infer Item>
        ? Item extends Array<any>
            ? FlattenArrayElements<Item>
            : Item
        : T;

type MyNestedArray = ["a", ["b", ["c"]], "d"];
type MyFlattenedElements = FlattenArrayElements<MyNestedArray>; // "a" | "b" | "c" | "d"
```

## 4. Variadic Tuple Types <a name="variadic-tuple-types"></a>
Allow typing functions that operate on tuples with a variable number of elements or specific leading/trailing elements.

```typescript
// Example: Typing a function that adds a prefix to a tuple
function addPrefix<T extends any[], P>(prefix: P, ...args: T): [P, ...T] {
  return [prefix, ...args];
}

const result = addPrefix("prefix", 1, "two", true);
// const result: [string, number, string, boolean]

// Example: Typing a `tail` function
type Tail<T extends any[]> = T extends [any, ...infer Rest] ? Rest : [];

type MyTuple = [1, 2, 3, 4];
type MyTupleTail = Tail<MyTuple>; // [2, 3, 4]
```

## 5. Template Literal Types - Advanced Usage <a name="template-literal-types-advanced"></a>
Combine with generics and conditional types for powerful string manipulation at the type level.

```typescript
// Example: Type-safe event names with namespaces
type EventNamespace = "user" | "product" | "order";
type EventAction = "created" | "updated" | "deleted";

type EventName<N extends EventNamespace, A extends EventAction> = `${N}:${A}`;

type UserEvent = EventName<"user", EventAction>; // "user:created" | "user:updated" | "user:deleted"

function emitEvent(event: UserEvent) {
  console.log("Emitting:", event);
}

emitEvent("user:created");
// emitEvent("product:created"); // Error!

// Example: Extracting parts from a string literal type
type ExtractRouteParams<Path extends string> =
  Path extends `${string}/:${infer Param}/${infer Rest}`
    ? { [K in Param | keyof ExtractRouteParams<`/${Rest}`>]: string }
    : Path extends `${string}/:${infer Param}`
      ? { [K in Param]: string }
      : {};

type Route1Params = ExtractRouteParams<"/users/:userId/posts/:postId">;
// type Route1Params = { userId: string; postId: string; }
```

## 6. Recursive Types and Self-Referential Types <a name="recursive-types"></a>
Types that can refer to themselves, useful for tree-like structures or JSON.

```typescript
interface TreeNode<T> {
  value: T;
  children?: Array<TreeNode<T>>;
}

const tree: TreeNode<string> = {
  value: "root",
  children: [
    { value: "child1" },
    { value: "child2", children: [{ value: "grandchild" }] },
  ],
};

// JSON-like structure
type JsonValue = 
  | string 
  | number 
  | boolean 
  | null 
  | JsonValue[] 
  | { [key: string]: JsonValue };

const myJson: JsonValue = {
  name: "Test",
  version: 1.0,
  active: true,
  tags: ["a", "b"],
  config: { nested: { value: null } },
};
```

## 7. Mapped Types - Advanced Modifiers and Key Remapping <a name="mapped-types-advanced"></a>
Fine-grained control over properties when transforming types.

```typescript
interface UserProfile {
  id: number;
  username: string;
  bio?: string;
  readonly email: string;
}

// Make all properties writable and optional
type MutableAndOptional<T> = {
  -readonly [P in keyof T]-?: T[P];
};
type FlexibleUserProfile = MutableAndOptional<UserProfile>;
/*
FlexibleUserProfile = {
  id: number;
  username: string;
  bio: string;
  email: string;
}
*/

// Create getter methods for an interface
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K]
};

type UserProfileGetters = Getters<UserProfile>;
/*
UserProfileGetters = {
  getId: () => number;
  getUsername: () => string;
  getBio: () => string | undefined;
  getEmail: () => string;
}
*/
```

## 8. The `infer` Keyword - Deeper Dive <a name="infer-keyword-deeper-dive"></a>
`infer` can be used in multiple positions and in complex conditional types to deconstruct and capture parts of types.

```typescript
// Get the return type of a function that returns a promise
type PromisedReturnType<T> = T extends (...args: any[]) => Promise<infer R> ? R : never;

async function fetchData(): Promise<{ data: string; count: number }> {
  return { data: "hello", count: 1 };
}

type FetchResult = PromisedReturnType<typeof fetchData>; // { data: string; count: number }

// Inferring multiple types from a string pattern
type ParsePath<Path extends string> =
  Path extends `${infer Segment}/${infer Rest}`
    ? [Segment, ...ParsePath<Rest>]
    : [Path];

type Parsed = ParsePath<"users/details/profile">; // ["users", "details", "profile"]
```

## 9. Type-Safe Builders and Fluent Interfaces <a name="type-safe-builders-fluent-interfaces"></a>
Guide the user through a series of steps to construct an object, ensuring validity at each step.

```typescript
class QueryBuilder<Model> {
  private conditions: string[] = [];

  select<K extends keyof Model>(...fields: K[]): Omit<this, 'select'> & { _selected: Pick<Model, K> } {
    this.conditions.push(`SELECT ${fields.join(', ')}`);
    return this as any; // Assertion for chaining
  }

  from(table: string): Omit<this, 'from' | 'select'> {
    this.conditions.push(`FROM ${table}`);
    return this as any;
  }

  where(condition: string): Omit<this, 'where' | 'from' | 'select'> {
    this.conditions.push(`WHERE ${condition}`);
    return this as any;
  }

  build(): string {
    return this.conditions.join(' ') + ';';
  }
}

interface User { id: number; name: string; email: string; }

const query =
  new QueryBuilder<User>()
    .select('id', 'name') // Now 'select' is not available
    .from('users')        // Now 'from' and 'select' are not available
    .where('id = 1')      // Now 'where', 'from', 'select' are not available
    .build();

console.log(query); // "SELECT id, name FROM users WHERE id = 1;"
// new QueryBuilder<User>().from("users").select("id"); // Error: Property 'select' does not exist
```
This uses `Omit` to remove methods as they are called, guiding the build process.

## 10. Stateful Type Guards and Assertion Functions Revisited <a name="stateful-type-guards-assertion-functions"></a>
For more complex scenarios where the type of an object changes based on its internal state or operations performed on it.

```typescript
interface RequestState {
  state: 'pending' | 'success' | 'error';
  data?: string;
  error?: Error;
}

function assertIsSuccess(req: RequestState): asserts req is RequestState & { state: 'success'; data: string } {
  if (req.state !== 'success' || typeof req.data !== 'string') {
    throw new Error('Request is not in success state or data is missing.');
  }
}

const myRequest: RequestState = { state: 'pending' };

function processRequest(req: RequestState) {
  // Simulate API call
  setTimeout(() => {
    req.state = 'success';
    req.data = 'Payload received!';

    assertIsSuccess(req); // Now req is narrowed
    console.log(req.data.toUpperCase()); // Safe to access data
  }, 1000);
}
processRequest(myRequest);
```

## 11. The `satisfies` Operator <a name="satisfies-operator"></a>
Introduced in TypeScript 4.9, `satisfies` allows you to validate that an expression conforms to a type without changing the inferred type of the expression. This is useful for ensuring an object meets a contract while still retaining its more specific literal type.

```typescript
interface Config {
  endpoints: Record<string, { url: string; timeout?: number }>;
  logLevel: 'debug' | 'info' | 'warn' | 'error';
}

const myAppConfig = {
  endpoints: {
    users: { url: '/api/users', timeout: 5000 },
    products: { url: '/api/products' }, // timeout is optional
  },
  logLevel: 'info',
  // customProperty: true // This would be an error if we used 'as Config'
} satisfies Config;

// myAppConfig retains its specific literal type
const usersTimeout = myAppConfig.endpoints.users.timeout; // number | undefined (actually 5000)
const productsUrl = myAppConfig.endpoints.products.url; // string (actually '/api/products')

// If we used 'as Config':
// const myAppConfigAs = { ... } as Config;
// const usersTimeoutAs = myAppConfigAs.endpoints.users.timeout; // Type is just 'number | undefined'
```

## 12. Phantom Types <a name="phantom-types"></a>
Use a generic parameter that isn't used in the structure of the type but exists only for type-checking purposes.

```typescript
type Kilograms = number & { _unit: 'kg' };
type Pounds = number & { _unit: 'lb' };

function createKg(value: number): Kilograms {
  return value as Kilograms;
}

function createLb(value: number): Pounds {
  return value as Pounds;
}

const weightInKg = createKg(70);
const weightInLb = createLb(154);

function addWeightsKg(w1: Kilograms, w2: Kilograms): Kilograms {
  return (w1 + w2) as Kilograms;
}

addWeightsKg(weightInKg, createKg(5));
// addWeightsKg(weightInKg, weightInLb); // Error! Argument of type 'Pounds' is not assignable to 'Kilograms'.
```
This is similar to branding but often used for units, states, or other metadata.

## 13. Discriminated Unions on Steroids <a name="discriminated-unions-advanced"></a>
Combine discriminated unions with mapped and conditional types for powerful and type-safe state management or event handling.

```typescript
type Action =
  | { type: 'FETCH_USER_REQUEST'; userId: string }
  | { type: 'FETCH_USER_SUCCESS'; userId: string; userData: { name: string; email: string } }
  | { type: 'FETCH_USER_FAILURE'; userId: string; error: string };

// Utility to extract an action by its type
type ActionByType<A, T extends string> = A extends { type: T } ? A : never;

type FetchUserSuccessAction = ActionByType<Action, 'FETCH_USER_SUCCESS'>;
// FetchUserSuccessAction = { type: 'FETCH_USER_SUCCESS'; userId: string; userData: { ... }; }

// Create a map of action types to their payloads
type ActionPayloadMap = {
  [A in Action as A['type']]: Omit<A, 'type'>;
};
/*
ActionPayloadMap = {
  FETCH_USER_REQUEST: { userId: string; };
  FETCH_USER_SUCCESS: { userId: string; userData: { ... }; };
  FETCH_USER_FAILURE: { userId: string; error: string; };
}
*/

function handleAction(action: Action) {
  switch (action.type) {
    case 'FETCH_USER_SUCCESS':
      console.log(action.userData.name); // Typed correctly
      break;
    // ... other cases
  }
}
```

## 14. Design Patterns in TypeScript with Type Safety <a name="design-patterns-ts"></a>
TypeScript's type system can enhance classic design patterns:
*   **Factory Pattern:** Use generics and conditional types to return specific types based on input.
*   **Singleton Pattern:** Ensure only one instance with private constructors and static accessors.
*   **Observer Pattern:** Typed event payloads and listener signatures.
*   **Strategy Pattern:** Define strategy interfaces and ensure concrete strategies implement them correctly.

## 15. Common Anti-Patterns and How to Avoid Them <a name="common-anti-patterns-ts"></a>
*   **Overusing `any`:** Defeats the purpose of TypeScript. Use `unknown` and narrow, or provide more specific types.
*   **Excessive `@ts-ignore` or `@ts-expect-error`:** Indicates underlying type issues that should be addressed.
*   **Overly Complex Types:** Strive for readability. If a type becomes too convoluted, consider breaking it down or rethinking the approach.
*   **Premature Abstraction with Generics:** Don't use generics unless there's a clear need for reusable, type-agnostic logic.
*   **Type Assertions (`as Type`) Everywhere:** Can hide type errors. Prefer type guards or safer type manipulations.

## 16. Key Takeaways <a name="key-takeaways-advanced-patterns"></a>
*   Advanced TypeScript patterns combine core features to solve complex typing problems elegantly.
*   Techniques like branding, advanced conditional/mapped types, `infer`, and variadic tuples provide fine-grained control over types.
*   The `satisfies` operator is a valuable tool for validating types while preserving literal type information.
*   These patterns enhance type safety, API design, and code maintainability, leading to more robust applications.
*   Strive for a balance between type safety and type complexity; readability is key.

Mastering these patterns will significantly elevate your TypeScript skills, allowing you to tackle more sophisticated development challenges with confidence.

---

⬅️ [Back to TypeScript with GraphQL (Client and Server)](../30-ts-with-graphql/README.md) | ➡️ [Next: TypeScript Performance Optimization Techniques](../32-ts-performance-optimization/README.md) *(Placeholder)*
