# 10. TypeScript Decorators

Decorators are a special kind of declaration that can be attached to classes, methods, accessors, properties, or parameters. Decorators use the form `@expression`, where `expression` must evaluate to a function that will be called at runtime with information about the decorated declaration.

**Note:** Decorators are an experimental feature in TypeScript. You must enable the `experimentalDecorators` compiler option in your `tsconfig.json` file to use them:

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES5", // or newer
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true // Often used with decorators, especially with reflection
  }
}
```

## Table of Contents
1.  [What are Decorators?](#what-are-decorators)
2.  [Decorator Factories](#decorator-factories)
3.  [Decorator Composition](#decorator-composition)
4.  [Class Decorators](#class-decorators)
5.  [Method Decorators](#method-decorators)
6.  [Accessor Decorators](#accessor-decorators)
7.  [Property Decorators](#property-decorators)
8.  [Parameter Decorators](#parameter-decorators)
9.  [Order of Evaluation](#order-of-evaluation)
10. [Use Cases for Decorators](#use-cases)
11. [Key Takeaways](#key-takeaways-decorators)

## 1. What are Decorators? <a name="what-are-decorators"></a>
Decorators provide a way to add annotations and a meta-programming syntax for class declarations and members. They are functions that are called at runtime with information about the thing they are decorating.

For example, a decorator `@sealed` might be applied to a class. This decorator function would receive the constructor of the class and could modify it or its prototype.

## 2. Decorator Factories <a name="decorator-factories"></a>
If you want to customize how a decorator is applied, you can write a decorator factory. A decorator factory is simply a function that returns the decorator function.

```typescript
function color(value: string) { // This is the decorator factory
  return function (target: any) { // This is the decorator
    // Add a property 'color' to the constructor
    target.prototype.color = value;
    console.log(`Decorator factory called for color: ${value}`);
  };
}

@color("red")
class MyCar {
  brand: string;
  constructor(brand: string) {
    this.brand = brand;
  }
}

let carInstance: any = new MyCar("Toyota");
console.log(carInstance.color); // Output: red
```

## 3. Decorator Composition <a name="decorator-composition"></a>
Multiple decorators can be applied to a single declaration. They are evaluated in a specific order:
*   Decorator factories are evaluated top-to-bottom.
*   The resulting decorator functions are called bottom-to-top.

```typescript
function First() {
  console.log("First(): factory evaluated");
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    console.log("First(): called");
  };
}

function Second() {
  console.log("Second(): factory evaluated");
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    console.log("Second(): called");
  };
}

class ExampleClassWithComposition {
  @First()
  @Second()
  method() {}
}

// Output will be:
// First(): factory evaluated
// Second(): factory evaluated
// Second(): called
// First(): called
```

## 4. Class Decorators <a name="class-decorators"></a>
A class decorator is declared just before a class declaration. It is applied to the constructor of the class and can be used to observe, modify, or replace a class definition.

The decorator function receives one argument: the constructor of the decorated class.

```typescript
function sealed(constructor: Function) {
  console.log("Sealed decorator called.");
  Object.seal(constructor);
  Object.seal(constructor.prototype);
}

@sealed
class Greeter {
  greeting: string;
  constructor(message: string) {
    this.greeting = message;
  }
  greet() {
    return "Hello, " + this.greeting;
  }
}

// Greeter.prototype.newMethod = () => {}; // This would cause an error if uncommented after sealing

// If the class decorator returns a value, it will replace the class declaration.
function classDecorator<T extends { new (...args: any[]): {} }>(constructor: T) {
  return class extends constructor {
    newProperty = "new property";
    hello = "override";
  };
}

@classDecorator
class MyOriginalClass {
  property = "property";
  hello = "original hello";
}

