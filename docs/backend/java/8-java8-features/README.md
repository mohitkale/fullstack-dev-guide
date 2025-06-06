# Java 8+ Features

## Table of Contents
1. [Lambda Expressions](#lambda-expressions)
2. [Functional Interfaces](#functional-interfaces)
3. [Method References](#method-references)
4. [Streams API](#streams-api)
5. [Optional Class](#optional-class)
6. [Default and Static Methods in Interfaces](#default-and-static-methods-in-interfaces)
7. [New Date/Time API](#new-datetime-api)
8. [Java 9-17 Features (Overview)](#java-9-17-features-overview)
9. [Practice Exercises](#practice-exercises-)
10. [Common Interview Questions](#common-interview-questions)

## Lambda Expressions

Lambda expressions provide a clear and concise way to represent a method interface using an expression. They enable you to treat functionality as a method argument.

### Syntax
```java
(parameters) -> expression
```
or
```java
(parameters) -> { statements; }
```

### Examples
```java
import java.util.Arrays;
import java.util.List;
import java.util.Collections;
import java.util.Comparator;

public class LambdaExamples {
    public static void main(String[] args) {
        // Example 1: Runnable with lambda
        Runnable r1 = () -> System.out.println("Hello Lambda!");
        new Thread(r1).start();
        
        // Example 2: Sorting with lambda
        List<String> names = Arrays.asList("Rahul", "Priya", "Amit", "Zoya");
        
        // Before Java 8
        Collections.sort(names, new Comparator<String>() {
            @Override
            public int compare(String a, String b) {
                return a.compareTo(b);
            }
        });
        
        // With Lambda
        Collections.sort(names, (a, b) -> a.compareTo(b));
        
        // Even better with method reference
        Collections.sort(names, String::compareTo);
        
        System.out.println("Sorted names: " + names);
        
        // Example 3: Custom functional interface
        MathOperation addition = (a, b) -> a + b;
        MathOperation subtraction = (a, b) -> a - b;
        
        System.out.println("10 + 5 = " + operate(10, 5, addition));
        System.out.println("10 - 5 = " + operate(10, 5, subtraction));
    }
    
    interface MathOperation {
        int operation(int a, int b);
    }
    
    private static int operate(int a, int b, MathOperation mathOperation) {
        return mathOperation.operation(a, b);
    }
}
```

## Functional Interfaces

A functional interface is an interface with a single abstract method (SAM). Java 8 provides several built-in functional interfaces in the `java.util.function` package.

### Common Functional Interfaces

| Interface        | Method          | Description                              |
|-----------------|-----------------|------------------------------------------|
| `Predicate<T>`  | `test(T t)`     | Takes one argument, returns boolean      |
| `Function<T,R>` | `apply(T t)`    | Takes one argument, produces a result    |
| `Consumer<T>`   | `accept(T t)`   | Takes one argument, returns no result    |
| `Supplier<T>`   | `get()`         | Takes no arguments, returns a result     |
| `UnaryOperator<T>` | `apply(T t)` | Takes one argument, returns same type    |
| `BinaryOperator<T>` | `apply(T t1, T t2)` | Takes two arguments, returns same type |


### Examples
```java
import java.util.function.*;
import java.util.*;

public class FunctionalInterfacesExample {
    public static void main(String[] args) {
        // Predicate: Tests a condition
        Predicate<String> isLongName = name -> name.length() > 5;
        System.out.println(isLongName.test("Rahul"));    // false
        System.out.println(isLongName.test("Priyanka")); // true
        
        // Function: Transforms data
        Function<String, Integer> nameLength = String::length;
        System.out.println(nameLength.apply("Amit")); // 4
        
        // Consumer: Performs an operation
        Consumer<String> greet = name -> System.out.println("Hello, " + name);
        greet.accept("Rahul");
        
        // Supplier: Provides a value
        Supplier<Double> randomValue = Math::random;
        System.out.println("Random value: " + randomValue.get());
        
        // Method references
        List<String> names = Arrays.asList("Rahul", "Priya", "Amit", "Zoya");
        names.forEach(System.out::println);
    }
}
```

## Method References

Method references are a shorthand notation of a lambda expression to call a method.

### Types of Method References
1. Reference to a static method: `ContainingClass::staticMethodName`
2. Reference to an instance method of a particular object: `object::instanceMethodName`
3. Reference to an instance method of an arbitrary object of a particular type: `ContainingType::methodName`
4. Reference to a constructor: `ClassName::new`

### Examples
```java
import java.util.*;
import java.util.function.*;

public class MethodReferenceExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Rahul", "Priya", "Amit", "Zoya");
        
        // 1. Static method reference
        names.forEach(System.out::println);
        
        // 2. Instance method reference of a particular object
        StringSorter sorter = new StringSorter();
        names.sort(sorter::compareByLength);
        System.out.println("Sorted by length: " + names);
        
        // 3. Instance method reference of an arbitrary object
        names.sort(String::compareToIgnoreCase);
        System.out.println("Case-insensitive sort: " + names);
        
        // 4. Constructor reference
        Supplier<List<String>> listSupplier = ArrayList::new;
        List<String> newList = listSupplier.get();
        newList.add("New element");
        System.out.println("New list: " + newList);
    }
}

class StringSorter {
    public int compareByLength(String a, String b) {
        return Integer.compare(a.length(), b.length());
    }
}
```

## Streams API

The Streams API is used to process collections of objects in a functional programming style.

### Stream Operations
1. **Intermediate Operations**: Return a new stream and are always lazy
   - `filter()`, `map()`, `flatMap()`, `distinct()`, `sorted()`, `peek()`, `limit()`, `skip()`

2. **Terminal Operations**: Produce a result or side-effect
   - `forEach()`, `collect()`, `reduce()`, `count()`, `min()`, `max()`, `anyMatch()`, `allMatch()`, `noneMatch()`, `findFirst()`, `findAny()`

### Examples
```java
import java.util.*;
import java.util.stream.*;

public class StreamsExample {
    public static void main(String[] args) {
        List<Student> students = Arrays.asList(
            new Student("Rahul", 85, "Computer Science"),
            new Student("Priya", 92, "Mathematics"),
            new Student("Amit", 78, "Physics"),
            new Student("Zoya", 91, "Computer Science"),
            new Student("Kiran", 82, "Mathematics")
        );
        
        // 1. Filter and forEach
        System.out.println("Computer Science students:");
        students.stream()
               .filter(s -> s.getDepartment().equals("Computer Science"))
               .forEach(s -> System.out.println(s.getName()));
        
        // 2. Map to get names and collect to list
        List<String> names = students.stream()
                                   .map(Student::getName)
                                   .collect(Collectors.toList());
        System.out.println("All student names: " + names);
        
        // 3. Calculate average marks
        double averageMarks = students.stream()
                                    .mapToInt(Student::getMarks)
                                    .average()
                                    .orElse(0.0);
        System.out.println("Average marks: " + averageMarks);
        
        // 4. Group by department
        Map<String, List<Student>> byDepartment = students.stream()
                                                         .collect(Collectors.groupingBy(Student::getDepartment));
        System.out.println("Students by department: " + byDepartment);
        
        // 5. Find top student
        students.stream()
               .max(Comparator.comparing(Student::getMarks))
               .ifPresent(s -> System.out.println("Top student: " + s.getName()));
    }
}

class Student {
    private String name;
    private int marks;
    private String department;
    
    public Student(String name, int marks, String department) {
        this.name = name;
        this.marks = marks;
        this.department = department;
    }
    
    // Getters
    public String getName() { return name; }
    public int getMarks() { return marks; }
    public String getDepartment() { return department; }
    
    @Override
    public String toString() {
        return name + " (" + marks + ")";
    }
}
```

## Optional Class

The `Optional` class is a container object that may or may not contain a non-null value.

### Examples
```java
import java.util.Optional;

public class OptionalExample {
    public static void main(String[] args) {
        // Creating Optional objects
        Optional<String> empty = Optional.empty();
        Optional<String> name = Optional.of("Rahul");
        // Optional<String> nullName = Optional.of(null); // Throws NullPointerException
        Optional<String> nullableName = Optional.ofNullable(null);
        
        // Check if value is present
        System.out.println("Is name present? " + name.isPresent()); // true
        System.out.println("Is empty present? " + empty.isPresent()); // false
        
        // Get the value if present
        name.ifPresent(n -> System.out.println("Hello, " + n));
        
        // Default value with orElse
        String nullName = null;
        String result = Optional.ofNullable(nullName).orElse("Default Name");
        System.out.println("Result: " + result); // Default Name
        
        // Throw exception if value is not present
        try {
            String value = empty.orElseThrow(() -> new RuntimeException("Value not present"));
        } catch (RuntimeException e) {
            System.out.println("Error: " + e.getMessage());
        }
        
        // Filter and map
        Optional<String> longName = name.filter(n -> n.length() > 5);
        System.out.println("Is name longer than 5? " + longName.isPresent());
        
        // Map to get the length
        Optional<Integer> nameLength = name.map(String::length);
        System.out.println("Name length: " + nameLength.orElse(0));
    }
}
```

## Default and Static Methods in Interfaces

Java 8 introduced default and static methods in interfaces to support backward compatibility and add utility methods.

### Examples
```java
public class InterfaceMethodsExample {
    public static void main(String[] args) {
        // Using default method
        Vehicle car = new Car();
        car.honk(); // Default implementation
        
        // Using static method
        Vehicle.repair();
        
        // Multiple inheritance with default methods
        FlyingCar flyingCar = new FlyingCar();
        flyingCar.drive(); // From Car
        flyingCar.fly();   // From Aircraft
        flyingCar.takeOff(); // Overridden in FlyingCar
    }
}

interface Vehicle {
    // Abstract method
    void drive();
    
    // Default method
    default void honk() {
        System.out.println("Vehicle is honking!");
    }
    
    // Static method
    static void repair() {
        System.out.println("Repairing vehicle...");
    }
}

interface Aircraft {
    void fly();
    
    default void takeOff() {
        System.out.println("Aircraft is taking off...");
    }
}

class Car implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Car is driving...");
    }
}

class FlyingCar implements Vehicle, Aircraft {
    @Override
    public void drive() {
        System.out.println("Flying car is driving...");
    }
    
    @Override
    public void fly() {
        System.out.println("Flying car is flying...");
    }
    
    // Resolving multiple inheritance conflict
    @Override
    public void takeOff() {
        Vehicle.super.honk();
        Aircraft.super.takeOff();
        System.out.println("Flying car is taking off...");
    }
}
```

## New Date/Time API

Java 8 introduced a new Date/Time API in the `java.time` package to address the shortcomings of the old `Date` and `Calendar` classes.

### Examples
```java
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.time.temporal.ChronoUnit;

public class DateTimeExample {
    public static void main(String[] args) {
        // Current date and time
        LocalDate today = LocalDate.now();
        LocalTime now = LocalTime.now();
        LocalDateTime currentDateTime = LocalDateTime.now();
        
        System.out.println("Today: " + today);
        System.out.println("Now: " + now);
        System.out.println("Current date and time: " + currentDateTime);
        
        // Creating specific dates
        LocalDate independenceDay = LocalDate.of(1947, Month.AUGUST, 15);
        System.out.println("Indian Independence Day: " + independenceDay);
        
        // Parsing dates
        LocalDate parsedDate = LocalDate.parse("2000-01-01");
        System.out.println("Parsed date: " + parsedDate);
        
        // Formatting dates
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy");
        String formattedDate = today.format(formatter);
        System.out.println("Formatted date: " + formattedDate);
        
        // Date arithmetic
        LocalDate nextWeek = today.plus(1, ChronoUnit.WEEKS);
        System.out.println("Next week: " + nextWeek);
        
        // Period between dates
        Period period = Period.between(independenceDay, today);
        System.out.printf("Years since independence: %d years, %d months, %d days%n",
                         period.getYears(), period.getMonths(), period.getDays());
        
        // Time zones
        ZonedDateTime zonedDateTime = ZonedDateTime.now(ZoneId.of("Asia/Kolkata"));
        System.out.println("Current time in India: " + zonedDateTime);
        
        // Duration
        LocalTime start = LocalTime.of(9, 0);
        LocalTime end = LocalTime.of(17, 30);
        Duration duration = Duration.between(start, end);
        System.out.println("Working hours: " + duration.toHours() + " hours");
    }
}
```

## Java 9-17 Features (Overview)

### Java 9
- Module system (JPMS)
- Factory methods for collections: `List.of()`, `Set.of()`, `Map.of()`
- Private methods in interfaces
- `Stream` API improvements: `takeWhile()`, `dropWhile()`, `ofNullable()`

### Java 10
- Local variable type inference with `var`
- `Optional.orElseThrow()`
- `List.copyOf()`, `Set.copyOf()`, `Map.copyOf()`

### Java 11
- `var` in lambda parameters
- New string methods: `isBlank()`, `lines()`, `strip()`, `repeat()`
- `Files.readString()` and `Files.writeString()`
- Running Java files directly without compilation

### Java 12-17
- Switch expressions (preview in 12-14, standard in 15+)
- Text blocks (preview in 13-14, standard in 15+)
- Records (preview in 14-15, standard in 16+)
- Sealed classes (preview in 15-16, standard in 17+)
- Pattern matching for `instanceof` (preview in 16, standard in 17+)

## Practice Exercises 📝

1. **Employee Management System**
   - Create a list of employees with name, department, and salary
   - Use Streams to find the highest-paid employee in each department
   - Calculate the average salary of employees with more than 5 years of experience
   - Group employees by department and calculate statistics (min, max, avg salary)

2. **Date/Time Utilities**
   - Create a method to find all Sundays in the current month
   - Calculate the number of working days between two dates (excluding weekends and holidays)
   - Implement a method to format a `LocalDateTime` in a human-readable format (e.g., "2 hours ago")

3. **Optional Chaining**
   - Create a class hierarchy with nested optional fields
   - Implement a method to safely access deeply nested fields using `Optional`
   - Provide meaningful default values when fields are not present

## Common Interview Questions

1. What are the main features introduced in Java 8?
2. What is the difference between `map()` and `flatMap()` in Streams?
3. How does the `Optional` class help prevent `NullPointerException`?
4. What is the difference between `Collection.stream().forEach()` and `Collection.forEach()`?
5. Explain the difference between `Predicate`, `Function`, and `Consumer` interfaces.
6. What are method references in Java 8? Provide examples.
7. How do default methods in interfaces help with backward compatibility?
8. What are the advantages of the new Date/Time API over the old `Date` and `Calendar` classes?
9. What is the difference between `Stream.iterate()` and `Stream.generate()`?
10. How does the `CompletableFuture` class improve upon the old `Future` interface?
11. What are the benefits of the module system introduced in Java 9?
12. What are records in Java 14+ and when would you use them?
13. Explain pattern matching for `instanceof` in Java 16+.
14. What are sealed classes in Java 17 and what problem do they solve?
15. How does the `var` keyword work in Java 10+ and when should you use it?

🔙 [Previous: Design Patterns](../7-design-patterns/README.md)
