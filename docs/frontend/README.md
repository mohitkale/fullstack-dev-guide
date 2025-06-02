# 🌐 Frontend Development Guide

[![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)](https://reactjs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Vite](https://img.shields.io/badge/Vite-B73BFE?style=for-the-badge&logo=vite&logoColor=FFD62E)](https://vitejs.dev/)

Welcome to the frontend development section! Here, you'll learn how to build the web interface for the WeAll social media application using React, TypeScript, and Vite.

> **Note**: This guide assumes you've already set up your development environment as described in the [Getting Started](../getting-started/README.md) guide.

## 📋 Table of Contents
1. [Introduction](#-introduction)
2. [Project Setup](#-project-setup)
3. [Folder Structure](#-folder-structure)
4. [Core Concepts](#-core-concepts)
5. [Building Components](#-building-components)
6. [State Management](#-state-management)
7. [Styling](#-styling)
8. [API Integration](#-api-integration)
9. [Testing](#-testing)
10. [Next Steps](#-next-steps)

## 🎯 Introduction

In this section, you'll learn how to build a modern, responsive web application with React and TypeScript. We'll cover everything from setting up your development environment to deploying your application.

### What You'll Build

1. **Authentication**
   - User registration and login
   - Password reset flow
   - Protected routes
   - Social login (Google, GitHub)

2. **Core Features**
   - News feed with infinite scroll
   - Create, edit, and delete posts
   - Like and comment on posts
   - User profiles with follow/unfollow
   - Real-time notifications
   - Search functionality

3. **UI/UX**
   - Responsive design
   - Dark/light theme
   - Loading states and error handling
   - Form validation
   - Accessible components

### Tech Stack

- **UI Library**: React 18
- **Language**: TypeScript
- **Build Tool**: Vite
- **State Management**: React Query + Context API
- **Styling**: CSS Modules + CSS Variables
- **Routing**: React Router v6
- **Form Handling**: React Hook Form
- **API Client**: Axios
- **Testing**: Jest + React Testing Library
- **Linting/Formatting**: ESLint + Prettier

### Prerequisites

Before you begin, make sure you have:

- Basic understanding of JavaScript/TypeScript
- Familiarity with React concepts (components, hooks, props)
- Node.js and npm/yarn installed
- Git version control

### Getting Help

If you get stuck at any point:

1. Check the [FAQ](#-faq) section
2. Search the [GitHub Issues](https://github.com/yourusername/mentorhub/issues)
3. Ask a question in our [Discord community](#)
4. Open a new issue with your question

## 🛠 Project Setup

### Prerequisites
- Node.js v18 or later
- npm or Yarn
- Git

### 1. Clone the Repository (if you haven't already)
```bash
git clone https://github.com/yourusername/mentorhub.git
cd mentorhub
```

### 2. Install Dependencies
```bash
# Navigate to the frontend directory
cd frontend

# Install dependencies
npm install
# or
yarn install
```

### 3. Set Up Environment Variables
```bash
# Copy the example environment file
cp .env.example .env

# Edit the .env file with your configuration
# For development, the defaults should work
VITE_API_URL=http://localhost:3000/api
VITE_APP_NAME=WeAll
VITE_APP_ENV=development
```

### 4. Start the Development Server
```bash
# Start the Vite development server
npm run dev
# or
yarn dev
```

### 5. Open in Browser
Open [http://localhost:5173](http://localhost:5173) in your preferred web browser.

### 6. (Optional) Build for Production
```bash
# Create a production build
npm run build
# or
yarn build

# Preview the production build locally
npm run preview
# or
yarn preview
```

### 7. Run Tests
```bash
# Run all tests
npm test
# or
yarn test

# Run tests in watch mode
npm test -- --watch
```

## 🏗️ Project Structure

```
frontend/
├── public/                    # Static files (images, fonts, etc.)
│   ├── favicon.ico
│   ├── logo192.png
│   └── logo512.png
├── src/
│   ├── assets/               # Static assets (images, icons, etc.)
│   │   ├── images/
│   │   └── icons/
│   │
│   ├── components/           # Reusable UI components
│   │   ├── common/           # Common components (Button, Input, etc.)
│   │   ├── layout/           # Layout components (Header, Footer, etc.)
│   │   ├── auth/             # Authentication components
│   │   ├── posts/            # Post-related components
│   │   └── users/            # User-related components
│   │
│   ├── config/             # Configuration files
│   │   ├── routes.tsx        # Route configurations
│   │   └── theme.ts          # Theme configuration
│   │
│   ├── contexts/            # React contexts
│   │   ├── AuthContext.tsx
│   │   └── ThemeContext.tsx
│   │
│   ├── hooks/               # Custom React hooks
│   │   ├── useAuth.ts
│   │   └── useApi.ts
│   │
│   ├── pages/               # Page components
│   │   ├── HomePage/
│   │   ├── LoginPage/
│   │   ├── SignupPage/
│   │   ├── ProfilePage/
│   │   └── SettingsPage/
│   │
│   ├── services/            # API services
│   │   ├── api.ts
│   │   ├── authService.ts
│   │   └── postService.ts
│   │
│   ├── store/               # State management
│   │   ├── slices/           # Redux slices
│   │   └── store.ts          # Redux store configuration
│   │
│   ├── styles/              # Global styles and themes
│   │   ├── base/
│   │   ├── components/
│   │   └── theme.ts
│   │
│   ├── types/               # TypeScript type definitions
│   │   ├── index.ts
│   │   └── models/
│   │
│   ├── utils/               # Utility functions
│   │   ├── formatters.ts
│   │   └── validators.ts
│   │
│   ├── App.tsx              # Main App component
│   ├── main.tsx              # Entry point
│   └── vite-env.d.ts         # Vite type definitions
│
├── .eslintrc.js            # ESLint configuration
├── .gitignore
├── index.html               # HTML template
├── package.json
├── README.md
├── tsconfig.json            # TypeScript configuration
└── vite.config.ts           # Vite configuration
```
## 🧩 Core Concepts

### 1. Component Architecture

We follow a component-based architecture where UI is broken down into small, reusable pieces. Here's how we structure our components:

#### Atomic Design Principles

1. **Atoms**: Basic building blocks (buttons, inputs, labels)
   - `Button.tsx`, `Input.tsx`, `Icon.tsx`

2. **Molecules**: Groups of atoms (search bar, form field with label)
   - `SearchBar.tsx`, `FormField.tsx`

3. **Organisms**: Complex UI components (header, sidebar, post card)
   - `Header.tsx`, `PostCard.tsx`, `CommentSection.tsx`

4. **Templates**: Page-level layouts
   - `MainLayout.tsx`, `AuthLayout.tsx`

5. **Pages**: Complete views
   - `HomePage.tsx`, `ProfilePage.tsx`

### 2. TypeScript Integration

We use TypeScript for type safety and better developer experience. Here's how we define types:

```typescript
// types/models/User.ts
export interface User {
  id: string;
  username: string;
  email: string;
  avatar?: string;
  bio?: string;
  createdAt: string;
  updatedAt: string;
}

// types/index.ts
export * from './models/User';
export * from './models/Post';
```

## 🏗️ Building Components

### 1. Creating a New Component

1. **Create a new file** in the appropriate directory:
   ```bash
   touch src/components/common/Button/Button.tsx
   touch src/components/common/Button/Button.module.css
   touch src/components/common/Button/index.ts
   ```

2. **Define the component** with TypeScript:
   ```tsx
   // Button.tsx
   import React from 'react';
   import styles from './Button.module.css';

   interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
     variant?: 'primary' | 'secondary' | 'danger';
     size?: 'small' | 'medium' | 'large';
     isLoading?: boolean;
   }


   export const Button: React.FC<ButtonProps> = ({
     children,
     variant = 'primary',
     size = 'medium',
     className = '',
     disabled = false,
     isLoading = false,
     ...props
   }) => {
     return (
       <button
         className={`${styles.button} ${styles[variant]} ${styles[size]} ${className}`}
         disabled={disabled || isLoading}
         {...props}
       >
         {isLoading ? 'Loading...' : children}
       </button>
     );
   };
   ```

3. **Style the component** with CSS Modules:
   ```css
   /* Button.module.css */
   .button {
     border: none;
     border-radius: 4px;
     cursor: pointer;
     font-weight: 500;
     transition: all 0.2s ease-in-out;
     display: inline-flex;
     align-items: center;
     justify-content: center;
     gap: 8px;
   }

   /* Variants */
   .primary {
     background-color: #3b82f6;
     color: white;
   }

   .primary:hover {
     background-color: #2563eb;
   }

   /* Sizes */
   .small {
     padding: 6px 12px;
     font-size: 0.875rem;
   }

   .medium {
     padding: 8px 16px;
     font-size: 1rem;
   }
   ```

4. **Export the component** from the index file:
   ```typescript
   // index.ts
   export { Button } from './Button';
   export type { ButtonProps } from './Button';
   ```

## 🧠 State Management

We use a combination of React Context and React Query for state management.

### 1. Authentication State with Context

```tsx
// contexts/AuthContext.tsx
import React, { createContext, useContext, useState, useEffect } from 'react';
import { User } from '../types';
import { getCurrentUser } from '../services/authService';

interface AuthContextType {
  user: User | null;
  isLoading: boolean;
  login: (userData: User) => void;
  logout: () => void;
}

const AuthContext = createContext<AuthContextType | undefined>(undefined);

export const AuthProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    const loadUser = async () => {
      try {
        const userData = await getCurrentUser();
        setUser(userData);
      } catch (error) {
        console.error('Failed to load user', error);
        setUser(null);
      } finally {
        setIsLoading(false);
      }
    };

    loadUser();
  }, []);

  const login = (userData: User) => {
    setUser(userData);
  };

  const logout = () => {
    // Clear tokens, etc.
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ user, isLoading, login, logout }}>
      {!isLoading && children}
    </AuthContext.Provider>
  );
};

export const useAuth = () => {
  const context = useContext(AuthContext);
  if (context === undefined) {
    throw new Error('useAuth must be used within an AuthProvider');
  }
  return context;
};
```

### 2. Server State with React Query

```tsx
// services/postService.ts
import { api } from './api';

export const getPosts = async (page = 1, limit = 10) => {
  const response = await api.get(`/posts?page=${page}&limit=${limit}`);
  return response.data;
};

export const createPost = async (postData: { content: string }) => {
  const response = await api.post('/posts', postData);
  return response.data;
};

// In your component
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { getPosts, createPost } from '../services/postService';

const PostsList = () => {
  const queryClient = useQueryClient();
  
  // Fetch posts
  const { data: posts, isLoading, error } = useQuery({
    queryKey: ['posts'],
    queryFn: () => getPosts(),
  });

  // Create post mutation
  const createPostMutation = useMutation({
    mutationFn: createPost,
    onSuccess: () => {
      // Invalidate and refetch posts after successful creation
      queryClient.invalidateQueries({ queryKey: ['posts'] });
    },
  });

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error loading posts</div>;

  return (
    <div>
      <button onClick={() => createPostMutation.mutate({ content: 'New post' })}>
        Create Post
      </button>
      {posts?.map((post) => (
        <PostCard key={post.id} post={post} />
      ))}
    </div>
  );
};
```

## 🎨 Styling

We use CSS Modules for component-scoped styling with CSS Variables for theming.

### 1. Theme Configuration

```css
/* styles/theme.css */
:root {
  /* Colors */
  --color-primary: #3b82f6;
  --color-primary-dark: #2563eb;
  --color-text: #1f2937;
  --color-text-light: #6b7280;
  --color-bg: #ffffff;
  --color-bg-secondary: #f3f4f6;
  --color-border: #e5e7eb;
  
  /* Spacing */
  --spacing-1: 0.25rem;
  --spacing-2: 0.5rem;
  --spacing-4: 1rem;
  
  /* Typography */
  --font-sans: 'Inter', system-ui, -apple-system, sans-serif;
  --font-mono: 'Fira Code', monospace;
}

/* Dark theme */
[data-theme='dark'] {
  --color-text: #f3f4f6;
  --color-text-light: #9ca3af;
  --color-bg: #111827;
  --color-bg-secondary: #1f2937;
  --color-border: #374151;
}
```

### 2. Using CSS Modules

```tsx
// components/PostCard/PostCard.module.css
.card {
  background: var(--color-bg);
  border: 1px solid var(--color-border);
  border-radius: 8px;
  padding: var(--spacing-4);
  margin-bottom: var(--spacing-4);
  transition: box-shadow 0.2s ease;
}

.card:hover {
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
}

.title {
  font-size: 1.25rem;
  font-weight: 600;
  color: var(--color-text);
  margin-bottom: var(--spacing-2);
}

.body {
  color: var(--color-text-light);
  line-height: 1.5;
}
```

## 🔄 API Integration

We use Axios as our HTTP client with request/response interceptors for handling authentication.

### 1. API Client Setup

```typescript
// services/api.ts
import axios from 'axios';

const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:3000/api';

export const api = axios.create({
  baseURL: API_URL,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Request interceptor
api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// Response interceptor
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Handle unauthorized (e.g., redirect to login)
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

// Auth service example
export const login = async (credentials: { email: string; password: string }) => {
  const response = await api.post('/auth/login', credentials);
  return response.data;
};
```

## 🧪 Testing

We use Jest and React Testing Library for testing our components.

### 1. Setting Up Tests

```bash
# Install testing dependencies
npm install --save-dev @testing-library/react @testing-library/jest-dom @testing-library/user-event jest @types/jest ts-jest
```

### 2. Example Test

```tsx
// components/Button/Button.test.tsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import '@testing-library/jest-dom';
import { Button } from './Button';

describe('Button', () => {
  it('renders the button with correct text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  it('calls onClick handler when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    fireEvent.click(screen.getByText('Click me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('shows loading state when isLoading is true', () => {
    render(<Button isLoading>Click me</Button>);
    expect(screen.getByText('Loading...')).toBeInTheDocument();
  });
});
```

## 🚀 Deployment

### 1. Building for Production

```bash
# Build the app
npm run build

# The built files will be in the `dist` directory
```

### 2. Environment Variables

Make sure to set the following environment variables in your production environment:

```
VITE_API_URL=https://api.yourdomain.com
VITE_APP_NAME=WeAll
VITE_APP_ENV=production
```

## 📚 Additional Resources

- [React Documentation](https://reactjs.org/docs/getting-started.html)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [React Query Documentation](https://tanstack.com/query/latest)
- [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/)
- [Vite Documentation](https://vitejs.dev/guide/)

## ❓ FAQ

### How do I add a new page?
1. Create a new file in the `src/pages` directory
2. Define your page component
3. Add a route in `src/config/routes.tsx`
4. Add a link in your navigation component

### How do I handle forms?
We recommend using React Hook Form for form handling. See the example in the [Form Handling](#form-handling) section.

### How do I add a new API endpoint?
1. Add a new function in the appropriate service file (e.g., `postService.ts`)
2. Use the `api` client to make the request
3. Export the function
4. Use it in your component with React Query or a regular function call

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guide](../../CONTRIBUTING.md) for more information.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](../../LICENSE) file for details.
│   │   ├── layout/     # Layout components (Header, Footer, etc.)
│   │   └── features/   # Feature-specific components
│   ├── hooks/          # Custom React hooks
│   ├── pages/          # Page components
│   ├── services/       # API services
│   ├── store/          # State management
│   ├── styles/         # Global styles
│   ├── types/          # TypeScript type definitions
│   ├── utils/          # Utility functions
│   ├── App.tsx         # Main App component
│   └── main.tsx        # Application entry point
└── ...
```

## 🧩 Core Concepts

### 1. React with TypeScript

We use TypeScript for type safety and better developer experience. Here's a simple component example:

```tsx
// components/common/Button.tsx
import React from 'react';

type ButtonProps = {
  children: React.ReactNode;
  variant?: 'primary' | 'secondary' | 'outline';
  onClick?: () => void;
  disabled?: boolean;
};

export const Button = ({
  children,
  variant = 'primary',
  onClick,
  disabled = false,
}: ButtonProps) => {
  return (
    <button 
      className={`btn btn-${variant}`}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </button>
  );
};
```

### 2. Component Composition

Build small, reusable components and compose them together:

```tsx
// pages/HomePage.tsx
import { PostList } from '../components/features/PostList';
import { CreatePost } from '../components/features/CreatePost';
import { Stories } from '../components/features/Stories';

export const HomePage = () => {
  return (
    <div className="home-page">
      <div className="main-content">
        <Stories />
        <CreatePost />
        <PostList />
      </div>
      <div className="sidebar">
        {/* Sidebar content */}
      </div>
    </div>
  );
};
```

## 🏗️ Building Components

### 1. Create a New Component

1. Create a new file in the appropriate folder under `src/components`
2. Follow the component naming convention: `PascalCase.tsx`
3. Export your component as a named export

Example:

```tsx
// components/features/PostCard.tsx
import React from 'react';

type Post = {
  id: string;
  content: string;
  author: {
    name: string;
    avatar: string;
  };
  likes: number;
  comments: number;
  timestamp: string;
};

type PostCardProps = {
  post: Post;
};

export const PostCard = ({ post }: PostCardProps) => {
  return (
    <div className="post-card">
      <div className="post-header">
        <img 
          src={post.author.avatar} 
          alt={post.author.name}
          className="avatar"
        />
        <div>
          <h4>{post.author.name}</h4>
          <span className="timestamp">{post.timestamp}</span>
        </div>
      </div>
      <div className="post-content">
        <p>{post.content}</p>
      </div>
      <div className="post-actions">
        <button>❤️ {post.likes}</button>
        <button>💬 {post.comments}</button>
        <button>🔄 Share</button>
      </div>
    </div>
  );
};
```

## 🧠 State Management

We'll use React Context for global state management. Here's a simple example:

```tsx
// context/AuthContext.tsx
import React, { createContext, useContext, useState } from 'react';

type User = {
  id: string;
  name: string;
  email: string;
  avatar: string;
};

type AuthContextType = {
  user: User | null;
  isAuthenticated: boolean;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
};

const AuthContext = createContext<AuthContextType | undefined>(undefined);

export const AuthProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [user, setUser] = useState<User | null>(null);

  const login = async (email: string, password: string) => {
    // Implement login logic
    // const response = await authService.login(email, password);
    // setUser(response.user);
  };

  const logout = () => {
    // Implement logout logic
    setUser(null);
  };

  return (
    <AuthContext.Provider
      value={{
        user,
        isAuthenticated: !!user,
        login,
        logout,
      }}
    >
      {children}
    </AuthContext.Provider>
  );
};

export const useAuth = () => {
  const context = useContext(AuthContext);
  if (context === undefined) {
    throw new Error('useAuth must be used within an AuthProvider');
  }
  return context;
};
```

## 🎨 Styling

We use plain CSS with CSS Modules for component-scoped styling. Here's an example:

```css
/* PostCard.module.css */
.postCard {
  background: white;
  border-radius: 8px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  margin-bottom: 20px;
  padding: 16px;
}

.postHeader {
  display: flex;
  align-items: center;
  margin-bottom: 12px;
}

.avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  margin-right: 12px;
}

.timestamp {
  color: #666;
  font-size: 0.9em;
}
```

## 🔄 API Integration

Create service files to handle API calls:

```ts
// services/postService.ts
import axios from 'axios';

const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:3000/api';

export const postService = {
  async getPosts(page = 1, limit = 10) {
    const response = await axios.get(`${API_URL}/posts`, {
      params: { page, limit },
    });
    return response.data;
  },

  async createPost(content: string, token: string) {
    const response = await axios.post(
      `${API_URL}/posts`,
      { content },
      {
        headers: {
          Authorization: `Bearer ${token}`,
        },
      }
    );
    return response.data;
  },
};
```

## 🧪 Testing

We use React Testing Library for testing our components:

```tsx
// components/__tests__/Button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from '../Button';

describe('Button', () => {
  it('renders the button with children', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  it('calls onClick when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    fireEvent.click(screen.getByText('Click me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

## 🚀 Next Steps

1. [Learn about backend integration](./backend/README.md)
2. [Explore mobile development](./mobile/README.md)
3. [Check out the component library](./COMPONENTS.md)
4. [Read about state management patterns](./STATE-MANAGEMENT.md)

## 📚 Additional Resources

- [React Documentation](https://react.dev/)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/)

Happy coding! 🎉