let decoratedInstance = new MyOriginalClass();
console.log((decoratedInstance as any).newProperty); // "new property"
console.log(decoratedInstance.hello); // "override"
```

## 5. Method Decorators <a name="method-decorators"></a>
A method decorator is declared just before a method declaration. It is applied to the **Property Descriptor** for the method, and can be used to observe, modify, or replace a method definition.

The decorator function receives three arguments:
1.  `target`: Either the constructor function of the class for a static member, or the prototype of the class for an instance member.
2.  `propertyKey`: The name of the method (string or symbol).
3.  `descriptor`: The Property Descriptor for the member (`PropertyDescriptor`).

```typescript
function enumerable(value: boolean) {
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    descriptor.enumerable = value;
    console.log(`Method decorator called for ${propertyKey}`);
  };
}

class Person {
  name: string;
  constructor(name: string) { this.name = name; }

  @enumerable(false) // Make greet method non-enumerable
  greet() {
    return "Hello, my name is " + this.name;
  }
}

let person = new Person("Alice");
for (let key in person) {
  console.log(key); // Will not log 'greet' if enumerable is false
}

// If the method decorator returns a value, it will be used as the Property Descriptor for the method.
function logAccess(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;
  descriptor.value = function(...args: any[]) {
    console.log(`Calling ${propertyKey} with arguments: ${JSON.stringify(args)}`);
    const result = originalMethod.apply(this, args);
    console.log(`Result from ${propertyKey}: ${result}`);
    return result;
  }
  return descriptor;
}

class Calculator {
    @logAccess
    add(a: number, b: number): number {
        return a + b;
    }
}

const calc = new Calculator();
calc.add(5, 3);
// Output:
// Calling add with arguments: [5,3]
// Result from add: 8
```

## 6. Accessor Decorators <a name="accessor-decorators"></a>
An accessor decorator is declared just before an accessor declaration (getters/setters). It is applied to the Property Descriptor for the accessor and can be used to observe, modify, or replace an accessor's definitions.

Accessor decorators receive the same three arguments as method decorators: `target`, `propertyKey`, and `descriptor`.

```typescript
function configurable(value: boolean) {
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    descriptor.configurable = value;
    console.log(`Accessor decorator called for ${propertyKey}`);
  };
}

class Point {
  private _x: number;
  private _y: number;
  constructor(x: number, y: number) {
    this._x = x;
    this._y = y;
  }

  @configurable(false)
  get x() { return this._x; }

  @configurable(false)
  set y(value: number) { this._y = value; }
}

// If an accessor decorator returns a value, it will be used as the Property Descriptor.
```
**Note:** TypeScript does not allow decorating both the `get` and `set` accessor for a single member. If you have both, apply the decorator to the first one specified in document order.

## 7. Property Decorators <a name="property-decorators"></a>
A property decorator is declared just before a property declaration.

The decorator function receives two arguments:
1.  `target`: Either the constructor function of the class for a static member, or the prototype of the class for an instance member.
2.  `propertyKey`: The name of the property (string or symbol).

```typescript
import "reflect-metadata"; // Required for metadata-based decorators

function format(formatString: string) {
  return Reflect.metadata("format", formatString);
}

function getFormat(target: any, propertyKey: string) {
  return Reflect.getMetadata("format", target, propertyKey);
}

class User {
  @format("Hello, %s")
  greeting: string;

  constructor(message: string) {
    this.greeting = message;
  }
  greet() {
    let formatString = getFormat(this, "greeting");
    return formatString.replace("%s", this.greeting);
  }
}

let user = new User("World");
console.log(user.greet()); // "Hello, World"
```
Property decorators **cannot** return a value to modify the property descriptor directly (because property descriptors are not provided as an argument). They are primarily used to record metadata about the property.

## 8. Parameter Decorators <a name="parameter-decorators"></a>
A parameter decorator is declared just before a parameter declaration. It is applied to the function for a class constructor or method declaration.

The decorator function receives three arguments:
1.  `target`: Either the constructor function of the class for a static member, or the prototype of the class for an instance member.
2.  `propertyKey`: The name of the method or `undefined` for constructor parameters.
3.  `parameterIndex`: The zero-based index of the parameter in the function’s parameter list.

```typescript
import "reflect-metadata";

const requiredMetadataKey = Symbol("required");

