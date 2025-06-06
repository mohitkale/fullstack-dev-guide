# Calculator REST API with Spring Boot

This guide will walk you through creating a RESTful API for a calculator application using Spring Boot and Java. The API will support basic arithmetic operations: addition, subtraction, multiplication, division, and modulo.

## Table of Contents
1. [Prerequisites](#1-prerequisites)
2. [Project Setup](#2-project-setup)
3. [Project Structure](#3-project-structure)
4. [Implementing the API](#4-implementing-the-api)
   - [4.1 Create the Spring Boot Application](#41-create-the-spring-boot-application)
   - [4.2 Create the Calculator Service](#42-create-the-calculator-service)
   - [4.3 Create the REST Controller](#43-create-the-rest-controller)
   - [4.4 Add Exception Handling](#44-add-exception-handling)
5. [Testing the API](#5-testing-the-api)
6. [Running the Application](#6-running-the-application)
7. [API Documentation](#7-api-documentation)
8. [Next Steps](#8-next-steps)

## 1. Prerequisites

Before you begin, ensure you have the following installed:

- Java Development Kit (JDK) 17 or later
- Maven 3.6.3 or later
- Your favorite IDE (IntelliJ IDEA, Eclipse, VS Code, etc.)
- Postman or any API testing tool (for testing endpoints)

## 2. Project Setup

### 2.1 Generate a Spring Boot Project

You can generate a new Spring Boot project using Spring Initializr (https://start.spring.io/) with the following dependencies:

- Spring Web
- Spring Boot DevTools
- Lombok (for reducing boilerplate code)
- Spring Boot Actuator (for health checks)

Or use the following cURL command to generate the project:

```bash
curl https://start.spring.io/starter.zip -d type=maven-project \
  -d language=java \
  -d bootVersion=3.2.0 \
  -d groupId=com.example \
  -d artifactId=calculator-api \
  -d name=calculator-api \
  -d description=Calculator%20REST%20API \
  -d packageName=com.example.calculator \
  -d packaging=jar \
  -d javaVersion=17 \
  -d dependencies=web,devtools,lombok,actuator \
  -o calculator-api.zip
```

Extract the downloaded ZIP file and open it in your IDE.

## 3. Project Structure

Here's the project structure we'll be creating:

```
src/main/java/com/example/calculator/
├── CalculatorApplication.java         # Main application class
├── controller/
│   └── CalculatorController.java     # REST controller
├── dto/
│   ├── CalculatorRequest.java        # Request DTO
│   └── CalculatorResponse.java        # Response DTO
├── exception/
│   ├── GlobalExceptionHandler.java   # Global exception handler
│   └── InvalidOperationException.java # Custom exception
└── service/
    └── CalculatorService.java        # Business logic
```

## 4. Implementing the API

### 4.1 Create the Spring Boot Application

First, let's create the main application class:

```java
package com.example.calculator;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class CalculatorApplication {
    public static void main(String[] args) {
        SpringApplication.run(CalculatorApplication.class, args);
    }
}
```

### 4.2 Create the Calculator Service

Create the service layer that will contain the business logic for our calculator operations.

```java
package com.example.calculator.service;

import com.example.calculator.exception.InvalidOperationException;
import org.springframework.stereotype.Service;

@Service
public class CalculatorService {

    public double add(double a, double b) {
        return a + b;
    }

    public double subtract(double a, double b) {
        return a - b;
    }

    public double multiply(double a, double b) {
        return a * b;
    }

    public double divide(double a, double b) {
        if (b == 0) {
            throw new InvalidOperationException("Division by zero is not allowed");
        }
        return a / b;
    }

    public double modulo(double a, double b) {
        if (b == 0) {
            throw new InvalidOperationException("Modulo by zero is not allowed");
        }
        return a % b;
    }
}
```

### 4.3 Create the REST Controller

Now, let's create the REST controller that will expose our calculator endpoints.

```java
package com.example.calculator.controller;

import com.example.calculator.dto.CalculatorRequest;
import com.example.calculator.dto.CalculatorResponse;
import com.example.calculator.service.CalculatorService;
import jakarta.validation.Valid;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/calculate")
public class CalculatorController {

    private final CalculatorService calculatorService;

    @Autowired
    public CalculatorController(CalculatorService calculatorService) {
        this.calculatorService = calculatorService;
    }

    @GetMapping("/add")
    public ResponseEntity<CalculatorResponse> add(
            @RequestParam double a,
            @RequestParam double b) {
        double result = calculatorService.add(a, b);
        return ResponseEntity.ok(new CalculatorResponse(result));
    }

    @GetMapping("/subtract")
    public ResponseEntity<CalculatorResponse> subtract(
            @RequestParam double a,
            @RequestParam double b) {
        double result = calculatorService.subtract(a, b);
        return ResponseEntity.ok(new CalculatorResponse(result));
    }

    @GetMapping("/multiply")
    public ResponseEntity<CalculatorResponse> multiply(
            @RequestParam double a,
            @RequestParam double b) {
        double result = calculatorService.multiply(a, b);
        return ResponseEntity.ok(new CalculatorResponse(result));
    }

    @GetMapping("/divide")
    public ResponseEntity<CalculatorResponse> divide(
            @RequestParam double a,
            @RequestParam double b) {
        double result = calculatorService.divide(a, b);
        return ResponseEntity.ok(new CalculatorResponse(result));
    }

    @GetMapping("/modulo")
    public ResponseEntity<CalculatorResponse> modulo(
            @RequestParam double a,
            @RequestParam double b) {
        double result = calculatorService.modulo(a, b);
        return ResponseEntity.ok(new CalculatorResponse(result));
    }
}
```

### 4.4 Add Exception Handling

Let's create custom exceptions and a global exception handler to handle errors gracefully.

```java
// src/main/java/com/example/calculator/exception/InvalidOperationException.java
package com.example.calculator.exception;

public class InvalidOperationException extends RuntimeException {
    public InvalidOperationException(String message) {
        super(message);
    }
}
```

```java
// src/main/java/com/example/calculator/exception/GlobalExceptionHandler.java
package com.example.calculator.exception;

import com.example.calculator.dto.ErrorResponse;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.context.request.WebRequest;

import java.time.LocalDateTime;

@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(InvalidOperationException.class)
    public ResponseEntity<ErrorResponse> handleInvalidOperationException(
            InvalidOperationException ex, WebRequest request) {
        ErrorResponse errorResponse = new ErrorResponse(
                LocalDateTime.now(),
                HttpStatus.BAD_REQUEST.value(),
                "Bad Request",
                ex.getMessage(),
                request.getDescription(false)
        );
        return new ResponseEntity<>(errorResponse, HttpStatus.BAD_REQUEST);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGlobalException(
            Exception ex, WebRequest request) {
        ErrorResponse errorResponse = new ErrorResponse(
                LocalDateTime.now(),
                HttpStatus.INTERNAL_SERVER_ERROR.value(),
                "Internal Server Error",
                ex.getMessage(),
                request.getDescription(false)
        );
        return new ResponseEntity<>(errorResponse, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

### 4.5 Create DTOs (Data Transfer Objects)

Let's create DTOs for request/response handling.

```java
// src/main/java/com/example/calculator/dto/CalculatorRequest.java
package com.example.calculator.dto;

import jakarta.validation.constraints.NotNull;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class CalculatorRequest {
    @NotNull(message = "First number (a) is required")
    private Double a;
    
    @NotNull(message = "Second number (b) is required")
    private Double b;
}
```

```java
// src/main/java/com/example/calculator/dto/CalculatorResponse.java
package com.example.calculator.dto;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class CalculatorResponse {
    private double result;
}
```

```java
// src/main/java/com/example/calculator/dto/ErrorResponse.java
package com.example.calculator.dto;

import com.fasterxml.jackson.annotation.JsonFormat;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class ErrorResponse {
    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd hh:mm:ss")
    private LocalDateTime timestamp;
    private int status;
    private String error;
    private String message;
    private String path;
}
```

## 5. Configure Application Properties

Add the following configuration to `src/main/resources/application.properties`:

```properties
# Server Configuration
server.port=8080
server.servlet.context-path=/api

# Actuator Endpoints
management.endpoints.web.base-path=/actuator
management.endpoints.web.exposure.include=health,info,metrics
management.endpoint.health.show-details=always

# Logging
logging.level.root=INFO
logging.level.com.example.calculator=DEBUG
```

## 6. Testing the API

You can test the API using curl commands or Postman:

### Addition
```bash
curl "http://localhost:8080/api/calculate/add?a=10&b=5"
```

### Subtraction
```bash
curl "http://localhost:8080/api/calculate/subtract?a=10&b=5"
```

### Multiplication
```bash
curl "http://localhost:8080/api/calculate/multiply?a=10&b=5"
```

### Division
```bash
curl "http://localhost:8080/api/calculate/divide?a=10&b=5"
```

### Modulo
```bash
curl "http://localhost:8080/api/calculate/modulo?a=10&b=3"
```

### Error Case (Division by Zero)
```bash
curl -v "http://localhost:8080/api/calculate/divide?a=10&b=0"
```

## 7. Running the Application

### Using Maven

```bash
# Build the application
mvn clean package

# Run the application
java -jar target/calculator-api-0.0.1-SNAPSHOT.jar
```

### Using Spring Boot Maven Plugin

```bash
mvn spring-boot:run
```

## 8. API Documentation

The API documentation is available using SpringDoc OpenAPI. Add the following dependency to your `pom.xml`:

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.1.0</version>
</dependency>
```

After adding the dependency and restarting the application, you can access:

- Swagger UI: http://localhost:8080/api/swagger-ui.html
- OpenAPI JSON: http://localhost:8080/api/v3/api-docs

## 9. Next Steps

1. **Add Unit Tests**: Implement unit tests using JUnit and Mockito.
2. **Add Integration Tests**: Test the API endpoints using Spring's TestRestTemplate or WebTestClient.
3. **Add Logging**: Implement proper logging using SLF4J.
4. **Add Security**: Secure your API using Spring Security.
5. **Containerization**: Create a Dockerfile and docker-compose.yml for containerization.
6. **CI/CD**: Set up a CI/CD pipeline using GitHub Actions or Jenkins.
7. **Monitoring**: Add monitoring using Spring Boot Actuator and Prometheus.
8. **Documentation**: Enhance API documentation with more details and examples.

## Conclusion

You've successfully created a RESTful calculator API using Spring Boot. The API supports basic arithmetic operations and includes proper error handling and input validation. Feel free to extend it with more features as needed!
