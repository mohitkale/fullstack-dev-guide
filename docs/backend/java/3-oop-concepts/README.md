# Object-Oriented Programming (OOP) in Java

## Table of Contents
1. [Introduction to OOP](#introduction-to-oop)
2. [Classes and Objects](#classes-and-objects)
3. [Inheritance](#inheritance)
4. [Polymorphism](#polymorphism)
5. [Abstraction](#abstraction)
6. [Encapsulation](#encapsulation)
7. [Interfaces and Abstract Classes](#interfaces-and-abstract-classes)
8. [Practice Exercises](#practice-exercises-)
9. [Common Interview Questions](#common-interview-questions)

## Introduction to OOP

Object-Oriented Programming (OOP) is a programming paradigm based on the concept of "objects", which can contain data and code. The four main principles of OOP are:

1. **Encapsulation**: Bundling data and methods that operate on the data within one unit
2. **Inheritance**: Mechanism to create a new class using properties of an existing class
3. **Polymorphism**: Ability of an object to take many forms
4. **Abstraction**: Hiding complex implementation details and showing only the necessary features

## Classes and Objects

### Class
A blueprint for creating objects. It defines a data type that includes both data (attributes) and methods (behaviors).

```java
// Class definition
class BankAccount {
    // Fields (attributes)
    private String accountNumber;
    private String accountHolder;
    private double balance;
    
    // Constructor
    public BankAccount(String accountNumber, String accountHolder, double initialBalance) {
        this.accountNumber = accountNumber;
        this.accountHolder = accountHolder;
        this.balance = initialBalance;
    }
    
    // Methods (behaviors)
    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
            System.out.println("Deposited: ₹" + amount);
        }
    }
    
    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
            System.out.println("Withdrawn: ₹" + amount);
        } else {
            System.out.println("Invalid amount or insufficient balance");
        }
    }
    
    public double checkBalance() {
        return balance;
    }
}
```

### Object
An instance of a class. You can create multiple objects from a single class.

```java
public class Main {
    public static void main(String[] args) {
        // Creating objects of BankAccount class
        BankAccount account1 = new BankAccount("1234567890", "Rahul Sharma", 10000.0);
        BankAccount account2 = new BankAccount("0987654321", "Priya Patel", 25000.0);
        
        // Using object methods
        account1.deposit(5000);
        account1.withdraw(2000);
        System.out.println("Current balance: ₹" + account1.checkBalance());
    }
}
```

## Inheritance

Inheritance allows a class to inherit properties and behaviors from another class.

```java
// Parent class (Superclass)
class BankAccount {
    protected String accountNumber;
    protected String accountHolder;
    protected double balance;
    
    public BankAccount(String accountNumber, String accountHolder, double balance) {
        this.accountNumber = accountNumber;
        this.accountHolder = accountHolder;
        this.balance = balance;
    }
    
    public void deposit(double amount) {
        if (amount > 0) balance += amount;
    }
    
    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance) balance -= amount;
    }
    
    public double getBalance() {
        return balance;
    }
}

// Child class (Subclass)
class SavingsAccount extends BankAccount {
    private double interestRate;
    
    public SavingsAccount(String accountNumber, String accountHolder, double balance, double interestRate) {
        super(accountNumber, accountHolder, balance);
        this.interestRate = interestRate;
    }
    
    public void addInterest() {
        double interest = balance * interestRate / 100;
        deposit(interest);
        System.out.println("Interest added: ₹" + interest);
    }
    
    @Override
    public void withdraw(double amount) {
        // Minimum balance requirement for savings account
        if (balance - amount >= 1000) {
            super.withdraw(amount);
        } else {
            System.out.println("Withdrawal failed. Minimum balance of ₹1000 required.");
        }
    }
}
```

## Polymorphism

### Compile-time Polymorphism (Method Overloading)
```java
class Calculator {
    // Method to add two integers
    public int add(int a, int b) {
        return a + b;
    }
    
    // Method to add three integers
    public int add(int a, int b, int c) {
        return a + b + c;
    }
    
    // Method to add two doubles
    public double add(double a, double b) {
        return a + b;
    }
}
```

### Run-time Polymorphism (Method Overriding)
```java
class Animal {
    public void makeSound() {
        System.out.println("Animal makes a sound");
    }
}

class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Dog barks: Woof! Woof!");
    }
}

class Cat extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Cat meows: Meow! Meow!");
    }
}

public class Main {
    public static void main(String[] args) {
        Animal myAnimal = new Animal();
        Animal myDog = new Dog();
        Animal myCat = new Cat();
        
        myAnimal.makeSound();  // Animal makes a sound
        myDog.makeSound();     // Dog barks: Woof! Woof!
        myCat.makeSound();     // Cat meows: Meow! Meow!
    }
}
```

## Abstraction

### Abstract Class
```java
abstract class Shape {
    protected String color;
    
    public Shape(String color) {
        this.color = color;
    }
    
    // Abstract method (no implementation)
    public abstract double calculateArea();
    
    // Concrete method
    public void displayColor() {
        System.out.println("Color: " + color);
    }
}

class Circle extends Shape {
    private double radius;
    
    public Circle(String color, double radius) {
        super(color);
        this.radius = radius;
    }
    
    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }
}
```

## Encapsulation

Encapsulation is about bundling data (variables) and methods that operate on the data into a single unit (class) and restricting direct access to some of the object's components.

```java
public class Student {
    // Private fields (data hiding)
    private String name;
    private int rollNumber;
    private double marks;
    
    // Public getter and setter methods
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        if (name != null && !name.trim().isEmpty()) {
            this.name = name;
        }
    }
    
    public int getRollNumber() {
        return rollNumber;
    }
    
    public void setRollNumber(int rollNumber) {
        if (rollNumber > 0) {
            this.rollNumber = rollNumber;
        }
    }
    
    public double getMarks() {
        return marks;
    }
    
    public void setMarks(double marks) {
        if (marks >= 0 && marks <= 100) {
            this.marks = marks;
        }
    }
    
    // Method to calculate grade based on marks
    public String calculateGrade() {
        if (marks >= 90) return "A+";
        else if (marks >= 80) return "A";
        else if (marks >= 70) return "B";
        else if (marks >= 60) return "C";
        else if (marks >= 50) return "D";
        else return "F";
    }
}
```

## Interfaces and Abstract Classes

### Interface
```java
interface PaymentGateway {
    // Constants (public static final by default)
    double TAX_RATE = 18.0;
    
    // Abstract methods (public abstract by default)
    boolean processPayment(double amount);
    double calculateTax(double amount);
    
    // Default method (Java 8+)
    default String getPaymentStatus() {
        return "Payment status: Pending";
    }
    
    // Static method (Java 8+)
    static boolean isValidAmount(double amount) {
        return amount > 0;
    }
}

class CreditCardPayment implements PaymentGateway {
    private String cardNumber;
    private String cardHolderName;
    
    public CreditCardPayment(String cardNumber, String cardHolderName) {
        this.cardNumber = cardNumber;
        this.cardHolderName = cardHolderName;
    }
    
    @Override
    public boolean processPayment(double amount) {
        // Actual payment processing logic would go here
        System.out.println("Processing credit card payment of ₹" + amount);
        return true;
    }
    
    @Override
    public double calculateTax(double amount) {
        return amount * TAX_RATE / 100;
    }
    
    @Override
    public String getPaymentStatus() {
        return "Credit card payment status: Completed";
    }
}
```

### Abstract Class vs Interface

| Feature | Abstract Class | Interface |
|---------|----------------|-----------|
| Can have constructors | Yes | No (until Java 8) |
| Can have instance variables | Yes | Only constants (public static final) |
| Can have concrete methods | Yes | Yes (default/static methods) |
| Multiple inheritance | No | Yes |
| Access modifiers | Any | public (by default) |
| When to use | Share code among related classes | Define behavior for unrelated classes |

## Practice Exercises 📝

1. **Banking System**
   - Create a `BankAccount` class with deposit, withdraw, and check balance methods
   - Create `SavingsAccount` and `CurrentAccount` classes that inherit from `BankAccount`
   - Add specific features like minimum balance for savings account and overdraft for current account

2. **Shape Calculator**
   - Create an abstract `Shape` class with abstract methods `calculateArea()` and `calculatePerimeter()`
   - Implement `Circle`, `Rectangle`, and `Triangle` classes that extend `Shape`
   - Create a method to display area and perimeter for any shape

3. **E-commerce System**
   - Create an interface `PaymentMethod` with methods like `processPayment()` and `refundPayment()`
   - Implement classes like `CreditCardPayment`, `UPIPayment`, and `NetBankingPayment`
   - Create a `ShoppingCart` class that can accept any payment method

## Common Interview Questions

1. What is the difference between method overloading and method overriding?
2. Explain the difference between abstraction and encapsulation.
3. What is the difference between an abstract class and an interface?
4. What is the `super` keyword used for in Java?
5. What is the `final` keyword and where can it be used?
6. What is the difference between `==` and `.equals()` when comparing objects?
7. What is a constructor and can it be made private?
8. What is the difference between `this()` and `super()` in Java constructors?
9. Can we override static methods in Java? Why or why not?
10. What is the purpose of the `instanceof` operator in Java?

🔙 [Previous: Java Basics](../2-java-basics/README.md) | 
🔜 [Next: Exception Handling →](../4-exception-handling/README.md)
