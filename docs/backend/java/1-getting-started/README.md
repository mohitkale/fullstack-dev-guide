# Getting Started with Java

## What is Java? ☕

Java is a high-level, object-oriented programming language developed by Sun Microsystems (now owned by Oracle). It's designed to be platform-independent, meaning Java programs can run on any device that has a Java Virtual Machine (JVM) installed.

### History Timeline (Indian Context)
- 1995: Java 1.0 released
- 2006: Java becomes open-source
- 2014: Java 8 (LTS) with Lambdas - Major update
- 2018: Java 11 (LTS) - Next major LTS
- 2021: Java 17 (LTS) - Current LTS version

## Why Java is Popular in India 🇮🇳
- High demand in IT services companies (TCS, Infosys, Wipro, etc.)
- Strong presence in banking and financial sectors
- Android app development
- Government projects and public sector undertakings (PSUs)

## Setting Up Your Development Environment

### 1. Install JDK (Java Development Kit)
For Indian users, I recommend using Eclipse Temurin JDK (previously AdoptOpenJDK):

1. Visit [Eclipse Temurin](https://adoptium.net/)
2. Download JDK 17 (LTS) for your operating system
3. Run the installer and follow the instructions
4. Verify installation by opening Command Prompt/Terminal and typing:
   ```bash
   java -version
   javac -version
   ```

### 2. Install an IDE (Integrated Development Environment)
For beginners, I recommend IntelliJ IDEA Community Edition (free):
1. Download from [jetbrains.com/idea](https://www.jetbrains.com/idea/download/)
2. Install with default settings

## Your First Java Program

Let's create a simple "Hello, India!" program:

1. Open IntelliJ IDEA
2. Create a new Java project
3. Create a new class called `HelloIndia`
4. Type the following code:

```java
public class HelloIndia {
    public static void main(String[] args) {
        System.out.println("Namaste India! 🇮🇳");
        System.out.println("Welcome to Java Programming!");
        
        // Let's print some Indian states
        String[] states = {"Maharashtra", "Tamil Nadu", "Uttar Pradesh", "Karnataka", "Gujarat"};
        System.out.println("\nSome Indian States:");
        for (String state : states) {
            System.out.println("- " + state);
        }
    }
}
```

5. Click the green Run button (▶️) or press Shift+F10

## Understanding the Code

- `public class HelloIndia`: Every Java program starts with a class definition
- `public static void main(String[] args)`: The entry point of any Java application
- `System.out.println()`: Prints text to the console
- `//`: Single-line comment
- `/* */`: Multi-line comment

## What is JVM? (Java Virtual Machine)

Think of JVM as a virtual computer that runs Java bytecode. It's what makes Java platform-independent:

1. You write `.java` files (source code)
2. `javac` compiles them to `.class` files (bytecode)
3. JVM executes the bytecode

## Common Issues & Solutions (Indian Context)

1. **Java not recognized**
   - Add Java to PATH during installation
   - Or set JAVA_HOME environment variable

2. **Version Mismatch**
   - Check with `java -version` and `javac -version`
   - They should show the same version

3. **IDE not detecting JDK**
   - Specify JDK path in IDE settings
   - Usually found under Project Structure > SDKs

## Practice Exercise 📝

1. Modify the HelloIndia program to print your name and city
2. Add a list of your favorite Indian dishes and print them
3. Try changing the text and see what happens
4. Add a method to calculate and print the sum of first 10 numbers

## Common Interview Questions (Fresher Level)

1. What is Java and why is it platform independent?
2. What is JVM, JRE, and JDK?
3. What is the difference between JDK and JRE?
4. What is the main method in Java?
5. What is bytecode in Java?

## Next Steps
In the next module, we'll dive into Java Basics including variables, data types, and operators. Make sure you're comfortable with running Java programs before moving on!

🔜 [Next: Java Basics →](../2-java-basics/README.md)
