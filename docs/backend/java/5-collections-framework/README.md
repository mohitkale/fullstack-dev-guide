# Java Collections Framework

## Table of Contents
1. [Introduction to Collections](#introduction-to-collections)
2. [Core Collection Interfaces](#core-collection-interfaces)
3. [List Implementations](#list-implementations)
4. [Set Implementations](#set-implementations)
5. [Map Implementations](#map-implementations)
6. [Queue Implementations](#queue-implementations)
7. [Utility Classes](#utility-classes)
8. [Java 8 Stream API](#java-8-stream-api)
9. [Practice Exercises](#practice-exercises-)
10. [Common Interview Questions](#common-interview-questions)

## Introduction to Collections

Collections in Java are containers that group multiple items into a single unit. They are used to store, retrieve, manipulate, and communicate aggregate data.

### Why Use Collections?
- Reduces programming effort
- Increases program speed and quality
- Allows interoperability among unrelated APIs
- Reduces effort to learn and use new APIs
- Reduces effort to design new APIs

### Collection Framework Hierarchy
```
                  Collection                Map
               /     |     \                |
              /      |      \               |
          List      Set     Queue         SortedMap
          / \       / \       |
         /   \     /   \      |
  ArrayList LinkedList  HashSet  PriorityQueue
```

## Core Collection Interfaces

### 1. Collection Interface
Root interface in the collection hierarchy. Defines the most common methods:
- `add()`, `remove()`, `contains()`, `size()`, `isEmpty()`, `clear()`

### 2. List Interface
- Ordered collection (sequence)
- Allows duplicate elements
- Access by index
- Main implementations: `ArrayList`, `LinkedList`, `Vector`

### 3. Set Interface
- No duplicate elements
- No ordering (unless using `LinkedHashSet` or `TreeSet`)
- Main implementations: `HashSet`, `LinkedHashSet`, `TreeSet`

### 4. Queue Interface
- Represents a waiting line
- Typically orders elements in FIFO (First-In-First-Out)
- Main implementations: `LinkedList`, `PriorityQueue`

### 5. Map Interface
- Maps keys to values
- No duplicate keys
- Each key maps to at most one value
- Main implementations: `HashMap`, `LinkedHashMap`, `TreeMap`, `Hashtable`

## List Implementations

### 1. ArrayList
- Resizable array implementation
- Fast random access (O(1))
- Slow insertions/deletions in the middle (O(n))
- Not synchronized

```java
import java.util.ArrayList;
import java.util.List;

public class ArrayListExample {
    public static void main(String[] args) {
        // Create a list of Indian cities
        List<String> cities = new ArrayList<>();
        
        // Add elements
        cities.add("Mumbai");
        cities.add("Delhi");
        cities.add("Bangalore");
        cities.add("Hyderabad");
        
        // Access elements
        System.out.println("First city: " + cities.get(0));
        
        // Iterate using for-each
        System.out.println("\nAll cities:");
        for (String city : cities) {
            System.out.println("- " + city);
        }
        
        // Check if list contains an element
        System.out.println("\nContains 'Mumbai'? " + cities.contains("Mumbai"));
        
        // Remove an element
        cities.remove("Delhi");
        System.out.println("\nAfter removing Delhi: " + cities);
    }
}
```

### 2. LinkedList
- Doubly-linked list implementation
- Fast insertions/deletions (O(1) at beginning/end, O(n) in middle)
- Slow random access (O(n))
- Implements both `List` and `Queue`

```java
import java.util.LinkedList;
import java.util.Queue;

public class LinkedListExample {
    public static void main(String[] args) {
        // LinkedList as a Queue
        Queue<String> bankQueue = new LinkedList<>();
        
        // Add customers to the queue
        bankQueue.offer("Rahul");
        bankQueue.offer("Priya");
        bankQueue.offer("Amit");
        
        System.out.println("Customers in queue: " + bankQueue);
        
        // Serve customers
        while (!bankQueue.isEmpty()) {
            String customer = bankQueue.poll();
            System.out.println("Serving: " + customer);
        }
    }
}
```

## Set Implementations

### 1. HashSet
- Backed by a hash table (actually a `HashMap` instance)
- No ordering guaranteed
- Constant-time performance for basic operations (O(1))
- No duplicate elements

```java
import java.util.HashSet;
import java.util.Set;

public class HashSetExample {
    public static void main(String[] args) {
        // Create a set of unique mobile numbers
        Set<String> mobileNumbers = new HashSet<>();
        
        // Add numbers (duplicates will be ignored)
        mobileNumbers.add("+91 9876543210");
        mobileNumbers.add("+91 9876543210");  // Duplicate
        mobileNumbers.add("+91 9876543211");
        mobileNumbers.add("+91 9876543212");
        
        System.out.println("Total unique numbers: " + mobileNumbers.size());
        
        // Check if a number exists
        String numberToCheck = "+91 9876543210";
        System.out.println("Number " + numberToCheck + " exists: " + 
            mobileNumbers.contains(numberToCheck));
    }
}
```

### 2. LinkedHashSet
- Maintains insertion order
- Slower than `HashSet` but faster than `TreeSet`
- No duplicates

### 3. TreeSet
- Sorted set (natural ordering or by `Comparator`)
- Implements `NavigableSet`
- Operations are O(log n)

```java
import java.util.Set;
import java.util.TreeSet;

public class TreeSetExample {
    public static void main(String[] args) {
        // Create a sorted set of names
        Set<String> names = new TreeSet<>();
        
        // Add names (will be automatically sorted)
        names.add("Rahul");
        names.add("Priya");
        names.add("Amit");
        names.add("Zoya");
        names.add("Kiran");
        
        System.out.println("Names in sorted order: " + names);
        
        // First and last elements
        if (names instanceof TreeSet) {
            TreeSet<String> treeSet = (TreeSet<String>) names;
            System.out.println("First name: " + treeSet.first());
            System.out.println("Last name: " + treeSet.last());
        }
    }
}
```

## Map Implementations

### 1. HashMap
- Hash table based implementation
- No ordering of keys
- Permits one null key and multiple null values
- O(1) for basic operations (assuming no hash collisions)

```java
import java.util.HashMap;
import java.util.Map;

public class HashMapExample {
    public static void main(String[] args) {
        // Create a map of student roll numbers to names
        Map<Integer, String> students = new HashMap<>();
        
        // Add entries
        students.put(101, "Rahul Sharma");
        students.put(102, "Priya Patel");
        students.put(103, "Amit Kumar");
        students.put(104, "Zoya Khan");
        
        // Get a value
        String studentName = students.get(102);
        System.out.println("Student with roll no 102: " + studentName);
        
        // Check if a key exists
        System.out.println("Contains roll no 105? " + students.containsKey(105));
        
        // Iterate through all entries
        System.out.println("\nAll students:");
        for (Map.Entry<Integer, String> entry : students.entrySet()) {
            System.out.println("Roll No: " + entry.getKey() + ", Name: " + entry.getValue());
        }
    }
}
```

### 2. LinkedHashMap
- Maintains insertion order
- Slower than `HashMap` but faster than `TreeMap`
- Useful for LRU cache implementation

### 3. TreeMap
- Sorted by natural ordering of keys or by `Comparator`
- Implements `NavigableMap`
- Operations are O(log n)

```java
import java.util.Map;
import java.util.TreeMap;

public class TreeMapExample {
    public static void main(String[] args) {
        // Create a map of cities to their populations (in millions)
        Map<String, Double> cityPopulations = new TreeMap<>();
        
        // Add entries (will be sorted by city name)
        cityPopulations.put("Mumbai", 20.4);
        cityPopulations.put("Delhi", 18.7);
        cityPopulations.put("Bangalore", 12.3);
        cityPopulations.put("Hyderabad", 10.3);
        
        // Print all cities and populations in sorted order
        System.out.println("City Populations (in millions):");
        for (Map.Entry<String, Double> entry : cityPopulations.entrySet()) {
            System.out.printf("- %-12s: %.1f million%n", entry.getKey(), entry.getValue());
        }
        
        // Get the first and last city alphabetically
        if (cityPopulations instanceof TreeMap) {
            TreeMap<String, Double> treeMap = (TreeMap<String, Double>) cityPopulations;
            System.out.println("\nFirst city: " + treeMap.firstKey());
            System.out.println("Last city: " + treeMap.lastKey());
        }
    }
}
```

## Queue Implementations

### 1. PriorityQueue
- Elements are ordered based on natural ordering or `Comparator`
- Does not permit null elements
- Head of the queue is the least element

```java
import java.util.PriorityQueue;
import java.util.Queue;

public class PriorityQueueExample {
    public static void main(String[] args) {
        // Create a priority queue of patients (by priority)
        Queue<Patient> emergencyQueue = new PriorityQueue<>();
        
        // Add patients with different priorities
        emergencyQueue.offer(new Patient("Rahul", 3));  // Low priority
        emergencyQueue.offer(new Patient("Priya", 1));  // High priority
        emergencyQueue.offer(new Patient("Amit", 2));   // Medium priority
        
        // Process patients in priority order
        System.out.println("Treating patients in order of priority:");
        while (!emergencyQueue.isEmpty()) {
            System.out.println("Treating: " + emergencyQueue.poll());
        }
    }
}

class Patient implements Comparable<Patient> {
    private String name;
    private int priority;  // 1 = highest priority
    
    public Patient(String name, int priority) {
        this.name = name;
        this.priority = priority;
    }
    
    @Override
    public int compareTo(Patient other) {
        // Lower number means higher priority
        return Integer.compare(this.priority, other.priority);
    }
    
    @Override
    public String toString() {
        return name + " (Priority: " + priority + ")";
    }
}
```

## Utility Classes

### 1. Collections Class
Provides utility methods for working with collections:
- `sort()`, `shuffle()`, `reverse()`
- `synchronizedXxx()` - Creates thread-safe collections
- `unmodifiableXxx()` - Creates unmodifiable collections

```java
import java.util.*;

public class CollectionsUtilityExample {
    public static void main(String[] args) {
        List<String> cities = new ArrayList<>();
        Collections.addAll(cities, "Mumbai", "Delhi", "Bangalore", "Chennai", "Kolkata");
        
        // Sort the list
        Collections.sort(cities);
        System.out.println("Sorted: " + cities);
        
        // Reverse the list
        Collections.reverse(cities);
        System.out.println("Reversed: " + cities);
        
        // Shuffle the list
        Collections.shuffle(cities);
        System.out.println("Shuffled: " + cities);
        
        // Create an unmodifiable list
        List<String> readOnlyList = Collections.unmodifiableList(cities);
        // readOnlyList.add("Pune"); // Will throw UnsupportedOperationException
    }
}
```

### 2. Arrays Class
Provides utility methods for working with arrays:
- `sort()`, `binarySearch()`, `equals()`, `fill()`, `asList()`

```java
import java.util.Arrays;
import java.util.List;

public class ArraysUtilityExample {
    public static void main(String[] args) {
        // Sorting an array
        int[] numbers = {5, 2, 8, 1, 9};
        Arrays.sort(numbers);
        System.out.println("Sorted array: " + Arrays.toString(numbers));
        
        // Binary search
        int index = Arrays.binarySearch(numbers, 8);
        System.out.println("Index of 8: " + index);
        
        // Convert array to list
        String[] fruits = {"Mango", "Apple", "Banana"};
        List<String> fruitList = Arrays.asList(fruits);
        System.out.println("Fruit list: " + fruitList);
    }
}
```

## Java 8 Stream API

The Stream API is used to process collections of objects in a functional programming style.

### Basic Stream Operations
```java
import java.util.*;
import java.util.stream.*;

public class StreamExample {
    public static void main(String[] args) {
        List<String> cities = Arrays.asList("Mumbai", "Delhi", "Bangalore", "Chennai", "Kolkata");
        
        // Filter cities starting with 'B' and convert to uppercase
        List<String> filtered = cities.stream()
            .filter(city -> city.startsWith("B"))
            .map(String::toUpperCase)
            .collect(Collectors.toList());
        System.out.println("Cities starting with 'B': " + filtered);
        
        // Get total length of all city names
        int totalLength = cities.stream()
            .mapToInt(String::length)
            .sum();
        System.out.println("Total characters: " + totalLength);
        
        // Group cities by their first letter
        Map<Character, List<String>> grouped = cities.stream()
            .collect(Collectors.groupingBy(city -> city.charAt(0)));
        System.out.println("Grouped by first letter: " + grouped);
    }
}
```

## Practice Exercises 📝

1. **Student Grade Tracker**
   - Create a `Student` class with name and grade
   - Use a `TreeMap` to store students by their ID
   - Implement methods to add, remove, and find students
   - Calculate average grade of all students

2. **Unique Words Counter**
   - Read a text file (e.g., a book or article)
   - Count the frequency of each word using a `HashMap`
   - Display the top 10 most frequent words

3. **Task Scheduler**
   - Create a `PriorityQueue` of tasks with different priorities
   - Allow adding tasks with priorities
   - Process tasks in order of priority

## Common Interview Questions

1. What is the difference between `ArrayList` and `LinkedList`?
2. When would you use `HashMap` vs `TreeMap`?
3. What is the difference between `HashSet` and `TreeSet`?
4. How does `HashMap` work internally in Java?
5. What is the difference between `Comparable` and `Comparator`?
6. What is the difference between `List` and `Set`?
7. How do you sort a collection in Java?
8. What is the difference between `Iterator` and `ListIterator`?
9. What is the difference between `HashMap` and `Hashtable`?
10. How do you create an immutable collection in Java?
11. What is the purpose of the `equals()` and `hashCode()` methods?
12. What is the difference between `Collection` and `Collections`?
13. How does Java handle hash collisions in `HashMap`?
14. What is the difference between `Queue` and `Deque`?
15. How do you convert an array to `ArrayList` and vice versa?

🔙 [Previous: Exception Handling](../4-exception-handling/README.md) | 
🔜 [Next: Generics →](../6-generics/README.md)
