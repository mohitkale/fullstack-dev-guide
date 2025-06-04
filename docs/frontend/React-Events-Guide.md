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

## Conclusion

In this guide, we've covered:
1. Basic event handling in React
2. Managing component state with `useState`
3. Handling form inputs and their changes
4. Creating reusable components
5. Passing data between parent and child components
6. Building forms with multiple fields

This foundation will help you understand how React handles user interactions and state management, which are essential skills for building interactive web applications.
