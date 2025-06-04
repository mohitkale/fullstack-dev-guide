# Calculator Backend with Express.js

This guide will walk you through creating a simple REST API for a calculator using Node.js and Express. By the end of this tutorial, you'll have a backend service that can perform basic arithmetic operations.

## Table of Contents
1. [Project Setup](#1-project-setup)
2. [Creating the Express Server](#2-creating-the-express-server)
3. [Implementing Calculator Endpoints](#3-implementing-calculator-endpoints)
4. [Adding Request Validation](#4-adding-request-validation)
5. [Testing with cURL and Postman](#5-testing-with-curl-and-postman)
6. [Error Handling](#6-error-handling)
7. [Running the Server](#7-running-the-server)

## 1. Project Setup

First, let's create a new Node.js project and install the required dependencies:

```bash
# Create a new directory for the project
mkdir calculator-backend
cd calculator-backend

# Initialize a new Node.js project
npm init -y

# Install required dependencies
npm install express cors

# Install development dependencies
npm install --save-dev typescript ts-node @types/node @types/express @types/cors nodemon

# Initialize TypeScript configuration
npx tsc --init
```

## 2. Setting Up the Project Structure

Create the following directory structure:
```
calculator-backend/
├── src/
│   ├── controllers/
│   │   └── calculator.controller.ts
│   ├── middlewares/
│   │   └── error.middleware.ts
│   ├── routes/
│   │   └── calculator.routes.ts
│   ├── types/
│   │   └── calculator.types.ts
│   ├── utils/
│   │   └── calculator.ts
│   ├── app.ts
│   └── server.ts
├── .gitignore
├── package.json
├── tsconfig.json
└── README.md
```

## 3. Creating the Express Server

Let's create the main application file:

```typescript
// src/app.ts
import express, { Application, Request, Response, NextFunction } from 'express';
import cors from 'cors';
import calculatorRoutes from './routes/calculator.routes';
import { errorHandler } from './middlewares/error.middleware';

class App {
  public app: Application;

  constructor() {
    this.app = express();
    this.initializeMiddlewares();
    this.initializeRoutes();
    this.initializeErrorHandling();
  }

  private initializeMiddlewares() {
    // Enable CORS
    this.app.use(cors());
    
    // Parse JSON request bodies
    this.app.use(express.json());
    
    // Parse URL-encoded request bodies
    this.app.use(express.urlencoded({ extended: true }));
  }

  private initializeRoutes() {
    // Health check endpoint
    this.app.get('/health', (req: Request, res: Response) => {
      res.status(200).json({ status: 'ok', message: 'Calculator API is running' });
    });

    // Calculator routes
    this.app.use('/api/calculate', calculatorRoutes);

    // 404 handler
    this.app.use((req: Request, res: Response) => {
      res.status(404).json({ error: 'Not Found' });
    });
  }

  private initializeErrorHandling() {
    // Error handling middleware
    this.app.use(errorHandler);
  }
}

export default new App().app;
```

Create the server file:

```typescript
// src/server.ts
import app from './app';

const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`);
});
```

## 4. Implementing Calculator Logic

Let's create the calculator utility functions:

```typescript
// src/utils/calculator.ts
type Operation = 'add' | 'subtract' | 'multiply' | 'divide' | 'modulus';

export const calculate = (a: number, b: number, operation: Operation): number => {
  switch (operation) {
    case 'add':
      return a + b;
    case 'subtract':
      return a - b;
    case 'multiply':
      return a * b;
    case 'divide':
      if (b === 0) {
        throw new Error('Division by zero is not allowed');
      }
      return a / b;
    case 'modulus':
      return a % b;
    default:
      throw new Error('Invalid operation');
  }
};

export const validateNumbers = (a: unknown, b: unknown): { a: number; b: number } => {
  if (a === undefined || b === undefined) {
    throw new Error('Both numbers are required');
  }

  const numA = Number(a);
  const numB = Number(b);

  if (isNaN(numA) || isNaN(numB)) {
    throw new Error('Both parameters must be valid numbers');
  }

  return { a: numA, b: numB };
};
```

## 5. Creating the Calculator Controller

```typescript
// src/controllers/calculator.controller.ts
import { Request, Response, NextFunction } from 'express';
import { calculate, validateNumbers } from '../utils/calculator';

export const add = (req: Request, res: Response, next: NextFunction) => {
  try {
    const { a, b } = validateNumbers(req.query.a, req.query.b);
    const result = calculate(a, b, 'add');
    res.json({ result });
  } catch (error) {
    next(error);
  }
};

export const subtract = (req: Request, res: Response, next: NextFunction) => {
  try {
    const { a, b } = validateNumbers(req.query.a, req.query.b);
    const result = calculate(a, b, 'subtract');
    res.json({ result });
  } catch (error) {
    next(error);
  }
};

export const multiply = (req: Request, res: Response, next: NextFunction) => {
  try {
    const { a, b } = validateNumbers(req.query.a, req.query.b);
    const result = calculate(a, b, 'multiply');
    res.json({ result });
  } catch (error) {
    next(error);
  }
};

export const divide = (req: Request, res: Response, next: NextFunction) => {
  try {
    const { a, b } = validateNumbers(req.query.a, req.query.b);
    const result = calculate(a, b, 'divide');
    res.json({ result });
  } catch (error) {
    next(error);
  }
};

export const modulus = (req: Request, res: Response, next: NextFunction) => {
  try {
    const { a, b } = validateNumbers(req.query.a, req.query.b);
    const result = calculate(a, b, 'modulus');
    res.json({ result });
  } catch (error) {
    next(error);
  }
};
```

## 6. Setting Up Routes

```typescript
// src/routes/calculator.routes.ts
import { Router } from 'express';
import { 
  add, 
  subtract, 
  multiply, 
  divide, 
  modulus 
} from '../controllers/calculator.controller';

const router = Router();

// GET /api/calculate/add?a=5&b=3
router.get('/add', add);

// GET /api/calculate/subtract?a=5&b=3
router.get('/subtract', subtract);

// GET /api/calculate/multiply?a=5&b=3
router.get('/multiply', multiply);

// GET /api/calculate/divide?a=10&b=2
router.get('/divide', divide);

// GET /api/calculate/modulus?a=10&b=3
router.get('/modulus', modulus);

export default router;
```

## 7. Adding Error Handling Middleware

```typescript
// src/middlewares/error.middleware.ts
import { Request, Response, NextFunction } from 'express';

export const errorHandler = (
  err: Error,
  req: Request,
  res: Response,
  next: NextFunction
) => {
  console.error(err.stack);
  
  if (err.message === 'Division by zero is not allowed') {
    return res.status(400).json({
      error: 'Bad Request',
      message: err.message,
    });
  }

  if (err.message === 'Both numbers are required' || 
      err.message === 'Both parameters must be valid numbers') {
    return res.status(400).json({
      error: 'Bad Request',
      message: err.message,
    });
  }

  res.status(500).json({
    error: 'Internal Server Error',
    message: 'Something went wrong',
  });
};
```

## 8. Updating package.json Scripts

Add these scripts to your `package.json`:

```json
{
  "scripts": {
    "start": "node dist/server.js",
    "dev": "nodemon --exec ts-node src/server.ts",
    "build": "tsc",
    "test": "echo \"Error: no test specified\" && exit 1"
  }
}
```

## 9. Testing the API

### Using cURL

1. **Add two numbers**:
   ```bash
   curl "http://localhost:3000/api/calculate/add?a=5&b=3"
   # Response: {"result":8}
   ```

2. **Subtract two numbers**:
   ```bash
   curl "http://localhost:3000/api/calculate/subtract?a=10&b=4"
   # Response: {"result":6}
   ```

3. **Multiply two numbers**:
   ```bash
   curl "http://localhost:3000/api/calculate/multiply?a=6&b=7"
   # Response: {"result":42}
   ```

4. **Divide two numbers**:
   ```bash
   curl "http://localhost:3000/api/calculate/divide?a=15&b=3"
   # Response: {"result":5}
   ```

5. **Modulus operation**:
   ```bash
   curl "http://localhost:3000/api/calculate/modulus?a=10&b=3"
   # Response: {"result":1}
   ```

### Using Postman

1. Open Postman
2. Set the request method to `GET`
3. Enter the URL: `http://localhost:3000/api/calculate/add?a=5&b=3`
4. Click "Send"
5. You should see the response: `{"result":8}`

## 10. Error Handling Examples

1. **Division by zero**:
   ```bash
   curl "http://localhost:3000/api/calculate/divide?a=10&b=0"
   # Response: {"error":"Bad Request","message":"Division by zero is not allowed"}
   ```

2. **Missing parameters**:
   ```bash
   curl "http://localhost:3000/api/calculate/add?a=5"
   # Response: {"error":"Bad Request","message":"Both numbers are required"}
   ```

3. **Invalid numbers**:
   ```bash
   curl "http://localhost:3000/api/calculate/add?a=five&b=three"
   # Response: {"error":"Bad Request","message":"Both parameters must be valid numbers"}
   ```

## 11. Running the Server

To start the development server:

```bash
# Start the development server with hot-reload
npm run dev

# In production
npm run build
npm start
```

## 12. Project Structure Overview

- `src/app.ts`: Main Express application setup
- `src/server.ts`: Server entry point
- `src/controllers/`: Request handlers
- `src/routes/`: API route definitions
- `src/middlewares/`: Custom middleware functions
- `src/utils/`: Utility functions

## 13. Next Steps

1. Add input validation using a library like Joi or class-validator
2. Add request rate limiting
3. Add API documentation with Swagger/OpenAPI
4. Add logging with Winston or Morgan
5. Add unit and integration tests with Jest
6. Add Docker support
7. Set up CI/CD pipeline

## 14. Conclusion

You've successfully created a RESTful API for a calculator using Express.js and TypeScript. The API supports basic arithmetic operations and includes proper error handling. You can now use this API as a backend for your calculator application or extend it with more features.
