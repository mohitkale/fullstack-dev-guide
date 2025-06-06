# Exception Handling in Java

## Table of Contents
1. [Introduction to Exceptions](#introduction-to-exceptions)
2. [Types of Exceptions](#types-of-exceptions)
3. [Try-Catch-Finally Block](#try-catch-finally-block)
4. [Throwing Exceptions](#throwing-exceptions)
5. [Custom Exceptions](#custom-exceptions)
6. [Best Practices](#best-practices)
7. [Practice Exercises](#practice-exercises-)
8. [Common Interview Questions](#common-interview-questions)

## Introduction to Exceptions

An exception is an event that disrupts the normal flow of a program's execution. In real-world terms, think of exceptions as unexpected situations that occur while a program is running.

### Real-world Analogy
Imagine you're traveling by train in India:
- **Normal flow**: Board train → Travel → Reach destination
- **Exception scenarios**:
  - Train gets cancelled (NullPointerException)
  - You board the wrong train (IllegalArgumentException)
  - You miss your train (TimeoutException)

## Types of Exceptions

### 1. Checked Exceptions (Compile-time)
- Must be handled at compile time
- Extend `Exception` class
- Examples: `IOException`, `SQLException`, `ClassNotFoundException`

### 2. Unchecked Exceptions (Runtime)
- Not checked at compile time
- Extend `RuntimeException`
- Examples: `NullPointerException`, `ArrayIndexOutOfBoundsException`, `ArithmeticException`

### 3. Errors
- Serious problems that applications should not try to catch
- Examples: `OutOfMemoryError`, `StackOverflowError`

## Try-Catch-Finally Block

### Basic Syntax
```java
try {
    // Code that might throw an exception
} catch (ExceptionType1 e1) {
    // Handle ExceptionType1
} catch (ExceptionType2 e2) {
    // Handle ExceptionType2
} finally {
    // Code that always executes (optional)
}
```

### Example: Handling Division by Zero
```java
public class DivisionExample {
    public static void main(String[] args) {
        int numerator = 10;
        int denominator = 0;
        
        try {
            int result = numerator / denominator;  // This will throw ArithmeticException
            System.out.println("Result: " + result);
        } catch (ArithmeticException e) {
            System.out.println("Error: Cannot divide by zero!");
            System.out.println("Exception details: " + e.getMessage());
        } finally {
            System.out.println("This block always executes");
        }
        
        System.out.println("Program continues...");
    }
}
```

### Multiple Catch Blocks
```java
public class MultipleCatchExample {
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3};
        
        try {
            // This will throw ArrayIndexOutOfBoundsException
            System.out.println(numbers[5]);
            
            // This line won't execute if exception occurs above
            int result = 10 / 0;
            
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Error: Array index out of bounds!");
        } catch (ArithmeticException e) {
            System.out.println("Error: Division by zero!");
        } catch (Exception e) {
            System.out.println("Some other error occurred: " + e.getMessage());
        }
    }
}
```

### Try-With-Resources (Java 7+)
Automatically closes resources like files, database connections, etc.

```java
import java.io.*;

public class TryWithResourcesExample {
    public static void main(String[] args) {
        // File path - update this to a real file path on your system
        String filePath = "/Users/username/documents/example.txt";
        
        // Try-with-resources automatically closes the BufferedReader
        try (FileReader fileReader = new FileReader(filePath);
             BufferedReader bufferedReader = new BufferedReader(fileReader)) {
            
            String line;
            while ((line = bufferedReader.readLine()) != null) {
                System.out.println(line);
            }
            
        } catch (FileNotFoundException e) {
            System.out.println("Error: File not found at " + filePath);
        } catch (IOException e) {
            System.out.println("Error reading file: " + e.getMessage());
        }
    }
}
```

## Throwing Exceptions

### Throwing Built-in Exceptions
```java
public class BankAccount {
    private double balance;
    
    public void withdraw(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }
        
        if (amount > balance) {
            throw new InsufficientFundsException("Insufficient balance");
        }
        
        balance -= amount;
    }
    
    public double getBalance() {
        return balance;
    }
}

public class Main {
    public static void main(String[] args) {
        BankAccount account = new BankAccount();
        
        try {
            account.withdraw(-100);  // Will throw IllegalArgumentException
        } catch (IllegalArgumentException e) {
            System.out.println("Invalid amount: " + e.getMessage());
        } catch (InsufficientFundsException e) {
            System.out.println("Transaction failed: " + e.getMessage());
        }
    }
}
```

### Throwing with Custom Messages
```java
public class AgeValidator {
    public static void validateVoterAge(int age) {
        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }
        
        if (age < 18) {
            throw new ArithmeticException("Person is not eligible to vote. Minimum age is 18.");
        }
        
        System.out.println("Person is eligible to vote!");
    }
    
    public static void main(String[] args) {
        try {
            validateVoterAge(-5);  // Will throw IllegalArgumentException
        } catch (IllegalArgumentException | ArithmeticException e) {
            System.out.println("Validation error: " + e.getMessage());
        }
    }
}
```

## Custom Exceptions

### Creating a Custom Exception
```java
// Custom checked exception
class InsufficientFundsException extends Exception {
    public InsufficientFundsException() {
        super();
    }
    
    public InsufficientFundsException(String message) {
        super(message);
    }
    
    public InsufficientFundsException(String message, Throwable cause) {
        super(message, cause);
    }
}

// Custom unchecked exception
class InvalidAgeException extends RuntimeException {
    public InvalidAgeException(String message) {
        super(message);
    }
}

// Using the custom exceptions
public class BankAccount {
    private double balance;
    
    public void withdraw(double amount) throws InsufficientFundsException {
        if (amount > balance) {
            throw new InsufficientFundsException("Insufficient balance. Current balance: " + balance);
        }
        balance -= amount;
    }
    
    public void setAge(int age) {
        if (age < 0 || age > 120) {
            throw new InvalidAgeException("Invalid age: " + age + ". Age must be between 0 and 120.");
        }
        // Set the age
    }
}
```

## Best Practices

1. **Be specific with exception types**
   ```java
   // Bad
   try {
       // code
   } catch (Exception e) {
       // too broad
   }
   
   // Good
   try {
       // code
   } catch (FileNotFoundException e) {
       // handle file not found
   } catch (IOException e) {
       // handle other I/O errors
   }
   ```

2. **Don't swallow exceptions**
   ```java
   // Bad
   try {
       // code that might fail
   } catch (Exception e) {
       // empty catch block!
   }
   
   // Good
   try {
       // code that might fail
   } catch (Exception e) {
       log.error("An error occurred: " + e.getMessage(), e);
       // or rethrow
       throw new CustomException("Failed to process request", e);
   }
   ```

3. **Use finally for cleanup**
   ```java
   Connection conn = null;
   try {
       conn = getConnection();
       // use connection
   } catch (SQLException e) {
       // handle exception
   } finally {
       if (conn != null) {
           try {
               conn.close();
           } catch (SQLException e) {
               // log but don't throw from finally
               log.error("Error closing connection", e);
           }
       }
   }
   ```

4. **Prefer try-with-resources**
   ```java
   // Better than manual finally block
   try (Connection conn = getConnection();
        Statement stmt = conn.createStatement();
        ResultSet rs = stmt.executeQuery("SELECT * FROM users")) {
       
       // use resources
   } catch (SQLException e) {
       // handle exception
   }
   ```

5. **Document exceptions with JavaDoc**
   ```java
   /**
    * Withdraws the specified amount from the account.
    * 
    * @param amount the amount to withdraw
    * @throws InsufficientFundsException if the account has insufficient funds
    * @throws IllegalArgumentException if the amount is negative
    */
   public void withdraw(double amount) throws InsufficientFundsException {
       if (amount < 0) {
           throw new IllegalArgumentException("Amount cannot be negative");
       }
       if (amount > balance) {
           throw new InsufficientFundsException("Insufficient balance");
       }
       balance -= amount;
   }
   ```

## Practice Exercises 📝

1. **Age Validator**
   - Create a method that validates a person's age
   - Throw a custom exception if age is negative or greater than 120
   - Handle the exception in the calling method

2. **File Processor**
   - Create a method that reads a file and counts the number of words
   - Handle `FileNotFoundException` and `IOException`
   - Use try-with-resources

3. **Bank Account**
   - Create a `BankAccount` class with `withdraw` and `deposit` methods
   - Throw appropriate exceptions for invalid amounts and insufficient funds
   - Create custom exceptions for business rules

## Common Interview Questions

1. What is the difference between checked and unchecked exceptions?
2. What is the difference between `throw` and `throws` in Java?
3. What is the purpose of the `finally` block?
4. What is the difference between `final`, `finally`, and `finalize`?
5. What is a `try-with-resources` statement?
6. What is the difference between `Error` and `Exception`?
7. Can we have a `try` block without a `catch` or `finally` block?
8. What is exception propagation in Java?
9. What is the difference between `printStackTrace()` and `getMessage()`?
10. What are the best practices for exception handling in Java?

🔙 [Previous: OOP Concepts](../3-oop-concepts/README.md) | 
🔜 [Next: Collections Framework →](../5-collections-framework/README.md)
