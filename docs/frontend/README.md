# 🌐 Frontend Development Guide

Welcome to the frontend development section! Here, you'll learn how to build the web interface for the WeAll social media application using React and TypeScript.

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

In this section, you'll build the user interface for WeAll, including:

- User authentication (login/signup)
- News feed with posts
- User profiles
- Notifications
- And more!

## 🛠 Project Setup

1. **Navigate to the frontend directory**
   ```bash
   cd frontend
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Start the development server**
   ```bash
   npm run dev
   ```

4. Open [http://localhost:5173](http://localhost:5173) in your browser

## 📁 Folder Structure

```
frontend/
├── public/             # Static files
├── src/
│   ├── assets/         # Images, fonts, etc.
│   ├── components/     # Reusable UI components
│   │   ├── common/     # Common components (Button, Input, etc.)
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
