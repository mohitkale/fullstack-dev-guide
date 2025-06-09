# 49. Advanced TypeScript Patterns and Best Practices

This tutorial consolidates and expands on advanced TypeScript concepts, patterns, and best practices to help you write truly idiomatic, robust, scalable, and maintainable TypeScript code. It assumes familiarity with core TypeScript features and previous tutorials in this series.

## Table of Contents
1.  [Introduction: Elevating Your TypeScript Skills](#intro-elevating-ts)
2.  [Mastering the Type System](#mastering-type-system-adv)
    *   [Conditional Types: Advanced Applications](#conditional-types-adv)
    *   [Mapped Types: Beyond Basic Transformations](#mapped-types-adv)
    *   [Recursive and Self-Referential Types](#recursive-types-adv)
    *   [Variadic Tuple Types in Practice](#variadic-tuples-adv)
    *   [Nominal Typing Simulation (Branding/Tagging) Revisited](#nominal-typing-adv)
    *   [Phantom Types for Enhanced Compile-Time Guarantees](#phantom-types-adv)
    *   [The `satisfies` Operator: Practical Use Cases](#satisfies-operator-adv)
3.  [Advanced Design Patterns in TypeScript](#advanced-design-patterns-ts)
    *   [Type-Safe Event Emitters/PubSub Systems](#type-safe-events-adv)
    *   [Robust State Machines with Discriminated Unions](#state-machines-adv)
    *   [Fluent Builder Pattern with Enhanced Type Safety](#builder-pattern-adv)
    *   [Dependency Injection (DI) Strategies](#di-strategies-adv)
    *   [Policy/Strategy Pattern with Type-Driven Logic](#policy-strategy-adv)
4.  [Sophisticated Asynchronous Programming Patterns](#async-patterns-adv)
    *   [Advanced Error Handling: `Result` Types and Railway Oriented Programming](#async-error-handling-adv)
    *   [Type-Safe API Client Design Patterns](#api-client-patterns-adv)
    *   [Managing Concurrency with Type Safety](#concurrency-patterns-adv)
5.  [Code Architecture and Maintainability](#code-architecture-adv)
    *   [Effective Module Design: Cohesion and Coupling](#module-design-adv)
    *   [Strategic Use of `tsconfig.json` for Scalability](#tsconfig-scalability-adv)
    *   [Writing Highly Testable TypeScript Code](#testable-code-adv)
    *   [Comprehensive Documentation with TSDoc](#tsdoc-adv)
6.  [Performance Considerations in TypeScript](#performance-ts-adv)
    *   [Understanding Type-Checking Impact](#type-checking-perf-adv)
    *   [Optimizing Build Times (`tsc`, bundlers)](#build-time-optimization-adv)
    *   [Runtime Performance Best Practices](#runtime-perf-adv)
7.  [Consolidated Best Practices Checklist](#best-practices-checklist-adv)
8.  [Staying Current with TypeScript's Evolution](#staying-current-ts-adv)
9.  [Conclusion: TypeScript Craftsmanship](#conclusion-craftsmanship-adv)

## 1. Introduction: Elevating Your TypeScript Skills <a name="intro-elevating-ts"></a>
TypeScript offers a powerful type system that, when used effectively, can dramatically improve code quality and developer productivity. This tutorial aims to move beyond intermediate usage to explore advanced techniques that unlock TypeScript's full potential for building complex applications.

## 2. Mastering the Type System <a name="mastering-type-system-adv"></a>

### Conditional Types: Advanced Applications <a name="conditional-types-adv"></a>
(Building on Tutorial #13 and #31)
Conditional types (`T extends U ? X : Y`) can be used for more than simple type selection. Combined with `infer`, they become powerful tools for type manipulation and inference.

**Example: Extracting function parameter types or return types, even for overloaded functions.**
```typescript
type Unpacked<T> = T extends (infer U)[] ? U :
                     T extends (...args: any[]) => infer R ? R :
                     T extends Promise<infer P> ? P :
                     T;

type T0 = Unpacked<string[]>; // string
type T1 = Unpacked<() => string>; // string
type T2 = Unpacked<Promise<string>>; // string
type T3 = Unpacked<Promise<string>[]>; // Promise<string>
type T4 = Unpacked<Unpacked<Promise<string>[]>>; // string
```

### Mapped Types: Beyond Basic Transformations <a name="mapped-types-adv"></a>
(Building on Tutorial #12 and #31)
Combine mapped types with conditional types, key remapping, and template literal types for sophisticated transformations.

**Example: Creating a type with only writable properties.**
```typescript
type WritableKeys<T> = {
  [P in keyof T]-?: IfEquals<
    { [Q in P]: T[P] },
    { -readonly [Q in P]: T[P] },
    P,
    never
  >
}[keyof T];

type IfEquals<X, Y, A = X, B = never> = (<T>() => T extends X ? 1 : 2) extends
  (<T>() => T extends Y ? 1 : 2) ? A : B;

type Writable<T> = Pick<T, WritableKeys<T>>;

interface UserProfile {
  readonly id: string;
  name: string;
  readonly email: string;
  age?: number;
}

type WritableUserProfile = Writable<UserProfile>; // { name: string; age?: number | undefined; }
```

### Recursive and Self-Referential Types <a name="recursive-types-adv"></a>
Essential for modeling tree-like structures, JSON objects, or linked lists.
```typescript
// Example: A JSON-like type
type JsonValue = 
  | string 
  | number 
  | boolean 
  | null 
  | JsonValue[] 
  | { [key: string]: JsonValue };

const jsonData: JsonValue = {
  name: "Test",
  version: 1.0,
  settings: {
    enabled: true,
    nodes: [1, { type: "leaf" }]
  }
};
```

### Nominal Typing Simulation (Branding/Tagging) Revisited <a name="nominal-typing-adv"></a>
(Building on Tutorial #31)
Ensure that types representing different concepts are not accidentally interchangeable, even if their structure is the same.
```typescript
type UserId = string & { __brand: 'UserId' };
type ProductId = string & { __brand: 'ProductId' };

function createUserId(id: string): UserId {
  return id as UserId;
}

function createProductId(id: string): ProductId {
  return id as ProductId;
}

const userId = createUserId("user-123");
const productId = createProductId("prod-456");

// function processItem(id: UserId) { /* ... */ }
// processItem(productId); // Error: Type 'ProductId' is not assignable to type 'UserId'.
```

## 3. Advanced Design Patterns in TypeScript <a name="advanced-design-patterns-ts"></a>

### Type-Safe Event Emitters/PubSub Systems <a name="type-safe-events-adv"></a>
Use generics and mapped types to create event emitters where event names and payload types are strictly checked.
```typescript
type EventMap = {
  'userLoggedIn': { userId: string; timestamp: Date };
  'itemAddedToCart': { itemId: string; quantity: number };
  'error': Error;
};

class TypedEventEmitter<Events extends Record<string, any>> {
  private listeners: { [K in keyof Events]?: Array<(payload: Events[K]) => void> } = {};

  on<K extends keyof Events>(event: K, callback: (payload: Events[K]) => void): void {
    this.listeners[event] = (this.listeners[event] || []) as any;
    this.listeners[event]?.push(callback);
  }

  emit<K extends keyof Events>(event: K, payload: Events[K]): void {
    this.listeners[event]?.forEach(callback => callback(payload));
  }
}

const appEvents = new TypedEventEmitter<EventMap>();
appEvents.on('userLoggedIn', (payload) => {
  console.log(payload.userId, payload.timestamp); // payload is { userId: string; timestamp: Date }
});
// appEvents.on('userLoggedIn', (payload: {id: string}) => {}); // Error
appEvents.emit('itemAddedToCart', { itemId: 'item-1', quantity: 2 });
// appEvents.emit('itemAddedToCart', { itemId: 'item-1' }); // Error: quantity missing
```

### Robust State Machines with Discriminated Unions <a name="state-machines-adv"></a>
(Building on Tutorial #11)
Model complex states and transitions with exhaustive checking.
```typescript
type IdleState = { status: 'idle' };
type LoadingState = { status: 'loading'; startTime: number };
type SuccessState<T> = { status: 'success'; data: T };
type ErrorState = { status: 'error'; error: Error };

type AppState<T> = IdleState | LoadingState | SuccessState<T> | ErrorState;

function handleState<T>(state: AppState<T>): void {
  switch (state.status) {
    case 'idle': console.log('Idle...'); break;
    case 'loading': console.log(`Loading since ${state.startTime}`); break;
    case 'success': console.log('Data:', state.data); break;
    case 'error': console.error('Error:', state.error.message); break;
    default: // Exhaustiveness check - TypeScript will error if a state is missed
      const _exhaustiveCheck: never = state;
      return _exhaustiveCheck;
  }
}
```

## 4. Sophisticated Asynchronous Programming Patterns <a name="async-patterns-adv"></a>

### Advanced Error Handling: `Result` Types <a name="async-error-handling-adv"></a>
Represent success or failure explicitly, avoiding reliance on exceptions for control flow.
```typescript
type Success<T> = { type: 'success'; value: T };
type Failure<E> = { type: 'failure'; error: E };
type Result<T, E> = Success<T> | Failure<E>;

async function fetchData(url: string): Promise<Result<string, Error>> {
  try {
    const response = await fetch(url);
    if (!response.ok) {
      return { type: 'failure', error: new Error(`HTTP error! status: ${response.status}`) };
    }
    const data = await response.text();
    return { type: 'success', value: data };
  } catch (error) {
    return { type: 'failure', error: error instanceof Error ? error : new Error(String(error)) };
  }
}

async function process() {
  const result = await fetchData('/api/data');
  if (result.type === 'success') {
    console.log('Data:', result.value);
  } else {
    console.error('Failed:', result.error.message);
  }
}
```

## 5. Code Architecture and Maintainability <a name="code-architecture-adv"></a>

### Effective Module Design: Cohesion and Coupling <a name="module-design-adv"></a>
*   **High Cohesion**: Group related functionality within a module.
*   **Low Coupling**: Minimize dependencies between modules. Use interfaces and dependency inversion.
*   **Barrel Files (`index.ts`)**: Use judiciously. They can simplify imports but also increase bundle sizes if not tree-shaken properly and can sometimes obscure dependencies.

### Writing Highly Testable TypeScript Code <a name="testable-code-adv"></a>
*   **Dependency Injection**: Makes mocking dependencies straightforward.
*   **Pure Functions**: Easier to test as they have no side effects.
*   **Interfaces for Abstractions**: Mock interfaces rather than concrete implementations.
*   Use testing frameworks like Jest, Vitest, or Mocha with TypeScript support (`ts-jest`, native support in Vitest).

## 7. Consolidated Best Practices Checklist <a name="best-practices-checklist-adv"></a>
*   **Enable `strict` mode** in `tsconfig.json` and address all resulting errors.
*   **Avoid `any` whenever possible**. Use `unknown` for values whose type is truly unknown and perform type checking/assertion.
*   **Use `readonly`** for properties and `Readonly<T>` / `ReadonlyArray<T>` for objects/arrays that should not be mutated.
*   **Prefer specific types** over primitive types for domain concepts (e.g., `UserId` instead of `string`).
*   **Leverage utility types** (`Partial`, `Pick`, `Omit`, `Record`, etc.) to avoid manual type duplication.
*   **Favor composition over inheritance** for more flexible and maintainable code.
*   **Use discriminated unions** for modeling states or variants.
*   **Write clear TSDoc comments** for public APIs.
*   **Keep functions small and focused** (Single Responsibility Principle).
*   **Ensure consistent code formatting** using Prettier and ESLint.
*   **Write comprehensive tests** for your types and logic.

## 9. Conclusion: TypeScript Craftsmanship <a name="conclusion-craftsmanship-adv"></a>
Mastering advanced TypeScript involves not just understanding its features but also developing an intuition for how to apply them effectively to build clean, robust, and maintainable software. Continuous learning, exploring community patterns, and practicing these techniques will elevate your TypeScript craftsmanship.

---

⬅️ [Back to TypeScript for Full-Stack Development](../48-ts-fullstack-dev/README.md) | ➡️ [Next: TypeScript and Internationalization (i18n)](../50-ts-i18n/README.md) *(Placeholder)*
