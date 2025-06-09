# 28. Using TypeScript with Popular Frameworks (React, Node.js)

TypeScript's strong typing capabilities can significantly enhance development when working with popular JavaScript frameworks like React and Node.js (with Express). It helps catch errors early, improves code readability, and makes refactoring safer.

This tutorial focuses on the essentials of integrating TypeScript into these environments, covering setup, common patterns, and best practices.

## Table of Contents
1.  [Why Use TypeScript with Frameworks?](#why-ts-with-frameworks)
2.  [TypeScript with React](#ts-with-react)
    *   [Setting Up a React Project with TypeScript](#react-setup-ts)
        *   [Create React App (CRA)](#react-cra-ts)
        *   [Vite](#react-vite-ts)
    *   [Typing Functional Components](#react-typing-functional-components)
        *   [Typing Props (`React.FC`)](#react-typing-props)
        *   [Typing Children](#react-typing-children)
    *   [Typing Hooks](#react-typing-hooks)
        *   [`useState`](#react-usestate)
        *   [`useEffect`](#react-useeffect)
        *   [`useContext`](#react-usecontext)
        *   [`useReducer`](#react-usereducer)
        *   [`useRef`](#react-useref)
    *   [Typing Event Handlers](#react-typing-event-handlers)
    *   [Typing Class Components (Brief Overview)](#react-typing-class-components)
3.  [TypeScript with Node.js & Express](#ts-with-nodejs-express)
    *   [Setting Up a Node.js/Express Project with TypeScript](#node-setup-ts)
    *   [Basic Server Setup](#node-basic-server)
    *   [Typing Request and Response Objects](#node-typing-req-res)
    *   [Typing Middleware](#node-typing-middleware)
    *   [Typing Route Handlers](#node-typing-route-handlers)
4.  [Integrating TypeScript with Existing JavaScript Projects](#ts-with-existing-js)
    *   [Gradual Adoption](#gradual-adoption)
    *   [Using `allowJs` and JSDoc](#allowjs-jsdoc)
5.  [Best Practices](#ts-frameworks-best-practices)
6.  [Key Takeaways](#ts-frameworks-key-takeaways)

## 1. Why Use TypeScript with Frameworks? <a name="why-ts-with-frameworks"></a>
*   **Early Error Detection**: Catch type-related bugs at compile time, not runtime.
*   **Improved Code Quality & Readability**: Explicit types make code easier to understand and maintain.
*   **Enhanced Tooling**: Better autocompletion, refactoring, and navigation in IDEs.
*   **Scalability**: Makes managing large codebases more manageable.
*   **Better Collaboration**: Clear contracts (types) for components and functions improve teamwork.

## 2. TypeScript with React <a name="ts-with-react"></a>
React and TypeScript are a powerful combination for building robust UIs.

### Setting Up a React Project with TypeScript <a name="react-setup-ts"></a>

#### Create React App (CRA) <a name="react-cra-ts"></a>
Create React App offers a simple way to start a new React project with TypeScript:
```bash
npx create-react-app my-ts-app --template typescript
# or
yarn create react-app my-ts-app --template typescript
```
This sets up a React project with TypeScript configured, including a `tsconfig.json` file.

#### Vite <a name="react-vite-ts"></a>
Vite is a modern build tool that provides a fast development experience. To create a React + TypeScript project with Vite:
```bash
npm create vite@latest my-vite-ts-app -- --template react-ts
# or
yarn create vite my-vite-ts-app --template react-ts
```

### Typing Functional Components <a name="react-typing-functional-components"></a>

#### Typing Props (`React.FC`) <a name="react-typing-props"></a>
You can define an interface or type alias for your component's props and use `React.FC` (Functional Component) generic type.

```tsx
// src/components/Greeting.tsx
import React from 'react';

interface GreetingProps {
  name: string;
  messageCount?: number; // Optional prop
}

const Greeting: React.FC<GreetingProps> = ({ name, messageCount = 0 }) => {
  return (
    <div>
      <h1>Hello, {name}!</h1>
      {messageCount > 0 && <p>You have {messageCount} new messages.</p>}
    </div>
  );
};

export default Greeting;
```
`React.FC` also implicitly includes `children` props, though it's often recommended to type `children` explicitly if used.

#### Typing Children <a name="react-typing-children"></a>
If your component accepts children, you can type them explicitly:
```tsx
import React, { ReactNode } from 'react';

interface LayoutProps {
  children: ReactNode; // For any valid React child (string, element, array, etc.)
  // OR
  // children: JSX.Element; // For a single JSX element
  // children: JSX.Element | JSX.Element[]; // For one or more JSX elements
}

const Layout: React.FC<LayoutProps> = ({ children }) => {
  return <div style={{ padding: '20px', border: '1px solid #ccc' }}>{children}</div>;
};

export default Layout;
```

### Typing Hooks <a name="react-typing-hooks"></a>

#### `useState` <a name="react-usestate"></a>
TypeScript can often infer the type for `useState` from the initial value. However, you can provide an explicit type if needed (e.g., for union types, or when the initial value is `null` or `undefined`).

```tsx
import React, { useState } from 'react';

const Counter: React.FC = () => {
  const [count, setCount] = useState<number>(0); // Explicit type
  const [user, setUser] = useState<{ name: string; age: number } | null>(null); // Union type

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(prevCount => prevCount + 1)}>Increment</button>
      {user && <p>User: {user.name}</p>}
    </div>
  );
};
```

#### `useEffect` <a name="react-useeffect"></a>
`useEffect` itself doesn't require special typing for its callback, but the values within its dependency array are type-checked.

```tsx
import React, { useState, useEffect } from 'react';

interface UserProfileProps {
  userId: number;
}

const UserProfile: React.FC<UserProfileProps> = ({ userId }) => {
  const [userData, setUserData] = useState<any>(null); // Replace 'any' with a proper type

  useEffect(() => {
    // console.log('Fetching data for user:', userId);
    // fetch(`/api/users/${userId}`)
    //   .then(res => res.json())
    //   .then(data => setUserData(data));
  }, [userId]); // userId is type-checked

  if (!userData) return <p>Loading...</p>;
  return <div>{/* Render user data */}</div>;
};
```

#### `useContext` <a name="react-usecontext"></a>
When creating a context, provide a default value that matches the context's type.

```tsx
import React, { createContext, useContext, useState, ReactNode } from 'react';

interface ThemeContextType {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
}

// Provide a default value that matches the type, or use `null` and check for it.
const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export const useTheme = () => {
  const context = useContext(ThemeContext);
  if (context === undefined) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
};

export const ThemeProvider: React.FC<{ children: ReactNode }> = ({ children }) => {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');
  const toggleTheme = () => setTheme(prevTheme => (prevTheme === 'light' ? 'dark' : 'light'));

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};
```

#### `useReducer` <a name="react-usereducer"></a>
Type the state, action, and reducer function.

```tsx
import React, { useReducer } from 'react';

interface State {
  count: number;
}

type Action = { type: 'increment' } | { type: 'decrement' } | { type: 'reset' };

const initialState: State = { count: 0 };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case 'increment': return { count: state.count + 1 };
    case 'decrement': return { count: state.count - 1 };
    case 'reset': return initialState;
    default: throw new Error('Unknown action');
  }
}

const ReducerCounter: React.FC = () => {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
    </>
  );
};
```

#### `useRef` <a name="react-useref"></a>
Type the ref based on the element it will refer to. Initialize with `null`.

```tsx
import React, { useRef, useEffect } from 'react';

const MyInput: React.FC = () => {
  const inputRef = useRef<HTMLInputElement>(null);

  useEffect(() => {
    inputRef.current?.focus(); // Use optional chaining as current might be null
  }, []);

  return <input ref={inputRef} type="text" />;
};
```

### Typing Event Handlers <a name="react-typing-event-handlers"></a>
React provides types for various events (e.g., `React.MouseEvent`, `React.ChangeEvent`).

```tsx
import React, { useState } from 'react';

const FormComponent: React.FC = () => {
  const [value, setValue] = useState('');

  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    setValue(event.target.value);
  };

  const handleClick = (event: React.MouseEvent<HTMLButtonElement>) => {
    console.log('Button clicked!', event.clientX, event.clientY);
  };

  return (
    <form>
      <input type="text" value={value} onChange={handleChange} />
      <button type="button" onClick={handleClick}>Submit</button>
    </form>
  );
};
```

### Typing Class Components (Brief Overview) <a name="react-typing-class-components"></a>
While functional components with hooks are more common, you can also type class components.

```tsx
import React, { Component } from 'react';

interface MyClassComponentProps {
  title: string;
}

interface MyClassComponentState {
  count: number;
}

class MyClassComponent extends Component<MyClassComponentProps, MyClassComponentState> {
  state: MyClassComponentState = {
    count: 0,
  };

  increment = () => {
    this.setState(prevState => ({ count: prevState.count + 1 }));
  };

  render() {
    return (
      <div>
        <h2>{this.props.title}</h2>
        <p>Count: {this.state.count}</p>
        <button onClick={this.increment}>Increment</button>
      </div>
    );
  }
}
```

## 3. TypeScript with Node.js & Express <a name="ts-with-nodejs-express"></a>
TypeScript can bring structure and safety to backend development with Node.js and Express.

### Setting Up a Node.js/Express Project with TypeScript <a name="node-setup-ts"></a>
1.  **Initialize npm project**:
    ```bash
    npm init -y
    ```
2.  **Install dependencies**:
    ```bash
    npm install express typescript ts-node @types/express @types/node --save-dev
    # or
    yarn add express typescript ts-node @types/express @types/node --dev
    ```
    *   `express`: The Express framework.
    *   `typescript`: The TypeScript compiler.
    *   `ts-node`: To run TypeScript files directly without pre-compiling (for development).
    *   `@types/express`, `@types/node`: Type definitions for Express and Node.js.
3.  **Create `tsconfig.json`**:
    ```bash
    npx tsc --init
    ```
    Adjust `tsconfig.json` (e.g., set `outDir`, `rootDir`, `module`, `target`, `esModuleInterop: true`, `resolveJsonModule: true`).
    Example `compilerOptions` snippet:
    ```json
    {
      "target": "es2020",
      "module": "commonjs",
      "rootDir": "./src",
      "outDir": "./dist",
      "esModuleInterop": true,
      "strict": true,
      "skipLibCheck": true
    }
    ```
4.  **Add scripts to `package.json`**:
    ```json
    "scripts": {
      "build": "tsc",
      "start": "node dist/server.js",
      "dev": "ts-node src/server.ts",
      "watch": "tsc -w"
    }
    ```

### Basic Server Setup <a name="node-basic-server"></a>
Create `src/server.ts`:
```typescript
// src/server.ts
import express, { Application, Request, Response, NextFunction } from 'express';

const app: Application = express();
const port: number = 3000;

app.use(express.json()); // Middleware to parse JSON bodies

app.get('/', (req: Request, res: Response) => {
  res.send('Hello, TypeScript with Express!');
});

// Example of a simple error handler (optional)
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});

app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

### Typing Request and Response Objects <a name="node-typing-req-res"></a>
Express types (`Request`, `Response`, `NextFunction`) are imported from `express`.

For custom properties on `req` or `res` (e.g., from middleware), you can extend Express's Request type using declaration merging or create custom interfaces.

```typescript
// src/types/express.d.ts (Example for extending Request)
// Ensure this file is included in your tsconfig.json
declare namespace Express {
  export interface Request {
    user?: { id: string; role: string }; // Example: user property added by auth middleware
  }
}
```

### Typing Middleware <a name="node-typing-middleware"></a>
Middleware functions also use `Request`, `Response`, and `NextFunction`.

```typescript
// src/middleware/logger.ts
import { Request, Response, NextFunction } from 'express';

export const loggerMiddleware = (req: Request, res: Response, next: NextFunction) => {
  console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
  next();
};

// In server.ts
// import { loggerMiddleware } from './middleware/logger';
// app.use(loggerMiddleware);
```

### Typing Route Handlers <a name="node-typing-route-handlers"></a>
You can define types for request bodies, query parameters, and route parameters.

```typescript
// src/routes/userRoutes.ts
import express, { Router, Request, Response } from 'express';

const router: Router = express.Router();

interface User {
  id: string;
  name: string;
  email: string;
}

interface CreateUserRequestBody {
  name: string;
  email: string;
}

// POST /users - Create a new user
router.post('/', (req: Request<{}, {}, CreateUserRequestBody>, res: Response<User | { error: string }>) => {
  const { name, email } = req.body;
  if (!name || !email) {
    return res.status(400).json({ error: 'Name and email are required' });
  }
  const newUser: User = { id: Date.now().toString(), name, email };
  // In a real app, save to database
  res.status(201).json(newUser);
});

// GET /users/:id - Get user by ID
interface GetUserRequestParams {
  id: string;
}
router.get('/:id', (req: Request<GetUserRequestParams>, res: Response<User | { error: string }>) => {
  const { id } = req.params;
  // In a real app, fetch from database
  // const user = db.findUserById(id);
  // if (!user) return res.status(404).json({ error: 'User not found' });
  res.json({ id, name: 'Dummy User', email: 'dummy@example.com' }); // Placeholder
});

export default router;

// In server.ts
// import userRoutes from './routes/userRoutes';
// app.use('/users', userRoutes);
```
*   `Request<P, ResBody, ReqBody, ReqQuery>`
    *   `P`: Route parameters (e.g., `{ id: string }`)
    *   `ResBody`: Response body type
    *   `ReqBody`: Request body type
    *   `ReqQuery`: Request query parameters type

## 4. Integrating TypeScript with Existing JavaScript Projects <a name="ts-with-existing-js"></a>

### Gradual Adoption <a name="gradual-adoption"></a>
TypeScript can be introduced into an existing JavaScript project incrementally.
1.  Add TypeScript and type definitions to your project.
2.  Create a `tsconfig.json` file.
3.  Enable `allowJs: true` in `compilerOptions` to allow JS files to be part of the compilation.
4.  Start by converting a few files from `.js` to `.ts` and fixing any type errors.
5.  Gradually convert more files as time permits.

### Using `allowJs` and JSDoc <a name="allowjs-jsdoc"></a>
With `allowJs: true` and `checkJs: true` in `tsconfig.json`, TypeScript can type-check JavaScript files using JSDoc annotations.

```javascript
// utils.js
/**
 * Adds two numbers.
 * @param {number} a The first number.
 * @param {number} b The second number.
 * @returns {number} The sum of the two numbers.
 */
export function add(a, b) {
  return a + b;
}
```
This allows you to get some benefits of TypeScript without fully converting files.

## 5. Best Practices <a name="ts-frameworks-best-practices"></a>
*   **Use Strict Mode**: Enable `strict: true` in `tsconfig.json` for robust type checking.
*   **Type Props and State**: Explicitly type component props and state in React.
*   **Leverage Utility Types**: Use TypeScript's utility types (`Partial`, `Pick`, `Omit`, etc.) to create new types from existing ones.
*   **Type API Responses**: Define interfaces for data fetched from APIs.
*   **Keep Types Close**: Define types close to where they are used, or in a central `types` directory for shared types.
*   **Install `@types` Packages**: For third-party libraries that don't include their own types, install the corresponding `@types/package-name` from DefinitelyTyped.
*   **Avoid `any`**: Use `any` sparingly. Prefer `unknown` if the type is truly unknown and perform type checking before use.

## 6. Key Takeaways <a name="ts-frameworks-key-takeaways"></a>
*   TypeScript significantly improves the development experience with React and Node.js/Express by adding static typing.
*   Setting up TypeScript involves installing necessary packages, configuring `tsconfig.json`, and using type definitions.
*   In React, typing props, state, hooks, and event handlers are common practices.
*   In Node.js/Express, typing request/response objects, middleware, and route handlers enhances backend code quality.
*   TypeScript can be adopted gradually into existing JavaScript projects.

---

⬅️ [Back to TypeScript Configuration & Compiler Options In-Depth](../27-ts-config-compiler-options/README.md) | ➡️ [Next Section: Further Learning / Advanced Topics](../XX-placeholder/README.md) (Placeholder)
