# 04. TypeScript Classes

TypeScript extends JavaScript's support for classes with features like type checking for class members, access modifiers, and more robust object-oriented programming capabilities. Classes provide a way to create blueprints for objects, encapsulating data and functions that operate on that data.

This tutorial covers how to define and use classes in TypeScript.

## Table of Contents
1.  [Introduction to Classes](#introduction-to-classes)
2.  [Basic Class Definition](#basic-class-definition)
    *   [Fields (Properties)](#fields)
    *   [Constructors](#constructors)
    *   [Methods](#methods)
3.  [Inheritance](#inheritance)
    *   [`super()` calls](#super-calls)
    *   [Method Overriding](#method-overriding)
4.  [Access Modifiers](#access-modifiers)
    *   [`public`](#public-modifier)
    *   [`private`](#private-modifier)
    *   [`protected`](#protected-modifier)
    *   [Default Visibility](#default-visibility)
    *   [Understanding `private` vs. JavaScript Private Fields (`#`)](#private-vs-js-private)
5.  [Readonly Modifier](#readonly-modifier-class)
6.  [Parameter Properties](#parameter-properties)
7.  [Accessors (Getters & Setters)](#accessors)
8.  [Static Members](#static-members)
    *   [Static Properties](#static-properties)
    *   [Static Methods](#static-methods)
    *   [Special Static Names](#special-static-names)
9.  [Abstract Classes](#abstract-classes)
    *   [Abstract Methods](#abstract-methods)
10. [Advanced Techniques](#advanced-techniques)
    *   [Constructor Functions](#constructor-functions-class)
    *   [Using a Class as an Interface](#class-as-interface)
11. [Key Takeaways](#key-takeaways-classes)

## 1. Introduction to Classes <a name="introduction-to-classes"></a>
Classes are a fundamental part of object-oriented programming (OOP). They allow you to define a blueprint for creating objects with specific properties (data) and methods (functions that operate on the data). TypeScript builds upon the ES6 class syntax, adding type safety and additional OOP features.

## 2. Basic Class Definition <a name="basic-class-definition"></a>
Here's a simple class definition:

```typescript
class Greeter {
  // Property
  greeting: string;

  // Constructor
  constructor(message: string) {
    this.greeting = message;
  }

  // Method
  greet() {
    return "Hello, " + this.greeting;
  }
}

let greeter = new Greeter("world");
console.log(greeter.greet()); // "Hello, world"
```

### Fields (Properties) <a name="fields"></a>
Fields are variables declared within a class. They represent the data associated with an instance of the class. You must declare fields and their types.

```typescript
class Animal {
  name: string; // Field declaration
  age: number;

  constructor(name: string, age: number) {
    this.name = name; // Initialization in constructor
    this.age = age;
  }
}
```

### Constructors <a name="constructors"></a>
A constructor is a special method for creating and initializing an object created with a class. There can only be one constructor per class. TypeScript allows for constructor overloads, but the implementation must be compatible with all overloads.

```typescript
class Point {
  x: number;
  y: number;

  constructor(x: number = 0, y: number = 0) { // Constructor with default values
    this.x = x;
    this.y = y;
  }
}

let p1 = new Point(); // x=0, y=0
let p2 = new Point(10, 20); // x=10, y=20
```

### Methods <a name="methods"></a>
Methods are functions defined within a class that operate on an object's data. They can access and modify the object's fields using `this`.

```typescript
class Person {
  name: string;
  constructor(name: string) {
    this.name = name;
  }

  sayHello(): string {
    return `Hello, my name is ${this.name}.`;
  }
}

let person = new Person("Alice");
console.log(person.sayHello()); // "Hello, my name is Alice."
```

## 3. Inheritance <a name="inheritance"></a>
TypeScript supports single inheritance using the `extends` keyword. This allows a class (subclass or derived class) to inherit properties and methods from another class (superclass or base class).

```typescript
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  move(distanceInMeters: number = 0) {
    console.log(`${this.name} moved ${distanceInMeters}m.`);
  }
}

class Dog extends Animal {
  constructor(name: string) {
    super(name); // Call the constructor of the base class (Animal)
  }

  bark() {
    console.log("Woof! Woof!");
  }

  // Override move method
  move(distanceInMeters: number = 5) {
    console.log("Running...");
    super.move(distanceInMeters); // Call the base class's move method
  }
}

const dog = new Dog("Buddy");
dog.bark();      // "Woof! Woof!"
dog.move(10);    // "Running..." then "Buddy moved 10m."
```

### `super()` calls <a name="super-calls"></a>
Derived classes that contain a constructor must call `super()` which will execute the constructor of the base class. It's important to call `super()` before accessing `this` in the constructor or returning from the constructor.

### Method Overriding <a name="method-overriding"></a>
Derived classes can override methods from the base class by providing a new implementation with the same name and compatible signature. You can still access the base class method using `super.methodName()`.

## 4. Access Modifiers <a name="access-modifiers"></a>
TypeScript provides access modifiers to control the visibility of class members (properties and methods).

### `public` <a name="public-modifier"></a>
Members marked `public` are accessible from anywhere. This is the default visibility if no modifier is specified.

```typescript
class MyClassPublic {
  public name: string;
  public constructor(name: string) {
    this.name = name;
  }
  public sayName() {
    console.log(this.name);
  }
}
let instancePublic = new MyClassPublic("Public Instance");
console.log(instancePublic.name); // Accessible
instancePublic.sayName(); // Accessible
```

### `private` <a name="private-modifier"></a>
Members marked `private` are only accessible from within the class they are declared in. They are not accessible from subclasses or outside the class.

```typescript
class MyClassPrivate {
  private secret: string;
  constructor(secret: string) {
    this.secret = secret;
  }
  revealSecret() {
    console.log(this.secret); // Accessible within the class
  }
}
let instancePrivate = new MyClassPrivate("My Secret");
// console.log(instancePrivate.secret); // Error: Property 'secret' is private and only accessible within class 'MyClassPrivate'.
instancePrivate.revealSecret(); // OK

class SubClassPrivate extends MyClassPrivate {
  showSecret() {
    // console.log(this.secret); // Error: Property 'secret' is private.
  }
}
```

### `protected` <a name="protected-modifier"></a>
Members marked `protected` are accessible within the declaring class and by instances of its subclasses. They are not accessible from outside these classes.

```typescript
class PersonProtected {
  protected name: string;
  constructor(name: string) {
    this.name = name;
  }
}

class EmployeeProtected extends PersonProtected {
  private department: string;

  constructor(name: string, department: string) {
    super(name);
    this.department = department;
  }

  public getElevatorPitch() {
    return `Hello, my name is ${this.name} and I work in ${this.department}.`; // 'name' is accessible because it's protected
  }
}

let howard = new EmployeeProtected("Howard", "Sales");
console.log(howard.getElevatorPitch());
// console.log(howard.name); // Error: Property 'name' is protected and only accessible within class 'PersonProtected' and its subclasses.
```
A constructor may also be marked `protected`. This means that the class cannot be instantiated outside of its containing class and subclasses, but it can be extended.

### Default Visibility <a name="default-visibility"></a>
If you don’t specify an access modifier, it defaults to `public`.

### Understanding `private` vs. JavaScript Private Fields (`#`) <a name="private-vs-js-private"></a>
TypeScript's `private` is a compile-time construct. It's enforced by the TypeScript compiler but doesn't prevent access at runtime (e.g., if you're working with plain JavaScript that consumes TypeScript output).

Modern JavaScript (ES2022+) introduced private class fields using a hash `#` prefix (e.g., `#myField`). These are truly private at runtime.

```typescript
class MyClassWithTSPrivate {
  private tsSecret: string = "TS secret";
}

class MyClassWithJSPrivate {
  #jsSecret: string = "JS secret";

  getJsSecret() {
    return this.#jsSecret; // Accessible within the class
  }
}

const tsInstance = new MyClassWithTSPrivate();
// console.log(tsInstance.tsSecret); // Compile-time error
// console.log((tsInstance as any).tsSecret); // Runtime access is possible

const jsInstance = new MyClassWithJSPrivate();
// console.log(jsInstance.#jsSecret); // Syntax error - truly private
console.log(jsInstance.getJsSecret()); // "JS secret"
```
TypeScript supports `#` private fields, and they offer stronger privacy guarantees.

## 5. Readonly Modifier <a name="readonly-modifier-class"></a>
You can make properties `readonly` by using the `readonly` keyword. Readonly properties must be initialized either at their declaration or in the constructor.

```typescript
class Octopus {
  readonly name: string;
  readonly numberOfLegs: number = 8;

  constructor(theName: string) {
    this.name = theName; // OK: Initialization in constructor
  }

  setName(newName: string) {
    // this.name = newName; // Error! name is readonly.
  }
}
let dad = new Octopus("Man with the 8 strong legs");
// dad.name = "Man with the 3-piece suit"; // Error! name is readonly.
```

## 6. Parameter Properties <a name="parameter-properties"></a>
TypeScript offers a shorthand for declaring and initializing class members from constructor parameters. By prefixing a constructor parameter with an access modifier (`public`, `private`, `protected`) or `readonly`, TypeScript will automatically create a property with that name and assign the parameter's value to it.

```typescript
class PersonParamProps {
  // No explicit field declarations needed for name and age
  constructor(public name: string, private age: number, readonly id: number) {}

  displayAge() {
    // console.log(this.age); // 'age' is private but accessible within the class
    return this.age;
  }
}

let adam = new PersonParamProps("Adam", 30, 123);
console.log(adam.name); // "Adam" (public)
// console.log(adam.age); // Error: 'age' is private
console.log(adam.displayAge()); // 30
console.log(adam.id); // 123 (readonly, public by default if no modifier)
// adam.id = 456; // Error: 'id' is readonly
```

## 7. Accessors (Getters & Setters) <a name="accessors"></a>
TypeScript supports getters and setters to control access to an object's properties. This allows you to execute code when a property is read or written.

```typescript
class Employee {
  private _fullName: string = "";

  get fullName(): string {
    console.log("Getter called");
    return this._fullName;
  }

  set fullName(newName: string) {
    console.log("Setter called");
    if (newName && newName.length > 20) {
      throw new Error("Name is too long!");
    }
    this._fullName = newName;
  }
}

let employee = new Employee();
employee.fullName = "John Doe"; // Calls setter
console.log(employee.fullName); // Calls getter, outputs "John Doe"

// employee.fullName = "This Name Is Way Too Long For Our System"; // Throws error
```
Accessors require you to set the compiler to output ECMAScript 5 or higher (`"target": "ES5"` or newer in `tsconfig.json`).

## 8. Static Members <a name="static-members"></a>
Static members belong to the class itself, not to instances of the class. You access them using the class name.

### Static Properties <a name="static-properties"></a>
```typescript
class Grid {
  static origin = { x: 0, y: 0 };
  scale: number;

  constructor(scale: number) {
    this.scale = scale;
  }

  calculateDistanceFromOrigin(point: { x: number; y: number; }) {
    let xDist = point.x - Grid.origin.x;
    let yDist = point.y - Grid.origin.y;
    return Math.sqrt(xDist * xDist + yDist * yDist) * this.scale;
  }
}

console.log(Grid.origin); // { x: 0, y: 0 }
let grid1 = new Grid(1.0);
let grid2 = new Grid(5.0);
console.log(grid1.calculateDistanceFromOrigin({ x: 10, y: 10 }));
```

### Static Methods <a name="static-methods"></a>
```typescript
class MathHelper {
  static PI: number = 3.14159;

  static calculateCircumference(radius: number): number {
    return 2 * MathHelper.PI * radius;
  }
}

console.log(MathHelper.PI); // 3.14159
console.log(MathHelper.calculateCircumference(5)); // 31.4159
```

### Special Static Names <a name="special-static-names"></a>
Static members cannot have names like `name`, `length`, `call`, etc., as these conflict with built-in properties of functions.

## 9. Abstract Classes <a name="abstract-classes"></a>
Abstract classes are base classes from which other classes may be derived. They cannot be instantiated directly. They may contain abstract methods or properties, which must be implemented by derived classes.

```typescript
abstract class Department {
  constructor(public name: string) {}

  printName(): void {
    console.log("Department name: " + this.name);
  }

  abstract printMeeting(): void; // Must be implemented in derived class
}

class AccountingDepartment extends Department {
  constructor() {
    super("Accounting and Auditing"); // constructors in derived classes must call super()
  }

  printMeeting(): void {
    console.log("The Accounting Department meets each Monday at 10am.");
  }

  generateReports(): void {
    console.log("Generating accounting reports...");
  }
}

let department: Department; // OK to create a reference to an abstract type
// department = new Department(); // Error: cannot create an instance of an abstract class
department = new AccountingDepartment(); // OK to create and assign a non-abstract subclass
department.printName();
department.printMeeting();
// department.generateReports(); // Error: Method doesn't exist on declared type 'Department'
(department as AccountingDepartment).generateReports(); // OK with type assertion
```

### Abstract Methods <a name="abstract-methods"></a>
Abstract methods are declared without an implementation in an abstract class. Derived classes must provide an implementation for all abstract methods of the base class.

## 10. Advanced Techniques <a name="advanced-techniques"></a>

### Constructor Functions <a name="constructor-functions-class"></a>
When you define a class in TypeScript, you are actually creating multiple declarations. The first is the type of the instances of the class. The second is a *constructor function* value.

```typescript
class GreeterClass {
  greeting: string;
  constructor(message: string) {
    this.greeting = message;
  }
  greet() {
    return "Hello, " + this.greeting;
  }
}

let greeterInstance: GreeterClass; // GreeterClass refers to the instance type
greeterInstance = new GreeterClass("Hi");

let GreeterConstructor: typeof GreeterClass = GreeterClass; // typeof GreeterClass refers to the constructor function type
GreeterConstructor.prototype.greetingSuffix = "!"; // Can add to prototype

let newGreeter = new GreeterConstructor("Hey");
console.log(newGreeter.greet() + (newGreeter as any).greetingSuffix); // "Hello, Hey!"
```

### Using a Class as an Interface <a name="class-as-interface"></a>
A class declaration creates a type representing instances of the class. This type can be used in interface-like positions.

```typescript
class Point2D {
  x: number = 0;
  y: number = 0;
}

interface Point3D extends Point2D { // Class Point2D used as an interface
  z: number;
}

let point3d: Point3D = { x: 1, y: 2, z: 3 };
console.log(point3d); // { x: 1, y: 2, z: 3 }
```

## 11. Key Takeaways <a name="key-takeaways-classes"></a>
*   Classes provide blueprints for objects, supporting OOP principles.
*   TypeScript enhances JavaScript classes with strong typing, access modifiers (`public`, `private`, `protected`), and `readonly` members.
*   Inheritance (`extends`, `super()`) allows for creating class hierarchies.
*   Parameter properties offer a concise way to declare and initialize members.
*   Accessors (getters/setters) control property access.
*   Static members belong to the class itself, not instances.
*   Abstract classes define common structure for subclasses and cannot be instantiated directly.

Classes are a powerful tool for building structured and maintainable applications in TypeScript.

---

⬅️ [Back to Interfaces](../03-ts-interfaces/README.md) | ➡️ [Next: Functions](../05-ts-functions/README.md)