function required(target: Object, propertyKey: string | symbol | undefined, parameterIndex: number) {
  console.log(`Parameter decorator called for parameter ${parameterIndex} of ${String(propertyKey)}`);
  let existingRequiredParameters: number[] = Reflect.getOwnMetadata(requiredMetadataKey, target, propertyKey!) || [];
  existingRequiredParameters.push(parameterIndex);
  Reflect.defineMetadata(requiredMetadataKey, existingRequiredParameters, target, propertyKey!);
}

function validate(target: any, propertyName: string, descriptor: TypedPropertyDescriptor<Function>) {
  let method = descriptor.value!;
  descriptor.value = function (...args: any[]) {
    let requiredParameters: number[] = Reflect.getOwnMetadata(requiredMetadataKey, target, propertyName);
    if (requiredParameters) {
      for (let parameterIndex of requiredParameters) {
        if (parameterIndex >= args.length || args[parameterIndex] === undefined || args[parameterIndex] === null) {
          throw new Error(`Missing required argument at index ${parameterIndex} for method ${propertyName}.`);
        }
      }
    }
    return method.apply(this, args);
  }
}

class BugReport {
  type = "report";
  title: string;

  constructor(title: string) {
    this.title = title;
  }

  @validate
  print(@required title: string, @required body: string, verbose?: boolean) {
    if (verbose) {
      return `Type: ${this.type}\nTitle: ${title}\nBody: ${body}`;
    }
    return title;
  }
}

const report = new BugReport("My Report");
console.log(report.print("Test Title", "Test Body", true));
// report.print("Test Title", null); // Throws error: Missing required argument at index 1...
```
Parameter decorators can only be used to observe that a parameter has been declared on a method. They cannot change the parameter's value or type directly.

## 9. Order of Evaluation <a name="order-of-evaluation"></a>
For decorators applied to various declarations within a class, the evaluation order is as follows:
1.  Parameter Decorators, then Method, Accessor, or Property Decorators are applied for each instance member.
2.  Parameter Decorators, then Method, Accessor, or Property Decorators are applied for each static member.
3.  Parameter Decorators are applied for the constructor.
4.  Class Decorators are applied for the class.

Within each step, decorators are evaluated in declaration order (as they appear in the code).

## 10. Use Cases for Decorators <a name="use-cases"></a>
Decorators are commonly used for:
*   **Metadata Annotation:** Adding metadata to classes or members (e.g., for ORMs, serialization, routing in frameworks like Angular or NestJS).
*   **Modifying Behavior:** Changing how a class or method behaves (e.g., logging, performance monitoring, access control).
*   **Code Generation:** They can be part of a larger system that generates code or modifies existing code structures.
*   **Dependency Injection:** Frameworks often use parameter decorators to mark constructor parameters for dependency injection.

## 11. Key Takeaways <a name="key-takeaways-decorators"></a>
*   Decorators are an **experimental feature** (`@expression`) for meta-programming.
*   Enable `experimentalDecorators` and often `emitDecoratorMetadata` in `tsconfig.json`.
*   **Decorator Factories** are functions that return decorator functions, allowing customization.
*   **Decorator Composition** allows multiple decorators, evaluated in a specific order.
*   Types of decorators:
    *   **Class Decorators:** Observe, modify, or replace class constructors.
    *   **Method Decorators:** Observe, modify, or replace method definitions.
    *   **Accessor Decorators:** Observe, modify, or replace accessor definitions.
    *   **Property Decorators:** Primarily for metadata; cannot directly modify property definitions.
    *   **Parameter Decorators:** Observe parameter declarations; often used for metadata or DI.
*   Decorators provide powerful ways to write cleaner, more declarative code, especially in frameworks.

While powerful, use decorators judiciously as they are an advanced feature and can make code harder to understand if overused or poorly implemented.

---

⬅️ [Back to Modules and Namespaces](../09-ts-modules-namespaces/README.md) | ➡️ [Next: Mixins](../11-ts-mixins/README.md)
