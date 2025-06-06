# Design Patterns in Java

## Table of Contents
1. [Introduction to Design Patterns](#introduction-to-design-patterns)
2. [Creational Patterns](#creational-patterns)
   - [Singleton](#singleton)
   - [Factory Method](#factory-method)
   - [Abstract Factory](#abstract-factory)
   - [Builder](#builder)
   - [Prototype](#prototype)
3. [Structural Patterns](#structural-patterns)
   - [Adapter](#adapter)
   - [Decorator](#decorator)
   - [Facade](#facade)
   - [Proxy](#proxy)
4. [Behavioral Patterns](#behavioral-patterns)
   - [Observer](#observer)
   - [Strategy](#strategy)
   - [Command](#command)
   - [Template Method](#template-method)
5. [Practice Exercises](#practice-exercises-)
6. [Common Interview Questions](#common-interview-questions)

## Introduction to Design Patterns

Design patterns are typical solutions to common problems in software design. They are like pre-made blueprints that you can customize to solve a recurring design problem in your code.

### Why Use Design Patterns?
- **Proven Solutions**: Tested and proven development paradigms
- **Code Reusability**: Reuse solutions to similar problems
- **Maintainability**: Makes code more understandable and maintainable
- **Communication**: Common vocabulary for developers

### Categories of Design Patterns
1. **Creational Patterns**: Deal with object creation mechanisms
2. **Structural Patterns**: Deal with object composition and structure
3. **Behavioral Patterns**: Deal with object interaction and responsibility

## Creational Patterns

### Singleton
Ensures a class has only one instance and provides a global point of access to it.

#### Implementation (Thread-Safe with Double-Checked Locking)
```java
public class DatabaseConnection {
    // Private static volatile instance
    private static volatile DatabaseConnection instance;
    
    // Private constructor to prevent instantiation
    private DatabaseConnection() {
        // Initialize database connection
    }
    
    // Public method to get the singleton instance
    public static DatabaseConnection getInstance() {
        if (instance == null) {  // First check (no locking)
            synchronized (DatabaseConnection.class) {
                if (instance == null) {  // Second check (with locking)
                    instance = new DatabaseConnection();
                }
            }
        }
        return instance;
    }
    
    // Other database methods
    public void connect() {
        System.out.println("Connected to database");
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        DatabaseConnection db1 = DatabaseConnection.getInstance();
        DatabaseConnection db2 = DatabaseConnection.getInstance();
        
        System.out.println(db1 == db2);  // true (same instance)
        db1.connect();
    }
}
```

### Factory Method
Defines an interface for creating an object, but lets subclasses decide which class to instantiate.

#### Implementation
```java
// Product interface
interface PaymentMethod {
    void processPayment(double amount);
}

// Concrete Products
class CreditCardPayment implements PaymentMethod {
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing credit card payment of $" + amount);
    }
}

class UpiPayment implements PaymentMethod {
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing UPI payment of ₹" + amount);
    }
}

// Creator (Factory)
abstract class PaymentProcessor {
    // Factory method
    public abstract PaymentMethod createPaymentMethod();
    
    public void process(double amount) {
        PaymentMethod paymentMethod = createPaymentMethod();
        paymentMethod.processPayment(amount);
    }
}

// Concrete Creators
class CreditCardProcessor extends PaymentProcessor {
    @Override
    public PaymentMethod createPaymentMethod() {
        return new CreditCardPayment();
    }
}

class UpiProcessor extends PaymentProcessor {
    @Override
    public PaymentMethod createPaymentMethod() {
        return new UpiPayment();
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        PaymentProcessor processor = new UpiProcessor();
        processor.process(1000.0);
    }
}
```

### Abstract Factory
Provides an interface for creating families of related or dependent objects without specifying their concrete classes.

#### Implementation
```java
// Abstract Products
interface Button {
    void render();
}

interface Checkbox {
    void render();
}

// Concrete Products for Windows
class WindowsButton implements Button {
    @Override
    public void render() {
        System.out.println("Rendering Windows button");
    }
}

class WindowsCheckbox implements Checkbox {
    @Override
    public void render() {
        System.out.println("Rendering Windows checkbox");
    }
}

// Concrete Products for MacOS
class MacOSButton implements Button {
    @Override
    public void render() {
        System.out.println("Rendering MacOS button");
    }
}

class MacOSCheckbox implements Checkbox {
    @Override
    public void render() {
        System.out.println("Rendering MacOS checkbox");
    }
}

// Abstract Factory
interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

// Concrete Factories
class WindowsFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new WindowsButton();
    }
    
    @Override
    public Checkbox createCheckbox() {
        return new WindowsCheckbox();
    }
}

class MacOSFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new MacOSButton();
    }
    
    @Override
    public Checkbox createCheckbox() {
        return new MacOSCheckbox();
    }
}

// Client code
class Application {
    private Button button;
    private Checkbox checkbox;
    
    public Application(GUIFactory factory) {
        button = factory.createButton();
        checkbox = factory.createCheckbox();
    }
    
    public void render() {
        button.render();
        checkbox.render();
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        // Create a Windows application
        GUIFactory windowsFactory = new WindowsFactory();
        Application windowsApp = new Application(windowsFactory);
        windowsApp.render();
        
        // Create a MacOS application
        GUIFactory macFactory = new MacOSFactory();
        Application macApp = new Application(macFactory);
        macApp.render();
    }
}
```

## Structural Patterns

### Adapter
Allows objects with incompatible interfaces to collaborate.

#### Implementation
```java
// Target interface expected by the client
interface MediaPlayer {
    void play(String audioType, String fileName);
}

// Adaptee (incompatible interface)
class AdvancedMediaPlayer {
    public void playVlc(String fileName) {
        System.out.println("Playing vlc file: " + fileName);
    }
    
    public void playMp4(String fileName) {
        System.out.println("Playing mp4 file: " + fileName);
    }
}

// Adapter
class MediaAdapter implements MediaPlayer {
    private AdvancedMediaPlayer advancedMediaPlayer;
    
    public MediaAdapter(String audioType) {
        if (audioType.equalsIgnoreCase("vlc")) {
            advancedMediaPlayer = new AdvancedMediaPlayer();
        } else if (audioType.equalsIgnoreCase("mp4")) {
            advancedMediaPlayer = new AdvancedMediaPlayer();
        }
    }
    
    @Override
    public void play(String audioType, String fileName) {
        if (audioType.equalsIgnoreCase("vlc")) {
            advancedMediaPlayer.playVlc(fileName);
        } else if (audioType.equalsIgnoreCase("mp4")) {
            advancedMediaPlayer.playMp4(fileName);
        }
    }
}

// Client
class AudioPlayer implements MediaPlayer {
    private MediaAdapter mediaAdapter;
    
    @Override
    public void play(String audioType, String fileName) {
        if (audioType.equalsIgnoreCase("mp3")) {
            System.out.println("Playing mp3 file: " + fileName);
        } else if (audioType.equalsIgnoreCase("vlc") || audioType.equalsIgnoreCase("mp4")) {
            mediaAdapter = new MediaAdapter(audioType);
            mediaAdapter.play(audioType, fileName);
        } else {
            System.out.println("Invalid media type: " + audioType);
        }
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        AudioPlayer audioPlayer = new AudioPlayer();
        
        audioPlayer.play("mp3", "song.mp3");
        audioPlayer.play("mp4", "movie.mp4");
        audioPlayer.play("vlc", "video.vlc");
        audioPlayer.play("avi", "movie.avi");
    }
}
```

## Behavioral Patterns

### Observer
Defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.

#### Implementation
```java
import java.util.ArrayList;
import java.util.List;

// Subject (Observable)
class WeatherStation {
    private int temperature;
    private List<Observer> observers = new ArrayList<>();
    
    public void addObserver(Observer observer) {
        observers.add(observer);
    }
    
    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }
    
    public void setTemperature(int temperature) {
        this.temperature = temperature;
        notifyObservers();
    }
    
    private void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(temperature);
        }
    }
}

// Observer
interface Observer {
    void update(int temperature);
}

// Concrete Observers
class PhoneDisplay implements Observer {
    @Override
    public void update(int temperature) {
        System.out.println("Phone Display: Temperature is " + temperature + "°C");
    }
}

class TVDisplay implements Observer {
    @Override
    public void update(int temperature) {
        System.out.println("TV Display: Current temperature is " + temperature + "°C");
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        WeatherStation weatherStation = new WeatherStation();
        
        Observer phoneDisplay = new PhoneDisplay();
        Observer tvDisplay = new TVDisplay();
        
        weatherStation.addObserver(phoneDisplay);
        weatherStation.addObserver(tvDisplay);
        
        // Change temperature (observers will be notified)
        weatherStation.setTemperature(25);
        weatherStation.setTemperature(27);
        
        // Remove an observer
        weatherStation.removeObserver(tvDisplay);
        weatherStation.setTemperature(30);
    }
}
```

## Practice Exercises 📝

1. **E-commerce Order System**
   - Implement the Command pattern to handle different order operations (place, cancel, return)
   - Create commands for each operation and use them to decouple sender and receiver

2. **Document Editor**
   - Implement the Memento pattern to add undo/redo functionality
   - Allow saving and restoring document states

3. **Social Media Notifications**
   - Implement the Observer pattern for a social media platform
   - Notify users when there are new posts, likes, or comments

## Common Interview Questions

1. What is the difference between Factory Method and Abstract Factory patterns?
2. When would you use the Singleton pattern? What are its drawbacks?
3. Explain the difference between the Adapter and Facade patterns.
4. What is the difference between the Observer and Publish-Subscribe patterns?
5. How would you implement a thread-safe Singleton in Java?
6. What is the difference between the Strategy and State patterns?
7. When would you use the Builder pattern?
8. Explain the difference between the Proxy and Decorator patterns.
9. What is the Template Method pattern? Give an example.
10. How does the Chain of Responsibility pattern work?

🔙 [Previous: Generics](../6-generics/README.md) | 
🔜 [Next: Java 8+ Features →](../8-java8-features/README.md)
