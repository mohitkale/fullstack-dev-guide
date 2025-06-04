# Fullstack Calculator with React and Express

This guide will walk you through building a complete fullstack calculator application with a React frontend and Express backend. The application demonstrates how to connect a modern frontend with a RESTful API backend.

## Table of Contents
1. [Project Overview](#1-project-overview)
2. [Prerequisites](#2-prerequisites)
3. [Project Structure](#3-project-structure)
4. [Backend Setup](#4-backend-setup)
5. [Frontend Setup](#5-frontend-setup)
6. [Connecting Frontend to Backend](#6-connecting-frontend-to-backend)
7. [Running the Application](#7-running-the-application)
8. [Testing the Application](#8-testing-the-application)
9. [Deployment](#9-deployment)
10. [Troubleshooting](#10-troubleshooting)

## 1. Project Overview

This project is a fullstack calculator application that includes:

- **Frontend**: React with TypeScript and Vite
- **Backend**: Node.js with Express and TypeScript
- **Features**:
  - Basic arithmetic operations (+, -, *, /, %)
  - Responsive design
  - Error handling
  - Loading states
  - API health monitoring

## 2. Prerequisites

Before you begin, ensure you have the following installed:

- Node.js (v16 or later)
- npm (v8 or later) or yarn
- Git (optional but recommended)

## 3. Project Structure

```
calculator-fullstack/
├── backend/               # Express API server
│   ├── src/
│   │   ├── app.ts        # Express app configuration
│   │   └── server.ts      # Server entry point
│   ├── package.json
│   └── tsconfig.json
├── frontend/              # React frontend
│   ├── src/
│   │   ├── components/   # React components
│   │   ├── services/     # API service
│   │   ├── App.tsx
│   │   └── main.tsx
│   ├── public/
│   ├── package.json
│   └── vite.config.ts
└── README.md
```

## 4. Backend Setup

### 4.1 Initialize the Backend

1. Create and navigate to the backend directory:
   ```bash
   mkdir -p calculator-fullstack/backend
   cd calculator-fullstack/backend
   ```

2. Initialize a new Node.js project:
   ```bash
   npm init -y
   ```

3. Install dependencies:
   ```bash
   npm install express cors
   npm install --save-dev typescript ts-node @types/node @types/express @types/cors nodemon
   ```

4. Initialize TypeScript:
   ```bash
   npx tsc --init
   ```

### 4.2 Backend Code

1.  **`backend/package.json`**:

    Create or update `calculator-fullstack/backend/package.json` with the following content:

    ```json
    {
      "name": "calculator-backend",
      "version": "1.0.0",
      "description": "Calculator API with Express and TypeScript",
      "main": "dist/server.js",
      "scripts": {
        "start": "node dist/server.js",
        "build": "tsc",
        "dev": "nodemon --exec ts-node src/server.ts",
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "dependencies": {
        "cors": "^2.8.5",
        "express": "^4.18.2"
      },
      "devDependencies": {
        "@types/cors": "^2.8.17",
        "@types/express": "^4.17.21",
        "@types/node": "^20.11.19",
        "nodemon": "^3.0.2",
        "ts-node": "^10.9.2",
        "typescript": "^5.3.3"
      }
    }
    ```

2.  **`backend/tsconfig.json`**:

    Create or update `calculator-fullstack/backend/tsconfig.json`:

    ```json
    {
      "compilerOptions": {
        "target": "es2018",
        "module": "commonjs",
        "outDir": "./dist",
        "rootDir": "./src",
        "strict": true,
        "esModuleInterop": true,
        "skipLibCheck": true,
        "forceConsistentCasingInFileNames": true,
        "moduleResolution": "node"
      },
      "include": ["src/**/*"],
      "exclude": ["node_modules"]
    }
    ```

    3.  **`backend/src/app.ts`**:

    Create `calculator-fullstack/backend/src/app.ts`:

    ```typescript
    import express, { Application, Request, Response, NextFunction } from 'express';
    import cors from 'cors';

    const app: Application = express();

    // Middleware
    app.use(cors()); // Enable CORS for all routes
    app.use(express.json()); // Parse JSON bodies
    app.use(express.urlencoded({ extended: true })); // Parse URL-encoded bodies

    // Health check endpoint
    app.get('/api/health', (req: Request, res: Response) => {
      res.json({ status: 'ok', message: 'Calculator API is running' });
    });

    // Helper function to validate numbers
    function validateNumbers(a: any, b: any): { a: number; b: number } {
      const numA = Number(a);
      const numB = Number(b);

      if (a === undefined || b === undefined) {
        throw { statusCode: 400, message: 'Both numbers (a and b) are required as query parameters.' };
      }

      if (isNaN(numA) || isNaN(numB)) {
        throw { statusCode: 400, message: 'Both parameters (a and b) must be valid numbers.' };
      }
      return { a: numA, b: numB };
    }

    // Calculator endpoints
    app.get('/api/calculate/add', (req: Request, res: Response, next: NextFunction) => {
      try {
        const { a, b } = validateNumbers(req.query.a, req.query.b);
        res.json({ result: a + b });
      } catch (error) {
        next(error);
      }
    });

    app.get('/api/calculate/subtract', (req: Request, res: Response, next: NextFunction) => {
      try {
        const { a, b } = validateNumbers(req.query.a, req.query.b);
        res.json({ result: a - b });
      } catch (error) {
        next(error);
      }
    });

    app.get('/api/calculate/multiply', (req: Request, res: Response, next: NextFunction) => {
      try {
        const { a, b } = validateNumbers(req.query.a, req.query.b);
        res.json({ result: a * b });
      } catch (error) {
        next(error);
      }
    });

    app.get('/api/calculate/divide', (req: Request, res: Response, next: NextFunction) => {
      try {
        const { a, b } = validateNumbers(req.query.a, req.query.b);
        if (b === 0) {
          throw { statusCode: 400, message: 'Division by zero is not allowed.' };
        }
        res.json({ result: a / b });
      } catch (error) {
        next(error);
      }
    });

    app.get('/api/calculate/modulo', (req: Request, res: Response, next: NextFunction) => {
      try {
        const { a, b } = validateNumbers(req.query.a, req.query.b);
        if (b === 0) {
          throw { statusCode: 400, message: 'Modulo by zero is not allowed.' };
        }
        res.json({ result: a % b });
      } catch (error) {
        next(error);
      }
    });

    // Error handling middleware (should be the last piece of middleware)
    app.use((err: any, req: Request, res: Response, next: NextFunction) => {
      console.error(err.stack);
      const statusCode = err.statusCode || 500;
      res.status(statusCode).json({
        error: {
          message: err.message || 'Internal Server Error',
          status: statusCode
        }
      });
    });

    // 404 Handler for undefined routes
    app.use((req: Request, res: Response) => {
        res.status(404).json({ 
            error: {
                message: 'Not Found - The requested resource does not exist.',
                status: 404
            }
        });
    });

    export { app };
    ```

4.  **`backend/src/server.ts`**:

    Create `calculator-fullstack/backend/src/server.ts`:

    ```typescript
    import { app } from './app';

    const PORT = process.env.PORT || 3001;

    app.listen(PORT, () => {
      console.log(`Server is running on http://localhost:${PORT}`);
      console.log('Press CTRL-C to stop\n');
    });
    ```

## 5. Frontend Setup

### 5.1 Initialize the Frontend

1. Navigate to the project root and create a new Vite project:
   ```bash
   cd calculator-fullstack
   npm create vite@latest frontend -- --template react-ts
   cd frontend
   ```

2. Install additional dependencies:
   ```bash
   npm install axios
   ```

### 5.2 Frontend Code

1.  **`frontend/vite.config.ts`**:

    Update `calculator-fullstack/frontend/vite.config.ts` to include the proxy configuration:

    ```typescript
    import { defineConfig } from 'vite';
    import react from '@vitejs/plugin-react';

    // https://vitejs.dev/config/
    export default defineConfig({
      plugins: [react()],
      server: {
        proxy: {
          // Proxy /api requests to the backend server
          '/api': {
            target: 'http://localhost:3001', // Your backend server URL
            changeOrigin: true, // Recommended for virtual hosted sites
            secure: false, // Set to true if your backend is HTTPS
            // ws: true, // If you're using WebSockets
          },
        },
      },
    });
    ```

2.  **`frontend/src/services/calculatorApi.ts`**:

    Create `calculator-fullstack/frontend/src/services/calculatorApi.ts`:

    ```typescript
    import axios from 'axios';

    // The base URL will be proxied by Vite during development
    // In production, this should be the actual URL of your deployed backend API
    const API_BASE_URL = '/api'; 

    export type Operation = 'add' | 'subtract' | 'multiply' | 'divide' | 'modulo';

    export interface CalculationResult {
      result: number;
    }

    export interface ApiError {
      error: {
        message: string;
        status: number;
      };
    }

    export const calculate = async (
      a: number,
      b: number,
      operation: Operation
    ): Promise<number> => {
      try {
        const response = await axios.get<CalculationResult>(
          `${API_BASE_URL}/calculate/${operation}`,
          {
            params: { a, b },
          }
        );
        return response.data.result;
      } catch (error) {
        if (axios.isAxiosError(error) && error.response) {
          // The request was made and the server responded with a status code
          // that falls out of the range of 2xx
          const apiError = error.response.data as ApiError;
          throw new Error(apiError.error?.message || 'Calculation failed on the server.');
        } else if (axios.isAxiosError(error) && error.request) {
          // The request was made but no response was received
          throw new Error('No response from server. Please check your network connection or if the server is running.');
        } else {
          // Something happened in setting up the request that triggered an Error
          throw new Error('An unexpected error occurred while making the request.');
        }
      }
    };

    export const checkApiHealth = async (): Promise<boolean> => {
      try {
        const response = await axios.get(`${API_BASE_URL}/health`);
        return response.status === 200 && response.data.status === 'ok';
      } catch (error) {
        console.error('API health check failed:', error);
        return false;
      }
    };
    ```

3.  **`frontend/src/components/Calculator.css`**:

    Create `calculator-fullstack/frontend/src/components/Calculator.css`:

    ```css
    .calculator {
      width: 300px;
      margin: 50px auto;
      padding: 20px;
      border: 1px solid #ccc;
      border-radius: 8px;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
      background-color: #f9f9f9;
    }

    .display {
      background-color: #222;
      color: white;
      font-size: 2em;
      padding: 10px;
      text-align: right;
      border-radius: 4px;
      margin-bottom: 15px;
      min-height: 1.5em; /* Ensure display doesn't collapse */
      overflow-wrap: break-word;
    }

    .display .previous-operand {
      color: #aaa;
      font-size: 0.6em;
      min-height: 1em;
    }
    
    .display .current-operand {
      font-size: 1em;
      min-height: 1.2em;
    }

    .display .error {
      color: #ff6666;
      font-size: 0.8em;
    }

    .buttons {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 10px;
    }

    .buttons button {
      padding: 15px;
      font-size: 1.2em;
      border: 1px solid #ddd;
      border-radius: 4px;
      background-color: #fff;
      cursor: pointer;
      transition: background-color 0.2s;
    }

    .buttons button:hover:not(:disabled) {
      background-color: #f0f0f0;
    }

    .buttons button:active:not(:disabled) {
      background-color: #e0e0e0;
    }
    
    .buttons button.span-two {
      grid-column: span 2;
    }

    .buttons button.operator {
      background-color: #ff9500;
      color: white;
    }

    .buttons button.operator:hover:not(:disabled) {
      background-color: #e08500;
    }

    .buttons button.equals {
      background-color: #4caf50;
      color: white;
      grid-column: span 2; /* Make equals button wider */
    }

    .buttons button.equals:hover:not(:disabled) {
      background-color: #45a049;
    }

    .buttons button:disabled {
      opacity: 0.7;
      cursor: not-allowed;
    }

    .api-status {
      text-align: center;
      margin-bottom: 10px;
      padding: 8px;
      border-radius: 4px;
    }

    .api-status.online {
      background-color: #d4edda; /* Light green */
      color: #155724; /* Dark green */
      border: 1px solid #c3e6cb;
    }

    .api-status.offline {
      background-color: #f8d7da; /* Light red */
      color: #721c24; /* Dark red */
      border: 1px solid #f5c6cb;
    }
    ```

4.  **`frontend/src/components/Calculator.tsx`**:

    Create `calculator-fullstack/frontend/src/components/Calculator.tsx`:

    ```tsx
    import { useState, useEffect, useCallback } from 'react';
    import { calculate, Operation, checkApiHealth } from '../services/calculatorApi';
    import './Calculator.css';

    const Calculator = () => {
      const [currentInput, setCurrentInput] = useState('0');
      const [previousInput, setPreviousInput] = useState('');
      const [operation, setOperation] = useState<Operation | null>(null);
      const [shouldResetInput, setShouldResetInput] = useState(true);
      const [error, setError] = useState<string | null>(null);
      const [isLoading, setIsLoading] = useState(false);
      const [isApiOnline, setIsApiOnline] = useState<boolean | null>(null); // null for initial checking state

      const updateApiStatus = useCallback(async () => {
        const healthy = await checkApiHealth();
        setIsApiOnline(healthy);
      }, []);

      useEffect(() => {
        updateApiStatus();
        // Optionally, set an interval to periodically check API health
        // const intervalId = setInterval(updateApiStatus, 30000); // Check every 30 seconds
        // return () => clearInterval(intervalId);
      }, [updateApiStatus]);

      const handleNumberClick = (number: string) => {
        setError(null);
        if (shouldResetInput || currentInput === '0') {
          setCurrentInput(number);
          setShouldResetInput(false);
        } else {
          setCurrentInput(prev => prev + number);
        }
      };

      const handleDecimalClick = () => {
        setError(null);
        if (shouldResetInput) {
          setCurrentInput('0.');
          setShouldResetInput(false);
          return;
        }
        if (!currentInput.includes('.')) {
          setCurrentInput(prev => prev + '.');
        }
      };

      const handleOperationClick = (op: Operation) => {
        setError(null);
        if (currentInput === '' && previousInput === '') return;

        if (previousInput && operation && !shouldResetInput) {
          // If there's a pending operation, calculate it first
          handleEqualsClick(op); // Pass the new operation to chain it
          return;
        }
        
        setPreviousInput(currentInput);
        setOperation(op);
        setShouldResetInput(true);
      };

      const handleEqualsClick = async (chainedOperation: Operation | null = null) => {
        if (!operation || previousInput === '') {
          if (chainedOperation) { // If equals was called as part of a new operation
            setOperation(chainedOperation);
            setPreviousInput(currentInput);
            setShouldResetInput(true);
          }
          return;
        }
        
        setIsLoading(true);
        setError(null);
        try {
          const prev = parseFloat(previousInput);
          const curr = parseFloat(currentInput);
          const result = await calculate(prev, curr, operation);
          setCurrentInput(String(result));
          setPreviousInput('');
          if (chainedOperation) {
            setOperation(chainedOperation);
            setPreviousInput(String(result));
          } else {
            setOperation(null);
          }
          setShouldResetInput(true);
        } catch (e) {
          const errorMessage = e instanceof Error ? e.message : 'An unknown error occurred.';
          setError(errorMessage);
          // Keep previousInput and operation for potential retry or correction
          // setPreviousInput(''); 
          // setOperation(null);
        } finally {
          setIsLoading(false);
        }
      };

      const handleClear = () => {
        setCurrentInput('0');
        setPreviousInput('');
        setOperation(null);
        setShouldResetInput(true);
        setError(null);
        setIsLoading(false);
      };

      const displayPrevious = `${previousInput} ${operation ? {add:'+', subtract:'-', multiply:'×', divide:'÷', modulo:'%'}[operation] : ''}`;
      const displayCurrent = error ? 'Error' : (isLoading ? '...' : currentInput);

      return (
        <div className="calculator">
          {isApiOnline === null && <div className="api-status">Checking API status...</div>}
          {isApiOnline === true && <div className="api-status online">API Online</div>}
          {isApiOnline === false && <div className="api-status offline">API Offline - Calculations may fail</div>}
          <div className="display">
            <div className="previous-operand">{error ? error : displayPrevious}</div>
            <div className="current-operand">{displayCurrent}</div>
          </div>
          <div className="buttons">
            <button onClick={handleClear} className="span-two">AC</button>
            <button onClick={() => handleOperationClick('modulo')} className="operator" disabled={isLoading}>%</button>
            <button onClick={() => handleOperationClick('divide')} className="operator" disabled={isLoading}>÷</button>

            <button onClick={() => handleNumberClick('7')} disabled={isLoading}>7</button>
            <button onClick={() => handleNumberClick('8')} disabled={isLoading}>8</button>
            <button onClick={() => handleNumberClick('9')} disabled={isLoading}>9</button>
            <button onClick={() => handleOperationClick('multiply')} className="operator" disabled={isLoading}>×</button>

            <button onClick={() => handleNumberClick('4')} disabled={isLoading}>4</button>
            <button onClick={() => handleNumberClick('5')} disabled={isLoading}>5</button>
            <button onClick={() => handleNumberClick('6')} disabled={isLoading}>6</button>
            <button onClick={() => handleOperationClick('subtract')} className="operator" disabled={isLoading}>-</button>

            <button onClick={() => handleNumberClick('1')} disabled={isLoading}>1</button>
            <button onClick={() => handleNumberClick('2')} disabled={isLoading}>2</button>
            <button onClick={() => handleNumberClick('3')} disabled={isLoading}>3</button>
            <button onClick={() => handleOperationClick('add')} className="operator" disabled={isLoading}>+</button>

            <button onClick={() => handleNumberClick('0')} className="span-two" disabled={isLoading}>0</button>
            <button onClick={handleDecimalClick} disabled={isLoading}>.</button>
            <button onClick={() => handleEqualsClick()} className="equals" disabled={isLoading}>=</button>
          </div>
        </div>
      );
    };

    export default Calculator;
    ```

5.  **`frontend/src/App.css`**:

    Create `calculator-fullstack/frontend/src/App.css` (can be minimal or include global styles):

    ```css
    #root {
      max-width: 1280px;
      margin: 0 auto;
      padding: 2rem;
      text-align: center;
    }

    body {
      font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
      line-height: 1.5;
      font-weight: 400;
      color-scheme: light dark;
      color: rgba(255, 255, 255, 0.87);
      background-color: #242424;
      margin: 0;
      display: flex;
      place-items: center;
      min-width: 320px;
      min-height: 100vh;
    }

    h1 {
      font-size: 2.5em;
      line-height: 1.1;
      margin-bottom: 1rem;
    }
    ```

6.  **`frontend/src/App.tsx`**:

    Update `calculator-fullstack/frontend/src/App.tsx`:

    ```tsx
    import Calculator from './components/Calculator';
    import './App.css';

    function App() {
      return (
        <>
          <h1>Fullstack Calculator</h1>
          <Calculator />
        </>
      );
    }

    export default App;
    ```

7.  **`frontend/src/main.tsx`**:

    Ensure `calculator-fullstack/frontend/src/main.tsx` is set up (usually default from Vite):

    ```tsx
    import React from 'react';
    import ReactDOM from 'react-dom/client';
    import App from './App.tsx';
    import './index.css'; // Global styles, if any

    ReactDOM.createRoot(document.getElementById('root')!).render(
      <React.StrictMode>
        <App />
      </React.StrictMode>,
    );
    ```

8.  **`frontend/index.html`**:

    Ensure `calculator-fullstack/frontend/index.html` has the root div:

    ```html
    <!doctype html>
    <html lang="en">
      <head>
        <meta charset="UTF-8" />
        <link rel="icon" type="image/svg+xml" href="/vite.svg" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Fullstack Calculator</title>
      </head>
      <body>
        <div id="root"></div>
        <script type="module" src="/src/main.tsx"></script>
      </body>
    </html>
    ```

## 6. Connecting Frontend to Backend

### 6.1 Configure Vite Proxy

Update the Vite configuration to proxy API requests to the backend:

```javascript
// frontend/vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:3001',
        changeOrigin: true,
        secure: false,
        ws: true,
      }
    }
  }
});
```

### 6.2 Update API Service

Modify the API service to use the proxy:

```typescript
// frontend/src/services/calculatorApi.ts
import axios from 'axios';

const API_BASE_URL = '/api';  // This will be proxied to the backend

// Rest of the code remains the same
```

## 7. Running the Application

### 7.1 Start the Backend

In the backend directory:
```bash
npm run dev
```

The backend will start on `http://localhost:3001`.

### 7.2 Start the Frontend

In the frontend directory:
```bash
npm run dev
```

The frontend will start on `http://localhost:5173`.

## 8. Testing the Application

1. **Basic Operations**:
   - Test each operation (+, -, *, /, %)
   - Verify calculations are correct

2. **Error Handling**:
   - Test division by zero
   - Test invalid inputs
   - Test what happens when the backend is down

3. **Responsive Design**:
   - Resize the browser window to test responsiveness
   - Test on different screen sizes

## 9. Deployment

### 9.1 Backend Deployment

1. **Build the backend**:
   ```bash
   npm run build
   ```

2. **Run in production**:
   ```bash
   NODE_ENV=production node dist/server.js
   ```

3. **Using PM2 (recommended for production)**:
   ```bash
   npm install -g pm2
   pm2 start dist/server.js --name "calculator-api"
   ```

### 9.2 Frontend Deployment

1. **Build for production**:
   ```bash
   npm run build
   ```

2. **Serve static files**:
   - The built files will be in the `dist` directory
   - You can serve them using Nginx, Apache, or a static hosting service

3. **Update API Base URL**:
   For production, update the API base URL in the frontend:
   ```typescript
   const API_BASE_URL = process.env.NODE_ENV === 'production' 
     ? 'https://your-api-domain.com' 
     : '/api';
   ```

## 10. Troubleshooting

### Backend Issues
- **Port already in use**: Ensure no other service is using port 3001
  ```bash
  lsof -i :3001
  kill -9 <PID>
  ```

- **TypeScript errors**: Make sure all TypeScript types are correctly defined

### Frontend Issues
- **CORS errors**: Ensure the backend has CORS enabled and the proxy is properly configured
- **API connection issues**: Check if the backend is running and accessible

### General Issues
- Clear `node_modules` and reinstall dependencies:
  ```bash
  rm -rf node_modules
  npm install
  ```

## Next Steps

1. **Add Authentication**:
   - Implement user authentication with JWT
   - Add protected routes

2. **Add Tests**:
   - Unit tests for utility functions
   - Integration tests for API endpoints
   - End-to-end tests for the UI

3. **Enhance Features**:
   - Add calculation history
   - Implement scientific calculator functions
   - Add theme switching

4. **Improve Error Handling**:
   - More detailed error messages
   - Better error recovery

5. **Add Logging**:
   - Server-side logging
   - Client-side error tracking

## Conclusion

You've successfully built a fullstack calculator application with React and Express! This project demonstrates how to create a modern web application with a clean separation of concerns between the frontend and backend.

Feel free to extend this application with more features or use it as a starting point for other fullstack projects!
