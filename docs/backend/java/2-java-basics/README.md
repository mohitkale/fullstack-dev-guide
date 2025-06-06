# Java Basics: Building Blocks of Java Programming

## Table of Contents
1. [Variables and Data Types](#variables-and-data-types)
2. [Operators in Java](#operators-in-java)
3. [Control Flow Statements](#control-flow-statements)
4. [Arrays and Strings](#arrays-and-strings)
5. [Practice Exercises](#practice-exercises-)
6. [Common Interview Questions](#common-interview-questions-fresher-level)

## Variables and Data Types

### Primitive Data Types
Java has 8 primitive data types:

1. **byte**: 8-bit integer (-128 to 127)
   ```java
   byte age = 25;
   ```

2. **short**: 16-bit integer (-32,768 to 32,767)
   ```java
   short population = 32000;
   ```

3. **int**: 32-bit integer (most commonly used)
   ```java
   int salary = 500000; // Salary in INR
   ```

4. **long**: 64-bit integer (add 'L' at the end)
   ```java
   long indiaPopulation = 1380004385L;
   ```

5. **float**: 32-bit floating point (add 'f' at the end)
   ```java
   float temperature = 36.5f; // Body temperature
   ```

6. **double**: 64-bit floating point (default for decimal numbers)
   ```java
   double pi = 3.14159265359;
   ```

7. **boolean**: true or false
   ```java
   boolean isIndian = true;
   ```

8. **char**: Single 16-bit Unicode character
   ```java
   char grade = 'A';
   ```

### Reference Data Types
- **String**: Sequence of characters
  ```java
  String name = "Rahul Sharma";
  String city = "Mumbai";
  ```

## Operators in Java

### 1. Arithmetic Operators
```java
int a = 10, b = 3;
System.out.println(a + b); // 13 (Addition)
System.out.println(a - b); // 7 (Subtraction)
System.out.println(a * b); // 30 (Multiplication)
System.out.println(a / b); // 3 (Division - integer division)
System.out.println(a % b); // 1 (Modulus - remainder)
```

### 2. Assignment Operators
```java
int x = 10;
x += 5;  // x = x + 5 → 15
x -= 3;  // x = x - 3 → 12
x *= 2;  // x = x * 2 → 24
x /= 4;  // x = x / 4 → 6
```

### 3. Comparison Operators
```java
int age = 25;
boolean canVote = age >= 18;  // true
boolean isTeenager = age >= 13 && age <= 19;  // false
```

### 4. Logical Operators
```java
boolean hasAadhar = true;
boolean hasPan = false;
boolean canOpenAccount = hasAadhar || hasPan;  // true
boolean isEligibleForLoan = hasAadhar && hasPan;  // false
```

## Control Flow Statements

### 1. If-Else
```java
int marks = 85;

if (marks >= 90) {
    System.out.println("Grade: A+");
} else if (marks >= 80) {
    System.out.println("Grade: A");  // This will be printed
} else if (marks >= 70) {
    System.out.println("Grade: B");
} else {
    System.out.println("Grade: C");
}
```

### 2. Switch Statement
```java
String day = "Sunday";

switch (day) {
    case "Monday":
        System.out.println("Start of work week");
        break;
    case "Friday":
        System.out.println("Weekend is coming!");
        break;
    case "Saturday":
    case "Sunday":
        System.out.println("Weekend!");  // This will be printed
        break;
    default:
        System.out.println("Midweek day");
}
```

### 3. For Loop
```java
// Print multiplication table of 5
for (int i = 1; i <= 10; i++) {
    System.out.printf("5 x %d = %d%n", i, 5 * i);
}
```

### 4. While Loop
```java
// Countdown from 10 to 1
int count = 10;
while (count > 0) {
    System.out.println(count);
    count--;
}
```

### 5. Do-While Loop
```java
// Ask for input at least once
Scanner scanner = new Scanner(System.in);
String input;
do {
    System.out.print("Enter 'exit' to quit: ");
    input = scanner.nextLine();
} while (!input.equalsIgnoreCase("exit"));
```

## Arrays and Strings

### 1. Arrays
```java
// Array of Indian states
String[] states = {"Maharashtra", "Tamil Nadu", "Uttar Pradesh", "Karnataka"};

// Access elements
System.out.println(states[0]);  // Maharashtra

// Length of array
System.out.println(states.length);  // 4

// Iterate through array
for (String state : states) {
    System.out.println(state);
}
```

### 2. Multi-dimensional Arrays
```java
// 2D array (like a table)
int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

// Access element
System.out.println(matrix[1][2]);  // 6
```

### 3. String Methods
```java
String name = "  Rahul Sharma  ";

System.out.println(name.length());        // 15 (including spaces)
System.out.println(name.trim());         // "Rahul Sharma"
System.out.println(name.toUpperCase());  // "  RAHUL SHARMA  "
System.out.println(name.contains("Rahul")); // true
System.out.println(name.substring(3, 8)); // "ul Sh"
```

## Practice Exercises 📝

1. **Temperature Converter**
   - Write a program that converts Celsius to Fahrenheit
   - Formula: °F = (°C × 9/5) + 32

2. **Simple Calculator**
   - Create a calculator that can perform +, -, *, / operations
   - Take two numbers and operation as input

3. **Student Grade Calculator**
   - Take marks of 5 subjects as input
   - Calculate percentage and grade

4. **Number Guessing Game**
   - Generate a random number between 1-100
   - Let user guess the number with hints (higher/lower)

## Common Interview Questions (Fresher Level)

1. What is the difference between `==` and `.equals()` in Java?
2. What is the difference between `int` and `Integer`?
3. What is the difference between `String`, `StringBuilder`, and `StringBuffer`?
4. Explain the difference between `while` and `do-while` loop.
5. How do you find the length of an array in Java?

🔙 [Previous: Getting Started](../1-getting-started/README.md) | 
🔜 [Next: OOP Concepts →](../3-oop-concepts/README.md)
