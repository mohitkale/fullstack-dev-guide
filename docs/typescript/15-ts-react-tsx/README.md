# 15. TypeScript with React (TSX)

Using TypeScript with React enhances development by adding static typing to your components, props, state, and event handlers. This leads to more robust, maintainable, and understandable React applications. TSX is an extension of JSX that allows you to embed XML-like syntax within TypeScript code.

## Table of Contents
1.  [Setting Up a React TypeScript Project](#setting-up-react-ts)
    *   [Create React App](#create-react-app-ts)
    *   [Manual Setup](#manual-setup-ts-react)
2.  [Understanding TSX](#understanding-tsx)
3.  [Typing Functional Components](#typing-functional-components)
    *   [`React.FC` (or `React.FunctionComponent`)](#react-fc)
    *   [Typing Props](#typing-props)
    *   [Children Prop](#children-prop)
4.  [Typing State (`useState`)](#typing-usestate)
5.  [Typing Event Handlers](#typing-event-handlers)
    *   [Mouse Events (`React.MouseEvent`)](#mouse-events)
    *   [Form Events (`React.ChangeEvent`, `React.FormEvent`)](#form-events)
    *   [Keyboard Events (`React.KeyboardEvent`)](#keyboard-events)
6.  [Typing Refs (`useRef`)](#typing-useref)
    *   [Referencing DOM Elements](#ref-dom-elements)
    *   [Generic Refs](#ref-generic-values)
7.  [Typing Context (`createContext`, `useContext`)](#typing-usecontext)
8.  [Typing Default Props](#typing-default-props)
9.  [Typing Class Components (Brief Overview)](#typing-class-components)
    *   [`React.Component<P, S>`](#react-component-ps)
10. [Higher-Order Components (HOCs) with TypeScript](#typing-hocs)
11. [Utility Types in React](#utility-types-react)
12. [Common Patterns and Best Practices](#common-patterns-best-practices-react-ts)
13. [Key Takeaways](#key-takeaways-react-ts)

## 1. Setting Up a React TypeScript Project <a name="setting-up-react-ts"></a>

### Create React App <a name="create-react-app-ts"></a>
The easiest way to start a new React TypeScript project is with Create React App:
```bash
npx create-react-app my-ts-app --template typescript
# or
yarn create react-app my-ts-app --template typescript
```
This command sets up a new React project with TypeScript configured out of the box, including a `tsconfig.json` file and necessary type definitions (`@types/react`, `@types/react-dom`).

### Manual Setup <a name="manual-setup-ts-react"></a>
For a manual setup, you'd need to:
1.  Install TypeScript: `npm install --save-dev typescript @types/node @types/react @types/react-dom @types/jest`
2.  Configure `tsconfig.json` with appropriate settings, especially:
    ```json
    {
      "compilerOptions": {
        "target": "es5",
        "lib": ["dom", "dom.iterable", "esnext"],
        "allowJs": true,
        "skipLibCheck": true,
        "esModuleInterop": true,
        "allowSyntheticDefaultImports": true,
        "strict": true,
        "forceConsistentCasingInFileNames": true,
        "module": "esnext",
        "moduleResolution": "node",
        "resolveJsonModule": true,
        "isolatedModules": true,
        "noEmit": true,
        "jsx": "react-jsx" // or "react" for older versions
      },
      "include": ["src"]
    }
    ```
3.  Set up your build process (e.g., Webpack with `ts-loader` or Babel with `@babel/preset-typescript`).

## 2. Understanding TSX <a name="understanding-tsx"></a>
TSX is TypeScript's version of JSX. It allows you to write HTML-like syntax directly within your TypeScript files. The TypeScript compiler (or Babel) transforms this syntax into `React.createElement` calls.

```tsx
// MyComponent.tsx
import React from 'react';

const MyComponent = () => {
  return (
    <div>
      <h1>Hello from TSX!</h1>
    </div>
  );
};

export default MyComponent;
```
Key difference from JS: In `.tsx` files, type assertions using angle brackets (`<Type>value`) can conflict with JSX syntax. Use `value as Type` instead.

## 3. Typing Functional Components <a name="typing-functional-components"></a>

### `React.FC` (or `React.FunctionComponent`) <a name="react-fc"></a>
`React.FC` is a generic type that provides type checking for functional components, including props and an implicit `children` prop.

```tsx
import React from 'react';

interface GreetingProps {
  name: string;
  messageCount?: number; // Optional prop
}

const Greeting: React.FC<GreetingProps> = ({ name, messageCount = 0, children }) => {
  return (
    <div>
      <h2>Hello, {name}! You have {messageCount} new messages.</h2>
      {children}
    </div>
  );
};

export default Greeting;
```
**Note on `React.FC`**: While `React.FC` implicitly includes `children`, some developers prefer to explicitly define `children` in their props interface for clarity, especially if `children` are not always expected or have a specific type.

Alternatively, you can type props directly on the function signature without `React.FC`:
```tsx
interface GreetingProps {
  name: string;
  children?: React.ReactNode; // Explicitly define children if needed
}

const Greeting = ({ name, children }: GreetingProps): JSX.Element => {
  return <div>Hello, {name}{children}</div>;
};
```
This approach is often preferred as it's more explicit and handles default props better with destructuring.

### Typing Props <a name="typing-props"></a>
Use interfaces or type aliases to define the shape of your component's props.

```tsx
import React from 'react';

// Using an interface
interface ButtonProps {
  label: string;
  onClick: () => void;
  disabled?: boolean;
}

export const Button: React.FC<ButtonProps> = ({ label, onClick, disabled = false }) => {
  return (
    <button onClick={onClick} disabled={disabled}>
      {label}
    </button>
  );
};

// Using a type alias
type UserCardProps = {
  userId: number;
  name: string;
  avatarUrl?: string;
};

export const UserCard: React.FC<UserCardProps> = ({ userId, name, avatarUrl }) => {
  return (
    <div className="user-card">
      {avatarUrl && <img src={avatarUrl} alt={name} />}
      <p>ID: {userId}</p>
      <p>Name: {name}</p>
    </div>
  );
};
```

### Children Prop <a name="children-prop"></a>
`React.ReactNode` is the most common type for `children`. It can represent anything React can render: primitives, React elements, arrays, or fragments.

```tsx
import React, { ReactNode } from 'react';

interface LayoutProps {
  header: ReactNode;
  children: ReactNode; // Explicit children prop
  footer?: ReactNode;
}

export const Layout: React.FC<LayoutProps> = ({ header, children, footer }) => {
  return (
    <div className="layout">
      <header>{header}</header>
      <main>{children}</main>
      {footer && <footer>{footer}</footer>}
    </div>
  );
};
```

## 4. Typing State (`useState`) <a name="typing-usestate"></a>
TypeScript can often infer the type of state from the initial value provided to `useState`. However, you can also provide an explicit type argument.

```tsx
import React, { useState } from 'react';

interface User {
  id: number;
  name: string;
}

const UserProfile = () => {
  // Inferred type: string
  const [username, setUsername] = useState("guest");

  // Explicit type: number | null
  const [count, setCount] = useState<number | null>(null);

  // Explicit type for object: User | null
  const [user, setUser] = useState<User | null>(null);

  const login = () => {
    setUser({ id: 1, name: "Alice" });
    setUsername("Alice");
    setCount(0);
  };

  return (
    <div>
      <p>User: {username}</p>
      <p>Count: {count === null ? 'N/A' : count}</p>
      {user && <p>Logged in as: {user.name}</p>}
      <button onClick={login}>Login</button>
    </div>
  );
};

export default UserProfile;
```

## 5. Typing Event Handlers <a name="typing-event-handlers"></a>
React provides types for various event objects in the `@types/react` package.

### Mouse Events (`React.MouseEvent`) <a name="mouse-events"></a>
```tsx
import React from 'react';

const MyButton = () => {
  const handleClick = (event: React.MouseEvent<HTMLButtonElement>) => {
    console.log('Button clicked!', event.currentTarget.tagName);
    // event.preventDefault(); // if it were a link or form submit button
  };

  return <button onClick={handleClick}>Click Me</button>;
};
```

### Form Events (`React.ChangeEvent`, `React.FormEvent`) <a name="form-events"></a>
```tsx
import React, { useState } from 'react';

const MyForm = () => {
  const [inputValue, setInputValue] = useState('');

  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    setInputValue(event.target.value);
  };

  const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    console.log('Form submitted with value:', inputValue);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" value={inputValue} onChange={handleChange} />
      <button type="submit">Submit</button>
    </form>
  );
};
```

### Keyboard Events (`React.KeyboardEvent`) <a name="keyboard-events"></a>
```tsx
import React from 'react';

const TextInputWithEnter = () => {
  const handleKeyPress = (event: React.KeyboardEvent<HTMLInputElement>) => {
    if (event.key === 'Enter') {
      console.log('Enter pressed! Value:', event.currentTarget.value);
    }
  };

  return <input type="text" onKeyPress={handleKeyPress} placeholder="Type and press Enter" />;
};
```

## 6. Typing Refs (`useRef`) <a name="typing-useref"></a>

### Referencing DOM Elements <a name="ref-dom-elements"></a>
When using `useRef` to reference a DOM element, provide the element type as a generic argument and initialize with `null`.

```tsx
import React, { useRef, useEffect } from 'react';

const FocusInput = () => {
  // Specify the element type (HTMLInputElement)
  const inputRef = useRef<HTMLInputElement>(null);

  useEffect(() => {
    // The 'current' property might be null, so check it
    inputRef.current?.focus();
  }, []);

  return <input ref={inputRef} type="text" placeholder="I will be focused" />;
};
```

### Generic Refs <a name="ref-generic-values"></a>
`useRef` can also hold any mutable value, similar to instance variables in classes.

```tsx
import React, { useRef, useEffect } from 'react';

const TimerComponent = () => {
  const intervalRef = useRef<number | null>(null); // Can also use NodeJS.Timeout for Node environments

  useEffect(() => {
    intervalRef.current = window.setInterval(() => {
      console.log('Timer tick');
    }, 1000);

    return () => {
      if (intervalRef.current !== null) {
        clearInterval(intervalRef.current);
      }
    };
  }, []);

  return <div>Timer is running (check console)</div>;
};
```

## 7. Typing Context (`createContext`, `useContext`) <a name="typing-usecontext"></a>
When creating context, provide a type for the context value. It's common to provide a default value that matches the type, or `null` if a default isn't meaningful (requiring a check in consumers).

```tsx
import React, { createContext, useContext, useState, ReactNode } from 'react';

interface ThemeContextType {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
}

// Provide a default value that matches the type structure
const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export const ThemeProvider: React.FC<{ children: ReactNode }> = ({ children }) => {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');

  const toggleTheme = () => {
    setTheme(prevTheme => (prevTheme === 'light' ? 'dark' : 'light'));
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

export const useTheme = () => {
  const context = useContext(ThemeContext);
  if (context === undefined) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
};

// Example Consumer Component
const ThemeToggleButton = () => {
  const { theme, toggleTheme } = useTheme();
  return (
    <button onClick={toggleTheme}>
      Switch to {theme === 'light' ? 'Dark' : 'Light'} Mode
    </button>
  );
};
```

## 8. Typing Default Props <a name="typing-default-props"></a>
For functional components, default props are best handled with default parameter values in destructuring.

```tsx
import React from 'react';

interface MyComponentProps {
  title: string;
  count?: number; // Optional prop
  description: string;
}

const MyComponent = ({
  title,
  count = 0, // Default value for count
  description
}: MyComponentProps): JSX.Element => {
  return (
    <div>
      <h1>{title}</h1>
      <p>Count: {count}</p>
      <p>{description}</p>
    </div>
  );
};

// If using React.FC, defaultProps can be set but has some quirks with type inference.
// The default parameter approach is generally preferred.
// MyComponent.defaultProps = {
//   count: 0,
// };
```

## 9. Typing Class Components (Brief Overview) <a name="typing-class-components"></a>
Though less common in modern React, class components are typed using `React.Component<P, S>`, where `P` is the props type and `S` is the state type.

```tsx
import React from 'react';

interface CounterProps {
  initialCount?: number;
}

interface CounterState {
  count: number;
}

class Counter extends React.Component<CounterProps, CounterState> {
  // Set default props
  static defaultProps = {
    initialCount: 0,
  };

  // Initialize state
  state: CounterState = {
    count: this.props.initialCount!, // Non-null assertion due to defaultProps
  };

  increment = () => {
    this.setState(prevState => ({ count: prevState.count + 1 }));
  };

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.increment}>Increment</button>
      </div>
    );
  }
}
```

## 10. Higher-Order Components (HOCs) with TypeScript <a name="typing-hocs"></a>
Typing HOCs can be complex. You need to correctly type the props passed into the HOC, the props added by the HOC, and the props of the wrapped component.

```tsx
import React, { ComponentType } from 'react';

interface WithLoadingProps {
  isLoading: boolean;
}

// This HOC adds an 'isLoading' prop to the wrapped component
const withLoading = <P extends object>(
  WrappedComponent: ComponentType<P & WithLoadingProps>
) => {
  // The props for the component returned by the HOC
  // It should accept the original props P, and it will manage isLoading internally or via its own props.
  // For simplicity, let's assume the HOC itself takes a prop to control loading.
  type HOCProps = P & { initialLoadingState?: boolean };

  return class extends React.Component<HOCProps> {
    render() {
      const { initialLoadingState = true, ...restProps } = this.props as HOCProps;
      // In a real HOC, isLoading might come from state, context, or props of the HOC itself.
      // Here, we'll just pass it through for simplicity, assuming the HOC is configured elsewhere.
      const isLoading = initialLoadingState; // Placeholder for actual loading logic

      return <WrappedComponent {...(restProps as P)} isLoading={isLoading} />;
    }
  };
};

// Example Usage
interface MyDataProps {
  data: string;
  // isLoading prop will be injected by HOC
}

const MyDataComponent: React.FC<MyDataProps & WithLoadingProps> = ({ data, isLoading }) => {
  if (isLoading) {
    return <p>Loading data...</p>;
  }
  return <p>Data: {data}</p>;
};

const MyDataWithLoading = withLoading(MyDataComponent);

// Usage of the HOC-enhanced component
// <MyDataWithLoading data="Some fetched data" initialLoadingState={false} />
```
Typing HOCs correctly often involves generics and careful consideration of prop transformations.

## 11. Utility Types in React <a name="utility-types-react"></a>
TypeScript's utility types are very useful in React contexts:
*   `Partial<Props>`: For components where all props are optional (e.g., an update form).
*   `Required<Props>`: To make all props required.
*   `Pick<Props, 'key1' | 'key2'>`: To create a new props type with only specific keys.
*   `Omit<Props, 'key1' | 'key2'>`: To create a new props type omitting specific keys.
*   `ComponentProps<typeof MyComponent>`: (From React's types) To extract prop types from an existing component.

```tsx
import React from 'react';

interface UserProfileProps {
  id: number;
  name: string;
  email: string;
  bio?: string;
}

// Props for a component that only displays user's name and email
type UserDisplayProps = Pick<UserProfileProps, 'name' | 'email'>;

const UserDisplay: React.FC<UserDisplayProps> = ({ name, email }) => (
  <div><p>{name} ({email})</p></div>
);

// Props for an edit form, where all fields are optional initially
type UserEditFormProps = Partial<Omit<UserProfileProps, 'id'>>; // id is not editable

const UserEditForm: React.FC<UserEditFormProps> = (props) => {
  // ... form logic
  return <form>{/* form fields for name, email, bio */}</form>;
};
```

## 12. Common Patterns and Best Practices <a name="common-patterns-best-practices-react-ts"></a>
*   **Be Explicit with Types:** While inference is good, explicitly typing props and state often improves clarity.
*   **Interfaces vs. Types for Props:** Both work. Interfaces are often preferred for public APIs (like component props) due to better error messages and extensibility. Type aliases are great for unions, intersections, or simpler shapes.
*   **`React.ReactNode` for Children:** Use this for maximum flexibility for the `children` prop.
*   **Type Event Handlers:** Use `React.MouseEvent`, `React.ChangeEvent`, etc., to get correct event object types.
*   **`strictNullChecks`:** Keep this enabled in `tsconfig.json` for robust null/undefined handling.
*   **Avoid `any`:** Use `unknown` if a type is truly unknown and then perform type checking or casting.

## 13. Key Takeaways <a name="key-takeaways-react-ts"></a>
*   TypeScript significantly improves the development experience with React by adding static typing.
*   TSX is JSX with TypeScript type-checking capabilities.
*   Use `interface` or `type` to define props for functional and class components.
*   `useState`, `useRef`, `useContext` can be typed explicitly using generic arguments.
*   React provides specific types for event objects (e.g., `React.MouseEvent`).
*   Utility types help in creating flexible and precise prop types.

By leveraging TypeScript with React, you can build more reliable, scalable, and maintainable applications.

---

⬅️ [Back to Utility Types](../14-ts-utility-types/README.md) | ➡️ [Next: TypeScript Project Configuration and Compiler Options](../16-ts-project-config-compiler-options/README.md)
