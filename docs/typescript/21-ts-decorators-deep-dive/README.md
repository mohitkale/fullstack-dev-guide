# 21. TypeScript Decorators (Deep Dive)

In Tutorial #10, we introduced TypeScript Decorators as an experimental feature for metaprogramming. This tutorial takes a deeper dive into decorators, exploring their types, evaluation order, metadata reflection, and practical applications in more detail.

**Reminder:** Decorators are an experimental feature in TypeScript. While widely used in frameworks like Angular, NestJS, and TypeORM, their syntax and behavior could potentially change in future ECMAScript proposals or TypeScript versions. Always check the official TypeScript documentation for the latest status.

## Table of Contents
1.  [Recap: What are Decorators?](#recap-decorators)
    *   Enabling Decorators (`experimentalDecorators`)
2.  [Decorator Types Revisited](#decorator-types-revisited)
    *   [Class Decorators](#class-decorators-deep)
        *   Signature: `(constructor: Function) => Function | void`
        *   Modifying/Replacing Constructors
    *   [Method Decorators](#method-decorators-deep)
        *   Signature: `(target: any, propertyKey: string, descriptor: PropertyDescriptor) => PropertyDescriptor | void`
        *   Observing, Modifying, or Replacing Method Definitions
    *   [Accessor Decorators](#accessor-decorators-deep)
        *   Signature: `(target: any, propertyKey: string, descriptor: PropertyDescriptor) => PropertyDescriptor | void`
        *   Observing, Modifying, or Replacing Accessor Definitions
    *   [Property Decorators](#property-decorators-deep)
        *   Signature: `(target: any, propertyKey: string) => void`
        *   Observing Properties (Cannot directly modify value at definition time)
    *   [Parameter Decorators](#parameter-decorators-deep)
        *   Signature: `(target: any, propertyKey: string | symbol, parameterIndex: number) => void`
        *   Observing Parameters (Cannot directly modify parameters)
3.  [Decorator Evaluation Order (Detailed)](#decorator-evaluation-order-detailed)
    *   Order of Decorator Application (Instance vs. Static)
    *   Order within a Single Declaration
4.  [Decorator Factories Revisited](#decorator-factories-revisited)
5.  [Metadata Reflection with Decorators](#metadata-reflection)
    *   Enabling `emitDecoratorMetadata`
    *   The `Reflect.metadata` Decorator
    *   The `Reflect.getMetadata`, `Reflect.getOwnMetadata` APIs
    *   How TypeScript Emits Type Metadata
    *   Use Cases: Dependency Injection, ORMs, Validation
6.  [Advanced Use Cases and Examples](#advanced-use-cases)
    *   Logging and Auditing
    *   Validation Framework (Conceptual)
    *   Dependency Injection (Conceptual)
    *   Serialization/Deserialization Control
7.  [Limitations and Considerations](#limitations-considerations-deep)
    *   Experimental Nature
    *   `this` Context in Decorators
    *   Interaction with Other TypeScript Features
8.  [Decorators vs. Higher-Order Functions/Components](#decorators-vs-hoc)
9.  [Key Takeaways](#key-takeaways-decorators-deep)

## 1. Recap: What are Decorators? <a name="recap-decorators"></a>
Decorators are special kinds of declarations that can be attached to classes, methods, accessors, properties, or parameters. They use the form `@expression`, where `expression` must evaluate to a function that will be called at runtime with information about the decorated declaration.

### Enabling Decorators (`experimentalDecorators`)
To use decorators, you must enable the `experimentalDecorators` compiler option in your `tsconfig.json`:
```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    // Potentially "emitDecoratorMetadata": true for metadata features
  }
}
```

## 2. Decorator Types Revisited <a name="decorator-types-revisited"></a>
Let's explore each decorator type with more focus on their signatures and capabilities.

### Class Decorators <a name="class-decorators-deep"></a>
Applied to a class declaration. They receive the constructor of the class as their sole argument.
*   **Signature:** `(constructor: Function) => Function | void`
*   **Purpose:** Observe, modify, or replace a class definition.
*   If a class decorator returns a new constructor function, it will replace the original class declaration.

```typescript
function sealed(constructor: Function) {
  Object.seal(constructor);
  Object.seal(constructor.prototype);
  console.log(`Class ${constructor.name} has been sealed.`);
}

function reportableClassDecorator<T extends { new (...args: any[]): {} }>(constructor: T) {
  return class extends constructor {
    reportingURL = "http://www.example.com/report";
    constructor(...args: any[]) {
      super(...args);
      console.log(`New instance of ${constructor.name} created, reporting to ${this.reportingURL}`);
    }
  };
}

@sealed
@reportableClassDecorator
class BugReport {
  type = "report";
  title: string;

  constructor(t: string) {
    this.title = t;
  }
}

const bug = new BugReport("Memory Leak");
// Output:
// New instance of BugReport created, reporting to http://www.example.com/report
// Class BugReport_1 has been sealed. (Note: Name might change due to decorator returning new class)
```

### Method Decorators <a name="method-decorators-deep"></a>
Applied to a method definition within a class.
*   **Signature:** `(target: any, propertyKey: string, descriptor: PropertyDescriptor) => PropertyDescriptor | void`
    *   `target`: For an instance member, this is the prototype of the class. For a static member, this is the constructor function of the class.
    *   `propertyKey`: The name of the method.
    *   `descriptor`: The Property Descriptor for the method (contains `value`, `writable`, `enumerable`, `configurable`).
*   **Purpose:** Observe, modify, or replace a method definition.
*   If a method decorator returns a `PropertyDescriptor`, it will be used to define the method.

```typescript
function enumerable(value: boolean) {
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    descriptor.enumerable = value;
    console.log(`Method ${propertyKey} enumerable set to ${value}`);
  };
}

function logExecution(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;
  descriptor.value = function (...args: any[]) {
    console.log(`Executing ${propertyKey} with args: ${JSON.stringify(args)}`);
    const result = originalMethod.apply(this, args);
    console.log(`Finished ${propertyKey}, result: ${JSON.stringify(result)}`);
    return result;
  };
  return descriptor;
}

class Calculator {
  @enumerable(false)
  @logExecution
  add(a: number, b: number): number {
    return a + b;
  }
}

const calc = new Calculator();
calc.add(5, 3);
// Output:
// Method add enumerable set to false
// Executing add with args: [5,3]
// Finished add, result: 8

for (const key in calc) { console.log(key); } // 'add' won't appear if enumerable is false
```

### Accessor Decorators <a name="accessor-decorators-deep"></a>
Applied to an accessor (`get` or `set`) definition.
*   **Signature:** `(target: any, propertyKey: string, descriptor: PropertyDescriptor) => PropertyDescriptor | void`
    *   Arguments are the same as for method decorators.
    *   The `descriptor` will have `get` and/or `set` functions.
*   **Purpose:** Observe, modify, or replace an accessor's definition.
*   If it returns a value, it's used as the Property Descriptor for the member.
*   **Note:** TypeScript does not allow decorating both `get` and `set` accessors for a single member. If you decorate one, the decorator applies to both implicitly.

```typescript
function configurable(value: boolean) {
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    descriptor.configurable = value;
    console.log(`Accessor ${propertyKey} configurable set to ${value}`);
  };
}

class Point {
  private _x: number = 0;
  private _y: number = 0;

  @configurable(false)
  get x() { return this._x; }
  set x(val: number) { this._x = val; }
}

const p = new Point();
// delete (p as any).x; // This would fail if configurable is false and in strict mode
```

### Property Decorators <a name="property-decorators-deep"></a>
Applied to a property declaration.
*   **Signature:** `(target: any, propertyKey: string | symbol) => void`
    *   `target`: For an instance member, the prototype of the class. For a static member, the constructor function.
    *   `propertyKey`: The name of the property.
*   **Purpose:** Primarily to observe that a property of a certain name has been declared for a class. **It cannot directly modify the property's value at definition time or provide a property descriptor.** The return value is ignored.
*   Often used with `Reflect.metadata` to record type information or attach metadata.

```typescript
function format(formatString: string) {
  return function (target: any, propertyKey: string) {
    // Store metadata. This requires Reflect.metadata or a similar library.
    // For simplicity, we'll just log here.
    console.log(`Property ${propertyKey} should be formatted as ${formatString}`);
    // In a real scenario, you'd use Reflect.defineMetadata(formatMetadataKey, formatString, target, propertyKey);
  };
}

class UserProfile {
  @format("email")
  email: string;

  constructor(email: string) {
    this.email = email;
  }
}
// Output: Property email should be formatted as email
```

### Parameter Decorators <a name="parameter-decorators-deep"></a>
Applied to a parameter declaration in a constructor or method.
*   **Signature:** `(target: any, propertyKey: string | symbol | undefined, parameterIndex: number) => void`
    *   `target`: For an instance member's parameter, the prototype. For a static member's parameter, the constructor. For a constructor parameter, the constructor function.
    *   `propertyKey`: The name of the method or `undefined` for constructor parameters.
    *   `parameterIndex`: The zero-based index of the parameter in the parameter list.
*   **Purpose:** Observe that a parameter has been declared on a method/constructor. **Cannot change the parameter's value or type directly.** The return value is ignored.
*   Commonly used for metadata reflection, often for dependency injection or validation.

```typescript
function required(target: any, propertyKey: string | symbol | undefined, parameterIndex: number) {
  console.log(`Parameter at index ${parameterIndex} of ${propertyKey ? String(propertyKey) : 'constructor'} is required.`);
  // Store metadata about which parameters are required
}

class Order {
  createOrder(@required item: string, quantity: number, @required customerId: string) {
    console.log(`Order created for ${item}, quantity ${quantity}, customer ${customerId}`);
  }
}
// Output:
// Parameter at index 0 of createOrder is required.
// Parameter at index 2 of createOrder is required.
```

## 3. Decorator Evaluation Order (Detailed) <a name="decorator-evaluation-order-detailed"></a>
Decorators are applied in a specific order:
1.  **Parameter Decorators**, then **Method, Accessor, or Property Decorators** are applied for each instance member.
2.  **Parameter Decorators**, then **Method, Accessor, or Property Decorators** are applied for each static member.
3.  **Parameter Decorators** are applied for the constructor.
4.  **Class Decorators** are applied to the class.

Within a single declaration (e.g., multiple decorators on one method):
*   Decorator expressions (the part after `@`) are evaluated top-to-bottom (like function calls).
*   The resulting decorator functions are then called bottom-to-top (as if they were nested).

```typescript
function F(name: string) {
  console.log(`F(): ${name} evaluated`);
  return function (target: any, propertyKey?: string, descriptor?: any) {
    console.log(`F(): ${name} called`);
  };
}

function G(name: string) {
  console.log(`G(): ${name} evaluated`);
  return function (target: any, propertyKey?: string, descriptor?: any) {
    console.log(`G(): ${name} called`);
  };
}

@F("Class")
class ExampleClass {
  @F("Static Method")
  @G("Static Method")
  static staticMethod() {}

  @F("Instance Method")
  @G("Instance Method")
  instanceMethod(@F("Param") param: any) {}
}

// Evaluation order of decorator expressions:
// F(): Class evaluated
// F(): Static Method evaluated
// G(): Static Method evaluated
// F(): Instance Method evaluated
// G(): Instance Method evaluated
// F(): Param evaluated

// Call order of decorator functions:
// G(): Static Method called
// F(): Static Method called
// F(): Param called
// G(): Instance Method called
// F(): Instance Method called
// F(): Class called
```

## 4. Decorator Factories Revisited <a name="decorator-factories-revisited"></a>
A decorator factory is simply a function that returns the decorator function. This allows you to customize the decorator with parameters.

```typescript
function LogLevel(level: string) { // Decorator Factory
  return function(constructor: Function) { // Actual Decorator
    console.log(`[${level.toUpperCase()}] Class ${constructor.name} registered.`);
  }
}

@LogLevel("info")
class MyService {}
// Output: [INFO] Class MyService registered.
```

## 5. Metadata Reflection with Decorators <a name="metadata-reflection"></a>
One of the most powerful uses of decorators is in conjunction with the metadata reflection API. This allows you to attach and retrieve metadata (like type information) at runtime.

### Enabling `emitDecoratorMetadata`
Set this to `true` in your `tsconfig.json`:
```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  }
}
```

### The `Reflect.metadata` Decorator
This requires installing the `reflect-metadata` library:
```bash
npm install reflect-metadata
```
And importing it (usually once at the entry point of your application):
```typescript
import "reflect-metadata";
```
The `Reflect.metadata` function is a decorator factory that attaches metadata.
`Reflect.metadata(metadataKey: any, metadataValue: any)`

### The `Reflect.getMetadata`, `Reflect.getOwnMetadata` APIs
*   `Reflect.getMetadata(metadataKey: any, target: Object, propertyKey?: string | symbol): any;`
*   `Reflect.getOwnMetadata(metadataKey: any, target: Object, propertyKey?: string | symbol): any;`

These retrieve metadata. `getMetadata` looks up the prototype chain, while `getOwnMetadata` only checks the target itself.

### How TypeScript Emits Type Metadata
When `emitDecoratorMetadata` is true, TypeScript automatically emits some type metadata for decorated declarations:
*   For properties and method/accessor parameters: `design:type` (the type of the property/parameter).
*   For method parameters: `design:paramtypes` (an array of parameter types for the method).
*   For methods: `design:returntype` (the return type of the method).

```typescript
import "reflect-metadata";

function showType(target: any, propertyKey: string) {
  const type = Reflect.getMetadata("design:type", target, propertyKey);
  console.log(`${propertyKey} type: ${type.name}`);
}

function showParamTypes(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const paramTypes = Reflect.getMetadata("design:paramtypes", target, propertyKey);
  const formattedParamTypes = paramTypes.map((p: any) => p.name).join(', ');
  console.log(`${propertyKey} param types: [${formattedParamTypes}]`);
}

class Demo {
  @showType
  myProperty: string = "hello";

  @showParamTypes
  myMethod(param1: number, param2: boolean): string {
    return `Received ${param1} and ${param2}`;
  }
}

// Output:
// myProperty type: String
// myMethod param types: [Number, Boolean]
```

### Use Cases: Dependency Injection, ORMs, Validation
*   **Dependency Injection:** Decorators can mark classes/properties for injection, and metadata can determine the type of dependency to inject.
*   **ORMs (Object-Relational Mappers):** Decorators define entities, columns, relationships, and metadata provides types for database mapping.
*   **Validation:** Decorators specify validation rules, and metadata can provide the type to validate against.

## 6. Advanced Use Cases and Examples <a name="advanced-use-cases"></a>

### Logging and Auditing
Method decorators can automatically log method calls, execution time, and arguments.

### Validation Framework (Conceptual)
Property decorators can attach validation rules, and a validation function can use `Reflect.getMetadata` to retrieve these rules and the property types.

```typescript
// Conceptual validation example
import "reflect-metadata";

const MinLengthKey = Symbol("MinLength");

function MinLength(limit: number) {
  return Reflect.metadata(MinLengthKey, limit);
}

class User {
  @MinLength(3)
  @showType // from previous example
  username: string;

  constructor(username: string) {
    this.username = username;
  }
}

function validate(obj: any): boolean {
  for (const key in obj) {
    const type = Reflect.getMetadata("design:type", obj, key);
    const minLength = Reflect.getMetadata(MinLengthKey, obj, key);

    if (type === String && minLength !== undefined) {
      if (obj[key].length < minLength) {
        console.error(`${key} is too short. Min length is ${minLength}, got ${obj[key].length}.`);
        return false;
      }
    }
  }
  console.log("Validation passed for", obj);
  return true;
}

const user1 = new User("Al"); // username type: String
validate(user1); // Error: username is too short. Min length is 3, got 2.

const user2 = new User("Alice");
validate(user2); // Validation passed for User { username: 'Alice' }
```

## 7. Limitations and Considerations <a name="limitations-considerations-deep"></a>
*   **Experimental Nature:** The feature might change.
*   **`this` Context:** Inside a decorator function, `this` does not refer to the instance of the class. For method/accessor decorators, `target` is the prototype (for instance members) or constructor (for static members).
*   **Order of Execution:** Can be complex to reason about with many decorators.
*   **Performance:** Overuse of decorators, especially those doing heavy work or reflection, can impact performance.

## 8. Decorators vs. Higher-Order Functions/Components <a name="decorators-vs-hoc"></a>
*   **Decorators:** Provide a declarative syntax for modifying or annotating declarations at design time. Their primary application is at the class/member definition site.
*   **Higher-Order Functions (HOFs):** Functions that operate on other functions (take functions as arguments or return them). Used for composition, wrapping, and extending function behavior dynamically.
*   **Higher-Order Components (HOCs) (React specific):** Functions that take a component and return a new component, often wrapping it with additional props or behavior.

Decorators can sometimes achieve similar outcomes to HOFs/HOCs (e.g., a method decorator wrapping a method is like an HOF). However, decorators are syntactically tied to class declarations, while HOFs are more general. The choice often depends on the specific use case and desired syntax.

## 9. Key Takeaways <a name="key-takeaways-decorators-deep"></a>
*   Decorators provide a powerful mechanism for metaprogramming in TypeScript, applied to classes, methods, accessors, properties, and parameters.
*   Each decorator type has a specific signature and purpose.
*   Evaluation order is well-defined but can be intricate.
*   `emitDecoratorMetadata` along with `reflect-metadata` unlocks powerful reflection capabilities, enabling advanced patterns like dependency injection and ORM behavior.
*   Property and parameter decorators are primarily for attaching metadata, as they cannot directly alter the definition or value.
*   While powerful, use decorators judiciously, being mindful of their experimental status and potential performance implications.

This deep dive should provide a more thorough understanding of TypeScript decorators, empowering you to use them effectively and understand their role in various frameworks and libraries.

---

⬅️ [Back to TypeScript and Testing (Jest, Mocha)](../20-ts-testing/README.md) | ➡️ [Next: TypeScript Utility Types (Deep Dive)](../22-ts-utility-types-deep-dive/README.md) *(Placeholder)*
