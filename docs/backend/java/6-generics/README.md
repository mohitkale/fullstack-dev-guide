# Generics in Java

## Table of Contents
1. [Introduction to Generics](#introduction-to-generics)
2. [Generic Classes](#generic-classes)
3. [Generic Methods](#generic-methods)
4. [Bounded Type Parameters](#bounded-type-parameters)
5. [Wildcards](#wildcards)
6. [Type Erasure](#type-erasure)
7. [Practice Exercises](#practice-exercises-)
8. [Common Interview Questions](#common-interview-questions)

## Introduction to Generics

Generics were introduced in Java 5 to provide compile-time type safety and eliminate the need for explicit type casting. They allow you to write code that can work with different data types.

### Benefits of Generics:
- **Type Safety**: Catches type mismatches at compile time
- **Code Reusability**: Write code that works with any data type
- **No Type Casting**: Eliminates the need for explicit type casting
- **Better Readability**: Makes the code more readable and maintainable

### Before Generics (Java 1.4 and earlier)
```java
// Non-generic code
List list = new ArrayList();
list.add("Hello");
String s = (String) list.get(0);  // Explicit casting needed
```

### With Generics (Java 5+)
```java
// Generic code
List<String> list = new ArrayList<>();
list.add("Hello");
String s = list.get(0);  // No casting needed
```

## Generic Classes

A generic class is a class that can work with any data type.

### Basic Generic Class
```java
// Generic class with a single type parameter
class Box<T> {
    private T content;
    
    public void setContent(T content) {
        this.content = content;
    }
    
    public T getContent() {
        return content;
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        // Integer Box
        Box<Integer> integerBox = new Box<>();
        integerBox.setContent(10);
        int value = integerBox.getContent();  // No casting needed
        
        // String Box
        Box<String> stringBox = new Box<>();
        stringBox.setContent("Hello, Generics!");
        String text = stringBox.getContent();  // No casting needed
    }
}
```

### Multiple Type Parameters
```java
class Pair<K, V> {
    private K key;
    private V value;
    
    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }
    
    public K getKey() { return key; }
    public V getValue() { return value; }
    
    public void setKey(K key) { this.key = key; }
    public void setValue(V value) { this.value = value; }
    
    @Override
    public String toString() {
        return "Pair{" + "key=" + key + ", value=" + value + '}';
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        // Pair of String and Integer
        Pair<String, Integer> studentMarks = new Pair<>("Rahul", 85);
        System.out.println(studentMarks);
        
        // Pair of Integer and String
        Pair<Integer, String> rollName = new Pair<>(101, "Priya Patel");
        System.out.println(rollName);
    }
}
```

## Generic Methods

Generic methods are methods that introduce their own type parameters.

### Basic Generic Method
```java
public class GenericMethodExample {
    // Generic method to print array elements
    public static <T> void printArray(T[] array) {
        for (T element : array) {
            System.out.print(element + " ");
        }
        System.out.println();
    }
    
    public static void main(String[] args) {
        // Array of integers
        Integer[] intArray = {1, 2, 3, 4, 5};
        System.out.print("Integer Array: ");
        printArray(intArray);
        
        // Array of strings
        String[] stringArray = {"Hello", "World", "Generics"};
        System.out.print("String Array: ");
        printArray(stringArray);
        
        // Array of doubles
        Double[] doubleArray = {1.1, 2.2, 3.3, 4.4};
        System.out.print("Double Array: ");
        printArray(doubleArray);
    }
}
```

### Generic Method with Multiple Type Parameters
```java
public class MultipleTypeParams {
    // Generic method with two type parameters
    public static <T, U> void printPair(T first, U second) {
        System.out.println("First: " + first + ", Second: " + second);
    }
    
    public static void main(String[] args) {
        // Different types
        printPair("Age", 25);  // String and Integer
        printPair(3.14, "Pi"); // Double and String
        printPair(10, true);   // Integer and Boolean
    }
}
```

## Bounded Type Parameters

Bounded type parameters allow you to restrict the types that can be used as type arguments.

### Single Bound
```java
// Only accepts Number or its subclasses (Integer, Double, etc.)
class NumberBox<T extends Number> {
    private T number;
    
    public NumberBox(T number) {
        this.number = number;
    }
    
    public double square() {
        return number.doubleValue() * number.doubleValue();
    }
    
    public boolean isEven() {
        return number.intValue() % 2 == 0;
    }
}

// Usage
public class BoundedTypeDemo {
    public static void main(String[] args) {
        NumberBox<Integer> intBox = new NumberBox<>(5);
        System.out.println("Square: " + intBox.square());
        System.out.println("Is even? " + intBox.isEven());
        
        NumberBox<Double> doubleBox = new NumberBox<>(3.14);
        System.out.println("Square: " + doubleBox.square());
        
        // This would cause a compilation error
        // NumberBox<String> stringBox = new NumberBox<>("Hello");
    }
}
```

### Multiple Bounds
```java
// T must be a class that implements both Comparable and Serializable
class GenericClass<T extends Comparable<T> & Serializable> {
    private T value;
    
    public GenericClass(T value) {
        this.value = value;
    }
    
    public boolean isGreaterThan(T other) {
        return value.compareTo(other) > 0;
    }
}
```

## Wildcards

Wildcards are used to represent an unknown type in generic code.

### Unbounded Wildcard
```java
// Accepts any type of List
public static void printList(List<?> list) {
    for (Object elem : list) {
        System.out.print(elem + " ");
    }
    System.out.println();
}
```

### Upper Bounded Wildcard
```java
// Accepts List of Number or its subclasses
public static double sumOfList(List<? extends Number> list) {
    double sum = 0.0;
    for (Number num : list) {
        sum += num.doubleValue();
    }
    return sum;
}
```

### Lower Bounded Wildcard
```java
// Accepts List of Integer or its superclasses
public static void addNumbers(List<? super Integer> list) {
    for (int i = 1; i <= 5; i++) {
        list.add(i);
    }
}
```

### Example with Wildcards
```java
import java.util.*;

public class WildcardExample {
    // Upper bounded wildcard
    public static double sumOfList(List<? extends Number> list) {
        double sum = 0.0;
        for (Number num : list) {
            sum += num.doubleValue();
        }
        return sum;
    }
    
    // Lower bounded wildcard
    public static void addIntegers(List<? super Integer> list) {
        list.add(1);
        list.add(2);
        list.add(3);
    }
    
    // Unbounded wildcard
    public static void printAll(List<?> list) {
        for (Object item : list) {
            System.out.print(item + " ");
        }
        System.out.println();
    }
    
    public static void main(String[] args) {
        // Test upper bounded wildcard
        List<Integer> intList = Arrays.asList(1, 2, 3, 4, 5);
        System.out.println("Sum of integers: " + sumOfList(intList));
        
        List<Double> doubleList = Arrays.asList(1.1, 2.2, 3.3);
        System.out.println("Sum of doubles: " + sumOfList(doubleList));
        
        // Test lower bounded wildcard
        List<Number> numberList = new ArrayList<>();
        addIntegers(numberList);
        System.out.println("Number list: " + numberList);
        
        // Test unbounded wildcard
        printAll(intList);
        printAll(doubleList);
        printAll(numberList);
    }
}
```

## Type Erasure

Generics in Java are implemented using type erasure, which means that all type parameters are removed during compilation and replaced with their bounds or `Object` if no bound is specified.

### How Type Erasure Works
```java
// Generic class
class Box<T> {
    private T value;
    public void set(T value) { this.value = value; }
    public T get() { return value; }
}

// After type erasure
/*
class Box {
    private Object value;
    public void set(Object value) { this.value = value; }
    public Object get() { return value; }
}
*/
```

### Bridge Methods
When a generic class extends or implements a generic class/interface, the compiler generates bridge methods to maintain polymorphism.

```java
interface Comparable<T> {
    int compareTo(T o);
}

class MyClass implements Comparable<MyClass> {
    private int value;
    
    public int compareTo(MyClass other) {
        return Integer.compare(this.value, other.value);
    }
}

// The compiler generates a bridge method:
/*
public int compareTo(Object o) {
    return compareTo((MyClass) o);  // Calls the type-specific method
}
*/
```

## Practice Exercises 📝

1. **Generic Stack**
   - Create a generic `Stack` class with `push()`, `pop()`, `peek()`, and `isEmpty()` methods
   - Test it with different data types (Integer, String, etc.)

2. **Pair Class**
   - Create a generic `Pair` class that can hold two values of potentially different types
   - Implement `equals()`, `hashCode()`, and `toString()` methods
   - Add a static factory method to create an immutable pair

3. **Generic Search**
   - Write a generic method to search for an element in an array
   - The method should return the index of the element or -1 if not found
   - Add support for a custom `Comparator`

4. **Bounded Type Exercise**
   - Create a generic class `NumberOperations` that works with `Number` and its subclasses
   - Add methods to calculate sum, average, min, and max of a list of numbers

## Common Interview Questions

1. What are generics in Java?
2. What is type erasure?
3. What is the difference between `List<?>` and `List<Object>`?
4. What is the difference between `List<? extends Number>` and `List<? super Number>`?
5. Can you create an instance of a generic type?
6. What is a raw type and why is it not recommended to use it?
7. What is the difference between `List` and `List<?>`?
8. Can you use primitives as type parameters in Java generics?
9. What is the purpose of the diamond operator `<>`?
10. How do you write a generic method that takes an array of any type and returns a List?

🔙 [Previous: Collections Framework](../5-collections-framework/README.md) | 
🔜 [Next: Design Patterns →](../7-design-patterns/README.md)
