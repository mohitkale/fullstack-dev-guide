# 08. JavaScript Objects and Prototypes

Objects are a fundamental part of JavaScript, as it's an object-oriented programming (OOP) language based on prototypes rather than classes (though ES6 introduced class syntax as syntactic sugar). Understanding objects and prototypal inheritance is key to mastering JavaScript.

## Table of Contents
1.  [What is an Object?](#what-is-object)
2.  [Creating Objects](#creating-objects)
    *   [Object Literals](#object-literals)
    *   [Constructor Functions](#constructor-functions)
    *   [The `new` Keyword](#new-keyword)
    *   [`Object.create()` Method](#object-create)
    *   [ES6 Classes (Syntactic Sugar)](#es6-classes-intro)
3.  [Object Properties](#object-properties)
    *   [Accessing Properties (Dot vs. Bracket Notation)](#accessing-properties)
    *   [Adding and Modifying Properties](#adding-modifying-properties)
    *   [Deleting Properties (`delete` operator)](#deleting-properties)
    *   [Property Descriptors (Advanced)](#property-descriptors)
    *   [Checking if a Property Exists (`in` operator, `hasOwnProperty`)](#checking-property-existence)
4.  [Object Methods](#object-methods)
    *   [Defining Methods](#defining-methods)
    *   [The `this` Keyword in Methods](#this-in-methods)
5.  [Getters and Setters](#getters-setters)
6.  [What is a Prototype?](#what-is-prototype)
7.  [The Prototype Chain](#prototype-chain)
    *   [How Property Lookup Works](#property-lookup-chain)
    *   [`__proto__` vs `Object.getPrototypeOf()`](#proto-vs-getprototypeof)
8.  [Prototypal Inheritance](#prototypal-inheritance)
    *   [Inheriting Properties and Methods](#inheriting-props-methods)
    *   [Setting an Object's Prototype](#setting-prototype)
        *   `Object.create()`
        *   Constructor Functions and `prototype` Property
        *   `Object.setPrototypeOf()` (Use with caution)
9.  [`Object.prototype`](#object-prototype-object)
    *   [Common Methods (e.g., `toString()`, `hasOwnProperty()`)](#common-object-prototype-methods)
10. [ES6 Classes Revisited](#es6-classes-revisited)
    *   [How Classes Relate to Prototypes](#classes-and-prototypes)
    *   [`constructor`, `super`, `extends`](#class-keywords)
11. [Best Practices for Objects and Prototypes](#best-practices-objects)
12. [Key Takeaways](#key-takeaways-objects-prototypes)

## 1. What is an Object? <a name="what-is-object"></a>
In JavaScript, an object is a standalone entity, with properties and type. Think of it as a collection of key-value pairs, where keys are strings (or Symbols) and values can be any data type, including other objects or functions (which are then called methods).

Objects are used to model real-world entities or abstract concepts. Almost everything in JavaScript is an object or can behave like one (e.g., primitives can have methods called on them due to auto-boxing).

## 2. Creating Objects <a name="creating-objects"></a>
There are several ways to create objects in JavaScript.

### Object Literals <a name="object-literals"></a>
This is the simplest and most common way to create an object.
```javascript
const person = {
  firstName: "John",
  lastName: "Doe",
  age: 30,
  isAdmin: false,
  greet: function() {
    console.log(`Hello, my name is ${this.firstName} ${this.lastName}.`);
  },
  address: {
    street: "123 Main St",
    city: "Anytown"
  }
};

console.log(person.firstName); // John
person.greet(); // Hello, my name is John Doe.
```

### Constructor Functions <a name="constructor-functions"></a>
Constructor functions are special functions used to create and initialize objects. By convention, their names start with a capital letter. They are invoked using the `new` keyword.
```javascript
function Person(firstName, lastName, age) {
  // 'this' refers to the new object being created
  this.firstName = firstName;
  this.lastName = lastName;
  this.age = age;

  this.getFullName = function() {
    return `${this.firstName} ${this.lastName}`;
  };
}

// Create objects (instances) using the constructor
const person1 = new Person("Alice", "Smith", 25);
const person2 = new Person("Bob", "Johnson", 40);

console.log(person1.firstName); // Alice
console.log(person2.getFullName()); // Bob Johnson
```
Methods defined directly inside a constructor function (like `getFullName` above) are created for *each instance*, which can be inefficient. It's often better to define shared methods on the constructor's `prototype` (see [Prototypal Inheritance](#prototypal-inheritance)).

### The `new` Keyword <a name="new-keyword"></a>
When `new` is used with a constructor function, it does the following:
1.  Creates a new, empty object.
2.  Sets the `this` keyword to point to this new object.
3.  Sets the new object's internal `[[Prototype]]` (often accessible via `__proto__`) to the constructor function's `prototype` object.
4.  Executes the constructor function's code, typically initializing the new object's properties.
5.  If the constructor function doesn't explicitly return an object, it implicitly returns `this` (the new object).

### `Object.create()` Method <a name="object-create"></a>
`Object.create()` allows you to create a new object with a specified prototype object and optional properties.
```javascript
const animalPrototype = {
  makeSound: function() {
    console.log("Generic animal sound");
  }
};

// Create an object 'dog' whose prototype is 'animalPrototype'
const dog = Object.create(animalPrototype);
dog.breed = "Labrador"; // Add a property to 'dog'

dog.makeSound(); // Generic animal sound (inherited from prototype)
console.log(dog.breed); // Labrador

// Create an object with no prototype (a truly empty object)
const emptyObj = Object.create(null);
// console.log(emptyObj.toString()); // Error: emptyObj.toString is not a function
```
This method is powerful for setting up inheritance chains directly.

### ES6 Classes (Syntactic Sugar) <a name="es6-classes-intro"></a>
ES6 introduced `class` syntax, which provides a cleaner and more familiar way (for developers from class-based languages) to create objects and manage inheritance. However, it's important to remember that ES6 classes are primarily syntactic sugar over JavaScript's existing prototypal inheritance.
```javascript
class Vehicle {
  constructor(make, model) {
    this.make = make;
    this.model = model;
  }

  getInfo() {
    return `${this.make} ${this.model}`;
  }
}

const car = new Vehicle("Toyota", "Camry");
console.log(car.getInfo()); // Toyota Camry
```
We'll revisit classes after understanding prototypes.

## 3. Object Properties <a name="object-properties"></a>
Properties are the characteristics of an object. They are key-value pairs.

### Accessing Properties (Dot vs. Bracket Notation) <a name="accessing-properties"></a>
*   **Dot Notation (`object.propertyName`)**: Simpler and more common. Property name must be a valid JavaScript identifier and known at coding time.
    ```javascript
    const book = { title: "The Great Gatsby", author: "F. Scott Fitzgerald" };
    console.log(book.title); // The Great Gatsby
    ```
*   **Bracket Notation (`object['propertyName']`)**: More flexible. Property name is a string (or Symbol) and can be dynamic (e.g., stored in a variable) or not a valid identifier (e.g., contains spaces or starts with a number).
    ```javascript
    console.log(book['author']); // F. Scott Fitzgerald

    let propName = "title";
    console.log(book[propName]); // The Great Gatsby

    const specialProps = {
      "property with spaces": "value1",
      "123property": "value2"
    };
    console.log(specialProps["property with spaces"]); // value1
    ```

### Adding and Modifying Properties <a name="adding-modifying-properties"></a>
You can add new properties or modify existing ones using assignment.
```javascript
const user = { name: "Eve" };

// Add new property
user.age = 28;
user['city'] = "London";

// Modify existing property
user.name = "Eva";

console.log(user); // { name: "Eva", age: 28, city: "London" }
```

### Deleting Properties (`delete` operator) <a name="deleting-properties"></a>
The `delete` operator removes a property from an object. It returns `true` if successful or if the property doesn't exist; `false` only if the property is non-configurable.
```javascript
const fruit = { name: "Apple", color: "Red", taste: "Sweet" };
delete fruit.taste;
console.log(fruit); // { name: "Apple", color: "Red" }

console.log(delete fruit.nonExistentProp); // true
```
`delete` only affects own properties. It cannot delete properties from the prototype chain.

### Property Descriptors (Advanced) <a name="property-descriptors"></a>
Each property has associated attributes called property descriptors, which control its behavior:
*   `value`: The value of the property.
*   `writable`: If `true`, the property's value can be changed.
*   `enumerable`: If `true`, the property will appear in `for...in` loops and `Object.keys()`.
*   `configurable`: If `true`, the property can be deleted, and its descriptors can be changed.

Use `Object.defineProperty()` and `Object.getOwnPropertyDescriptor()` to work with these.
```javascript
const obj = {};
Object.defineProperty(obj, 'myProp', {
  value: 100,
  writable: false, // read-only
  enumerable: true,
  configurable: false
});
console.log(obj.myProp); // 100
// obj.myProp = 200; // In strict mode, throws TypeError. Silently fails otherwise.
// delete obj.myProp; // false (cannot delete)
```

### Checking if a Property Exists (`in` operator, `hasOwnProperty`) <a name="checking-property-existence"></a>
*   **`in` operator**: Returns `true` if the property exists in the object or its prototype chain.
    ```javascript
    const person = { name: "Alice" };
    console.log('name' in person);       // true (own property)
    console.log('toString' in person); // true (inherited from Object.prototype)
    ```
*   **`hasOwnProperty()`**: Returns `true` only if the property is an *own* property of the object (not inherited).
    ```javascript
    console.log(person.hasOwnProperty('name'));       // true
    console.log(person.hasOwnProperty('toString')); // false
    ```

## 4. Object Methods <a name="object-methods"></a>
Methods are functions that are stored as object properties.

### Defining Methods <a name="defining-methods"></a>
```javascript
// Method in object literal
const calculator = {
  value: 0,
  add: function(num) {
    this.value += num;
    return this.value;
  },
  // ES6 shorthand method syntax
  subtract(num) {
    this.value -= num;
    return this.value;
  }
};

console.log(calculator.add(5));      // 5
console.log(calculator.subtract(2)); // 3
```

### The `this` Keyword in Methods <a name="this-in-methods"></a>
Inside a regular function method, `this` refers to the object that the method was called on (the object before the dot).
```javascript
const myObj = {
  name: "MyObject",
  logName: function() {
    console.log(this.name); // 'this' refers to myObj
  }
};
myObj.logName(); // MyObject

const anotherLogName = myObj.logName;
// anotherLogName(); // In non-strict mode, 'this' would be global (window/undefined). In strict mode, 'this' is undefined. Leads to error.
```
The value of `this` depends on *how the function is called* (invocation context), not where it's defined. Arrow functions behave differently: they lexically bind `this`, meaning they inherit `this` from their surrounding scope.

## 5. Getters and Setters <a name="getters-setters"></a>
Getters and setters allow you to define special methods that look like regular properties but execute code when accessed or modified.

```javascript
const userProfile = {
  _firstName: "Jane",
  _lastName: "Doe",

  get fullName() {
    console.log("Getting full name...");
    return `${this._firstName} ${this._lastName}`;
  },

  set fullName(nameStr) {
    console.log("Setting full name...");
    const parts = nameStr.split(' ');
    if (parts.length === 2) {
      this._firstName = parts[0];
      this._lastName = parts[1];
    } else {
      console.error("Invalid full name format.");
    }
  }
};

console.log(userProfile.fullName); // Getting full name... Jane Doe (accesses the getter)

userProfile.fullName = "Alice Wonderland"; // Setting full name... (invokes the setter)
console.log(userProfile._firstName); // Alice
console.log(userProfile.fullName);   // Getting full name... Alice Wonderland
```
Getters are defined with `get` and setters with `set`.

## 6. What is a Prototype? <a name="what-is-prototype"></a>
In JavaScript, objects can inherit properties and methods from other objects. This mechanism is called prototypal inheritance.

Every JavaScript object has an internal property called `[[Prototype]]` (often exposed as `__proto__` or accessible via `Object.getPrototypeOf()`). This property is a reference (link) to another object, called the object's **prototype**.

When you try to access a property or method on an object, if JavaScript doesn't find it directly on the object itself, it looks at the object's prototype. If it's not found there, it looks at the prototype's prototype, and so on, until the property is found or the chain ends with `null`.

## 7. The Prototype Chain <a name="prototype-chain"></a>
This sequence of linked prototypes forms the **prototype chain**.

**Example:**
```javascript
const parent = {
  parentProp: "I am from parent",
  greet: function() {
    console.log("Hello from parent!");
  }
};

const child = Object.create(parent);
child.childProp = "I am from child";

console.log(child.childProp);  // "I am from child" (own property)
console.log(child.parentProp); // "I am from parent" (inherited from parent)
child.greet();               // "Hello from parent!" (inherited method)

console.log(Object.getPrototypeOf(child) === parent); // true
```
Here, `child`'s prototype is `parent`. If we access `child.parentProp`, JavaScript first checks `child`. Not found. Then it checks `parent` (child's prototype). Found.

### `__proto__` vs `Object.getPrototypeOf()` <a name="proto-vs-getprototypeof"></a>
*   `__proto__` (dunder proto): A non-standard but widely supported way to get/set an object's prototype. Its use is discouraged in modern JavaScript.
*   `Object.getPrototypeOf(obj)`: The standard ES5 way to get an object's prototype.
*   `Object.setPrototypeOf(obj, prototype)`: The standard ES6 way to set an object's prototype (use with caution as it can impact performance).

## 8. Prototypal Inheritance <a name="prototypal-inheritance"></a>
Prototypal inheritance is the mechanism by which objects inherit features from one another.

### Inheriting Properties and Methods <a name="inheriting-props-methods"></a>
As seen in the prototype chain, objects inherit properties and methods from their prototypes.

### Setting an Object's Prototype <a name="setting-prototype"></a>
1.  **`Object.create(prototypeObj)`**: Creates a new object with `prototypeObj` as its prototype.
    ```javascript
    const proto = { version: "1.0" };
    const myInstance = Object.create(proto);
    console.log(myInstance.version); // 1.0
    ```
2.  **Constructor Functions and `prototype` Property**: Every function in JavaScript has a special property called `prototype` (note: this is *not* the internal `[[Prototype]]` link of the function object itself, but a property whose value is an object).
    When you create an object using `new ConstructorFunc()`, the new object's internal `[[Prototype]]` is set to `ConstructorFunc.prototype`.
    ```javascript
    function Dog(name) {
      this.name = name;
    }

    // Add methods to Dog.prototype so all Dog instances share them
    Dog.prototype.bark = function() {
      console.log(`${this.name} says Woof!`);
    };

    Dog.prototype.species = "Canine";

    const fido = new Dog("Fido");
    const lucy = new Dog("Lucy");

    fido.bark(); // Fido says Woof!
    lucy.bark(); // Lucy says Woof!
    console.log(fido.species); // Canine

    // fido's internal [[Prototype]] is Dog.prototype
    console.log(Object.getPrototypeOf(fido) === Dog.prototype); // true
    console.log(fido.hasOwnProperty('bark')); // false (bark is on the prototype)
    console.log(Dog.prototype.hasOwnProperty('bark')); // true
    ```
    This is the traditional way to achieve inheritance and share methods among instances.

## 9. `Object.prototype` <a name="object-prototype-object"></a>
Most objects in JavaScript ultimately inherit from `Object.prototype` (unless explicitly created with `Object.create(null)`). This object sits at the top of the prototype chain for most objects and provides common methods.

### Common Methods (e.g., `toString()`, `hasOwnProperty()`) <a name="common-object-prototype-methods"></a>
*   `toString()`: Returns a string representation of the object. Often overridden by more specific object types.
*   `hasOwnProperty(prop)`: Checks if an object has a specific property as its *own* property.
*   `valueOf()`: Returns the primitive value of the specified object.
*   `isPrototypeOf(obj)`: Checks if an object exists in another object's prototype chain.

```javascript
const myObj = {};
console.log(myObj.toString()); // "[object Object]"
console.log(myObj.hasOwnProperty('name')); // false
```

## 10. ES6 Classes Revisited <a name="es6-classes-revisited"></a>
ES6 classes provide a more convenient syntax for creating constructor functions and managing prototypal inheritance.

### How Classes Relate to Prototypes <a name="classes-and-prototypes"></a>
*   A `class` declaration creates a function (the constructor).
*   Methods defined within the class (outside the `constructor`) are added to the `prototype` object of this constructor function.
*   `static` methods are added to the constructor function itself, not its prototype.

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() { // This method is added to Animal.prototype
    console.log(`${this.name} makes a noise.`);
  }

  static createUnknown() { // Static method on Animal constructor
    return new Animal("Unknown");
  }
}

const fluffy = new Animal("Fluffy");
fluffy.speak(); // Fluffy makes a noise.

// speak is on Animal.prototype
console.log(Object.getPrototypeOf(fluffy).hasOwnProperty('speak')); // true
console.log(Animal.prototype.hasOwnProperty('speak')); // true

const unknownAnimal = Animal.createUnknown();
unknownAnimal.speak(); // Unknown makes a noise.
```

### `constructor`, `super`, `extends` <a name="class-keywords"></a>
*   **`constructor`**: Special method for creating and initializing an object created with a class.
*   **`extends`**: Used for class inheritance. It sets up the prototype chain.
*   **`super`**: Used to call the parent class's constructor ( `super(...)` ) or parent class methods ( `super.methodName()` ).

```javascript
class Cat extends Animal { // Cat inherits from Animal
  constructor(name, breed) {
    super(name); // Calls the parent class (Animal) constructor
    this.breed = breed;
  }

  speak() { // Overrides Animal's speak method
    super.speak(); // Calls Animal's speak method
    console.log(`${this.name} purrs.`);
  }
}

const whiskers = new Cat("Whiskers", "Siamese");
whiskers.speak();
// Output:
// Whiskers makes a noise.
// Whiskers purrs.

console.log(Object.getPrototypeOf(Cat.prototype) === Animal.prototype); // true
```
ES6 classes make OOP in JavaScript more structured and easier to read, but the underlying mechanism is still prototypal inheritance.

## 11. Best Practices for Objects and Prototypes <a name="best-practices-objects"></a>
*   **Prefer Object Literals for Simple Objects**: For one-off objects, literals are concise.
*   **Use Constructor Functions or Classes for Reusable Object Blueprints**: When you need multiple instances with the same structure and methods.
*   **Define Shared Methods on the `prototype` (or in Classes)**: To save memory and ensure all instances share the same function object for methods.
*   **Understand `this`**: Be very clear about how `this` behaves in different contexts (regular functions, arrow functions, methods, constructors).
*   **Use `Object.create()` for Fine-Grained Prototype Control**: When you need to explicitly set an object's prototype.
*   **Favor ES6 Classes for OOP**: They offer cleaner syntax and better organization for object-oriented patterns, while still using prototypes under the hood.
*   **Be Cautious with Modifying Built-in Prototypes**: Extending `Object.prototype` or prototypes of other built-in objects (e.g., `Array.prototype`) is generally discouraged as it can lead to conflicts with future JavaScript versions or other libraries (aka "monkey patching").

## 12. Key Takeaways <a name="key-takeaways-objects-prototypes"></a>
*   Objects are collections of key-value pairs, fundamental to JavaScript.
*   Objects can be created via literals, constructor functions (`new`), `Object.create()`, or ES6 classes.
*   Properties can be accessed via dot or bracket notation. `this` in methods refers to the calling object.
*   Prototypes enable inheritance: objects inherit properties/methods from their prototype object, forming a prototype chain.
*   Every function has a `prototype` property, which becomes the `[[Prototype]]` of instances created with `new` and that function.
*   `Object.prototype` is the base for most objects, providing common methods.
*   ES6 classes are syntactic sugar over prototypal inheritance, offering a clearer OOP syntax.

Mastering objects and prototypes is essential for understanding JavaScript's core mechanics and writing effective, object-oriented code.

---

⬅️ [Back to JavaScript Scope and Closures](../07-js-scope-closures/README.md) | ➡️ [Next Section: The `this` Keyword](../09-js-this-keyword/README.md)
