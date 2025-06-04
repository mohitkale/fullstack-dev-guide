# React Events and State Management Guide

> **Note**: This guide assumes you've already set up a new React + TypeScript project using Vite. If you haven't, refer to the main [Frontend Development Guide](./README.md) for setup instructions.

## Table of Contents
1. [Understanding the Default Counter](#1-understanding-the-default-counter)
2. [Toggling Images on Click](#2-toggling-images-on-click)
3. [Handling Input Fields](#3-handling-input-fields)
4. [Creating a Login Form](#4-creating-a-login-form)
5. [Moving Login to a Separate Component](#5-moving-login-to-a-separate-component)
6. [Creating a Register Component](#6-creating-a-register-component)
7. [Parent-Child Communication](#7-parent-child-communication)
8. [Using useEffect Hook](#8-using-useeffect-hook)

## 1. Understanding the Default Counter

When you create a new React + Vite project, you'll see a counter example. Let's break down how it works:

```tsx
function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount((count) => count + 1)}>
        count is {count}
      </button>
    </div>
  );
}
```

### How it works:
1. `useState(0)` creates a state variable `count` initialized to 0
2. `setCount` is a function to update the `count` value
3. The `onClick` handler calls `setCount` with a new value
4. When state updates, React re-renders the component with the new value

### Alternative Implementations:

```tsx
// 1. Using a separate handler function
const handleClick = () => {
  setCount(count + 1);
};
<button onClick={handleClick}>Count: {count}</button>

// 2. Using the previous state
<button onClick={() => setCount(prevCount => prevCount + 1)}>
  Count: {count}
</button>
```

## 2. Toggling Images on Click

Let's modify the default Vite + React setup to show both logos side by side and swap their positions when clicking a button.

```tsx
import { useState } from 'react';
import reactLogo from './assets/react.svg';
import viteLogo from '/vite.svg';

function App() {
  const [logos, setLogos] = useState([reactLogo, viteLogo]);

  const swapLogos = () => {
    setLogos(prev => [prev[1], prev[0]]); // Swap the positions
  };

  return (
    <div>
      <div style={{ display: 'flex', gap: '20px', margin: '20px 0' }}>
        <img src={logos[0]} className="logo" alt="React logo" style={{ height: '100px' }} />
        <img src={logos[1]} className="logo" alt="Vite logo" style={{ height: '100px' }} />
      </div>
      <button onClick={swapLogos}>Swap Logos</button>
    </div>
  );
}
```

This example demonstrates:
- Using an array in state to manage multiple related values
- Updating state based on previous state
- Rendering lists of items in JSX

## 4. Creating a Login Form

Let's create a login form with proper state management and form handling.

### Login.tsx
```tsx
import { useState } from 'react';

// Define the shape of our form data
interface LoginFormData {
  username: string;
  password: string;
}

interface LoginProps {
  // Callback when form is submitted with valid data
  onSubmit: (data: LoginFormData) => void;
}

export function Login({ onSubmit }: LoginProps) {
  // Individual states for better control and independent validation
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [error, setError] = useState('');

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    
    // Basic validation
    if (!username.trim() || !password) {
      setError('Please fill in all fields');
      return;
    }
    
    // Clear any previous errors
    setError('');
    
    // Call the parent's onSubmit with the form data
    onSubmit({ username, password });
  };

  return (
    <form onSubmit={handleSubmit}>
      <h2>Login</h2>
      
      {error && <div style={{ color: 'red' }}>{error}</div>}
      
      <div>
        <label>
          Username:
          <input
            type="text"
            value={username}
            onChange={(e) => setUsername(e.target.value)}
            placeholder="Enter username"
          />
        </label>
      </div>
      
      <div>
        <label>
          Password:
          <input
            type="password"
            value={password}
            onChange={(e) => setPassword(e.target.value)}
            placeholder="Enter password"
          />
        </label>
      </div>
      
      <button type="submit">LOGIN</button>
    </form>
  );
}
```

### App.tsx
```tsx
import { useState } from 'react';
import { Login } from './Login';

function App() {
  const [loginData, setLoginData] = useState<{ username: string; password: string } | null>(null);

  const handleLogin = (data: { username: string; password: string }) => {
    // In a real app, you would validate and send this to your backend
    console.log('Login data:', data);
    setLoginData(data);
  };

  return (
    <div>
      <h1>React Events Guide</h1>
      
      <div style={{ display: 'flex', gap: '2rem', marginTop: '2rem' }}>
        <div>
          <Login onSubmit={handleLogin} />
          
          {loginData && (
            <div style={{ marginTop: '1rem' }}>
              <h3>Login Successful!</h3>
              <p>Username: {loginData.username}</p>
              <p>Password length: {'*'.repeat(loginData.password.length)}</p>
            </div>
          )}
        </div>
      </div>
    </div>
  );
}

export default App;
```

### Key Improvements:
1. **Individual States**: Using separate states for each form field for better control
2. **Type Safety**: Proper TypeScript interfaces for props and form data
3. **Controlled Components**: Each input is a controlled component
4. **Basic Validation**: Simple client-side validation
5. **Error Handling**: Shows error messages to the user
6. **Security**: Doesn't log or display actual passwords
7. **Separation of Concerns**: Login component handles UI, parent handles business logic

## 4. Creating a Login Form

Now, let's create a simple login form with username, password, and a login button.

```tsx
function App() {
  const [formData, setFormData] = useState({
    username: '',
    password: ''
  });
  const [loginResult, setLoginResult] = useState('');

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value
    }));
  };

  const handleLogin = () => {
    setLoginResult(JSON.stringify(formData, null, 2));
  };

  return (
    <div>
      <h2>Login</h2>
      <div>
        <div>Username: {formData.username}</div>
        <input
          type="text"
          name="username"
          value={formData.username}
          onChange={handleChange}
          placeholder="Username"
        />
      </div>
      <div>
        <div>Password: {formData.password}</div>
        <input
          type="password"
          name="password"
          value={formData.password}
          onChange={handleChange}
          placeholder="Password"
        />
      </div>
      <button onClick={handleLogin}>LOGIN</button>
      {loginResult && (
        <div>
          <h3>Login Data:</h3>
          <pre>{loginResult}</pre>
        </div>
      )}
    </div>
  );
}
```

## 5. Moving Login to a Separate Component

Let's move the login form to its own component.

### Login.tsx
```tsx
import { useState } from 'react';

interface LoginProps {
  onLogin: (data: { username: string; password: string }) => void;
}

export function Login({ onLogin }: LoginProps) {
  const [formData, setFormData] = useState({
    username: '',
    password: ''
  });

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value
    }));
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    onLogin(formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <h2>Login</h2>
      <div>
        <div>Username: {formData.username}</div>
        <input
          type="text"
          name="username"
          value={formData.username}
          onChange={handleChange}
          placeholder="Username"
        />
      </div>
      <div>
        <div>Password: {formData.password}</div>
        <input
          type="password"
          name="password"
          value={formData.password}
          onChange={handleChange}
          placeholder="Password"
        />
      </div>
      <button type="submit">LOGIN</button>
    </form>
  );
}
```

### App.tsx
```tsx
import { useState } from 'react';
import { Login } from './Login';

function App() {
  const [loginData, setLoginData] = useState('');

  const handleLogin = (data: { username: string; password: string }) => {
    setLoginData(JSON.stringify(data, null, 2));
  };

  return (
    <div>
      <h1>React Events Guide</h1>
      <Login onLogin={handleLogin} />
      {loginData && (
        <div>
          <h3>Login Data in Parent:</h3>
          <pre>{loginData}</pre>
        </div>
      )}
    </div>
  );
}

export default App;
```

## 6. Creating a Register Component

Now, let's create a more comprehensive registration form.

### Register.tsx
```tsx
import { useState } from 'react';

interface RegisterData {
  name: string;
  email: string;
  username: string;
  password: string;
  confirmPassword: string;
}

interface RegisterProps {
  onRegister: (data: RegisterData) => void;
}

export function Register({ onRegister }: RegisterProps) {
  const [formData, setFormData] = useState<RegisterData>({
    name: '',
    email: '',
    username: '',
    password: '',
    confirmPassword: ''
  });

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value
    }));
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    onRegister(formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <h2>Register</h2>
      
      <div>
        <div>Name: {formData.name}</div>
        <input
          type="text"
          name="name"
          value={formData.name}
          onChange={handleChange}
          placeholder="Full Name"
        />
      </div>

      <div>
        <div>Email: {formData.email}</div>
        <input
          type="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
          placeholder="Email"
        />
      </div>

      <div>
        <div>Username: {formData.username}</div>
        <input
          type="text"
          name="username"
          value={formData.username}
          onChange={handleChange}
          placeholder="Username"
        />
      </div>

      <div>
        <div>Password: {formData.password}</div>
        <input
          type="password"
          name="password"
          value={formData.password}
          onChange={handleChange}
          placeholder="Password"
        />
      </div>

      <div>
        <div>Confirm Password: {formData.confirmPassword}</div>
        <input
          type="password"
          name="confirmPassword"
          value={formData.confirmPassword}
          onChange={handleChange}
          placeholder="Confirm Password"
        />
      </div>

      <button type="submit">REGISTER</button>
    </form>
  );
}
```

### Updated App.tsx
```tsx
import { useState } from 'react';
import { Login } from './Login';
import { Register } from './Register';

function App() {
  const [loginData, setLoginData] = useState('');
  const [registerData, setRegisterData] = useState('');

  const handleLogin = (data: { username: string; password: string }) => {
    setLoginData(JSON.stringify(data, null, 2));
  };

  const handleRegister = (data: any) => {
    setRegisterData(JSON.stringify(data, null, 2));
  };

  return (
    <div>
      <h1>React Events Guide</h1>
      
      <div style={{ display: 'flex', gap: '2rem', marginTop: '2rem' }}>
        <div>
          <Login onLogin={handleLogin} />
          {loginData && (
            <div>
              <h4>Login Data in Parent:</h4>
              <pre>{loginData}</pre>
            </div>
          )}
        </div>

        <div>
          <Register onRegister={handleRegister} />
          {registerData && (
            <div>
              <h4>Registration Data in Parent:</h4>
              <pre>{registerData}</pre>
            </div>
          )}
        </div>
      </div>
    </div>
  );
}

export default App;
```

## 7. Parent-Child Communication

In React, components can communicate in several ways. Let's explore the most common patterns.

### 7.1 Passing Data from Parent to Child (Props)

```tsx
// Child Component
interface WelcomeProps {
  name: string;
  age?: number; // Optional prop
}

function Welcome({ name, age = 18 }: WelcomeProps) {
  return (
    <div>
      <h2>Hello, {name}!</h2>
      {age && <p>You are {age} years old</p>}
    </div>
  );
}

// Parent Component
function App() {
  return (
    <div>
      <Welcome name="Alice" age={25} />
      <Welcome name="Bob" />
    </div>
  );
}
```

### 7.2 Passing Functions as Props (Child to Parent Communication)

```tsx
// Child Component
interface ButtonProps {
  onClick: () => void;
  children: React.ReactNode;
}

function CustomButton({ onClick, children }: ButtonProps) {
  return <button onClick={onClick}>{children}</button>;
}

// Parent Component
function CounterApp() {
  const [count, setCount] = useState(0);

  const increment = () => setCount(c => c + 1);
  const decrement = () => setCount(c => c - 1);
  const reset = () => setCount(0);

  return (
    <div>
      <h2>Count: {count}</h2>
      <CustomButton onClick={increment}>Increment</CustomButton>
      <CustomButton onClick={decrement}>Decrement</CustomButton>
      <CustomButton onClick={reset}>Reset</CustomButton>
    </div>
  );
}
```

### 7.3 Using Context for Deeply Nested Components

```tsx
// Context.js
import { createContext, useContext, useState } from 'react';

const ThemeContext = createContext({
  theme: 'light',
  toggleTheme: () => {}
});

export function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setTheme] = useState('light');
  
  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      <div data-theme={theme}>
        {children}
      </div>
    </ThemeContext.Provider>
  );
}

export const useTheme = () => useContext(ThemeContext);

// ThemedButton.js
import { useTheme } from './ThemeContext';

function ThemedButton() {
  const { theme, toggleTheme } = useTheme();
  
  return (
    <button 
      onClick={toggleTheme}
      style={{
        background: theme === 'light' ? '#333' : '#fff',
        color: theme === 'light' ? '#fff' : '#333',
        padding: '10px 20px',
        border: 'none',
        borderRadius: '4px',
        cursor: 'pointer'
      }}
    >
      Toggle {theme === 'light' ? 'Dark' : 'Light'} Mode
    </button>
  );
}
```

## 8. Using useEffect Hook

The `useEffect` hook lets you perform side effects in function components.

### 8.1 Basic Usage

```tsx
import { useState, useEffect } from 'react';

function Timer() {
  const [seconds, setSeconds] = useState(0);

  // Similar to componentDidMount and componentDidUpdate
  useEffect(() => {
    const timer = setInterval(() => {
      setSeconds(s => s + 1);
    }, 1000);

    // Cleanup function (runs on component unmount)
    return () => clearInterval(timer);
  }, []); // Empty array means this effect runs once on mount

  return <div>Seconds: {seconds}</div>;
}
```

### 8.2 Fetching Data with useEffect

```tsx
import { useState, useEffect } from 'react';

interface User {
  id: number;
  name: string;
  email: string;
}

function UserProfile({ userId }: { userId: number }) {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchUser = async () => {
      try {
        setLoading(true);
        const response = await fetch(`https://api.example.com/users/${userId}`);
        if (!response.ok) {
          throw new Error('Failed to fetch user');
        }
        const data = await response.json();
        setUser(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchUser();
  }, [userId]); // Re-run when userId changes

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>No user found</div>;

  return (
    <div>
      <h2>{user.name}</h2>
      <p>Email: {user.email}</p>
    </div>
  );
}
```

### 8.3 Debouncing User Input

```tsx
import { useState, useEffect } from 'react';

function SearchBar({ onSearch }: { onSearch: (query: string) => void }) {
  const [query, setQuery] = useState('');
  const [debouncedQuery, setDebouncedQuery] = useState('');

  // Update debounced query after user stops typing for 500ms
  useEffect(() => {
    const timerId = setTimeout(() => {
      setDebouncedQuery(query);
    }, 500);

    // Cleanup function to clear the timeout
    return () => {
      clearTimeout(timerId);
    };
  }, [query]);

  // Call onSearch when debouncedQuery changes
  useEffect(() => {
    if (debouncedQuery) {
      onSearch(debouncedQuery);
    }
  }, [debouncedQuery, onSearch]);

  return (
    <input
      type="text"
      value={query}
      onChange={(e) => setQuery(e.target.value)}
      placeholder="Search..."
    />
  );
}
```

### 8.4 Tracking Window Size

```tsx
import { useState, useEffect } from 'react';

function useWindowSize() {
  const [windowSize, setWindowSize] = useState({
    width: typeof window !== 'undefined' ? window.innerWidth : 0,
    height: typeof window !== 'undefined' ? window.innerHeight : 0,
  });

  useEffect(() => {
    // Only run on client-side
    if (typeof window === 'undefined') return;


    const handleResize = () => {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    };

    // Add event listener
    window.addEventListener('resize', handleResize);
    
    // Call handler right away so state gets updated with initial window size
    handleResize();
    
    // Remove event listener on cleanup
    return () => window.removeEventListener('resize', handleResize);
  }, []); // Empty array ensures that effect is only run on mount and unmount

  return windowSize;
}

function ResponsiveComponent() {
  const { width } = useWindowSize();
  const isMobile = width < 768;

  return (
    <div>
      <p>Window width: {width}px</p>
      {isMobile ? (
        <p>Mobile view</p>
      ) : (
        <p>Desktop view</p>
      )}
    </div>
  );
}
```

## 9. Conclusion

In this guide, we've covered:
1. Basic event handling in React
2. Managing component state with `useState`
3. Handling form inputs and their changes
4. Creating reusable components
5. Passing data between parent and child components
6. Building forms with multiple fields
7. Advanced component communication patterns
8. Using `useEffect` for side effects

This foundation will help you understand how React handles user interactions and state management, which are essential skills for building interactive web applications.
