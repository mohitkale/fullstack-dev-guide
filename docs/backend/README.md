# 🖥️ Backend Development Guide

Welcome to the backend development section! Here, you'll learn how to build the server-side components of the WeAll social media application. We'll cover three different backend implementations:

1. **Express + PostgreSQL** (with Prisma)
2. **Express + MongoDB** (with Mongoose)
3. **Supabase** (Backend as a Service)

## 📋 Table of Contents
1. [Choose Your Backend](#-choose-your-backend)
2. [Project Structure](#-project-structure)
3. [Environment Setup](#-environment-setup)
4. [Database Design](#-database-design)
5. [API Development](#-api-development)
6. [Authentication](#-authentication)
7. [Error Handling](#-error-handling)
8. [Testing](#-testing)
9. [Deployment](#-deployment)
10. [Next Steps](#-next-steps)

## 🎯 Choose Your Backend

Select the backend technology you want to work with:

- [Express + PostgreSQL Guide](./postgres/README.md)
- [Express + MongoDB Guide](./mongo/README.md)
- [Supabase Guide](./supabase/README.md)

## 📁 Project Structure (Express Example)

```
backend-express/
├── src/
│   ├── config/         # Configuration files
│   ├── controllers/     # Route controllers
│   ├── middleware/      # Custom middleware
│   ├── models/          # Database models
│   ├── routes/          # Route definitions
│   ├── services/        # Business logic
│   ├── utils/           # Utility functions
│   ├── validators/      # Request validation
│   ├── app.ts           # Express app setup
│   └── server.ts        # Server startup
├── tests/              # Test files
├── .env                # Environment variables
├── package.json
└── tsconfig.json
```

## ⚙️ Environment Setup

1. **Node.js**
   Ensure you have Node.js 18+ installed:
   ```bash
   node --version
   ```

2. **Database**
   - For PostgreSQL: Install [PostgreSQL](https://www.postgresql.org/download/)
   - For MongoDB: Install [MongoDB](https://www.mongodb.com/try/download/community)
   - For Supabase: No local installation needed

3. **Install Dependencies**
   ```bash
   # For Express backends
   npm install express cors helmet morgan dotenv
   npm install -D typescript ts-node @types/node @types/express @types/cors
   
   # For PostgreSQL
   npm install pg @prisma/client
   npm install -D prisma
   
   # For MongoDB
   npm install mongoose
   
   # For Supabase
   npm install @supabase/supabase-js
   ```

## 🗄️ Database Design

### PostgreSQL with Prisma

1. Initialize Prisma:
   ```bash
   npx prisma init
   ```

2. Define your schema in `prisma/schema.prisma`:
   ```prisma
   // prisma/schema.prisma
   datasource db {
     provider = "postgresql"
     url      = env("DATABASE_URL")
   }

   generator client {
     provider = "prisma-client-js"
   }

   model User {
     id        String   @id @default(uuid())
     email     String   @unique
     name      String
     password  String
     posts     Post[]
     createdAt DateTime @default(now())
     updatedAt DateTime @updatedAt
   }

   model Post {
     id        String   @id @default(uuid())
     content   String
     author    User     @relation(fields: [authorId], references: [id])
     authorId  String
     likes     Like[]
     comments  Comment[]
     createdAt DateTime @default(now())
     updatedAt DateTime @updatedAt
   }
   ```

### MongoDB with Mongoose

```javascript
// models/User.js
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  email: { type: String, required: true, unique: true },
  name: { type: String, required: true },
  password: { type: String, required: true },
  posts: [{ type: mongoose.Schema.Types.ObjectId, ref: 'Post' }],
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now }
});

module.exports = mongoose.model('User', userSchema);
```

## 🚀 API Development

### Express Server Setup

```typescript
// src/app.ts
import express from 'express';
import cors from 'cors';
import helmet from 'helmet';
import morgan from 'morgan';
import { errorHandler } from './middleware/errorHandler';
import authRoutes from './routes/auth';
import postRoutes from './routes/posts';

const app = express();

// Middleware
app.use(express.json());
app.use(cors());
app.use(helmet());
app.use(morgan('dev'));

// Routes
app.use('/api/auth', authRoutes);
app.use('/api/posts', postRoutes);

// Error handling
app.use(errorHandler);

export default app;
```

### Example Route Controller

```typescript
// controllers/postController.ts
import { Request, Response } from 'express';
import { prisma } from '../utils/prisma';

export const createPost = async (req: Request, res: Response) => {
  try {
    const { content } = req.body;
    const userId = req.user.id; // From auth middleware

    const post = await prisma.post.create({
      data: {
        content,
        authorId: userId,
      },
      include: {
        author: {
          select: {
            id: true,
            name: true,
            email: true,
          },
        },
      },
    });

    res.status(201).json({
      success: true,
      data: post,
    });
  } catch (error) {
    next(error);
  }
};
```

## 🔐 Authentication

### JWT Authentication

```typescript
// middleware/auth.ts
import { Request, Response, NextFunction } from 'express';
import jwt from 'jsonwebtoken';

export const authenticate = (req: Request, res: Response, next: NextFunction) => {
  try {
    const token = req.header('Authorization')?.replace('Bearer ', '');
    
    if (!token) {
      return res.status(401).json({ message: 'No token, authorization denied' });
    }

    const decoded = jwt.verify(token, process.env.JWT_SECRET!);
    req.user = decoded.user;
    next();
  } catch (err) {
    res.status(401).json({ message: 'Token is not valid' });
  }
};
```

## 🧪 Testing

### Using Jest

```typescript
// __tests__/auth.test.ts
import request from 'supertest';
import app from '../src/app';
import { prisma } from '../src/utils/prisma';

describe('Authentication', () => {
  beforeAll(async () => {
    // Set up test database
    await prisma.user.deleteMany({});
  });

  afterAll(async () => {
    await prisma.$disconnect();
  });

  describe('POST /api/auth/register', () => {
    it('should register a new user', async () => {
      const res = await request(app)
        .post('/api/auth/register')
        .send({
          name: 'Test User',
          email: 'test@example.com',
          password: 'password123',
        });

      expect(res.statusCode).toEqual(201);
      expect(res.body).toHaveProperty('token');
    });
  });
});
```

## 🚀 Deployment

### Environment Variables

Create a `.env` file:

```env
# Server
PORT=3000
NODE_ENV=development

# Database (PostgreSQL example)
DATABASE_URL=postgresql://user:password@localhost:5432/weall?schema=public

# JWT
JWT_SECRET=your_jwt_secret
JWT_EXPIRE=30d

# CORS
CORS_ORIGIN=http://localhost:5173
```

### PM2 (Production Process Manager)

```bash
# Install PM2 globally
npm install -g pm2

# Start your app
pm2 start dist/server.js --name weall-api

# Save process list
pm2 save

# Generate startup script
pm2 startup

# Monitor logs
pm2 logs weall-api
```

## 📚 Next Steps

1. [Frontend Integration](../frontend/README.md)
2. [Mobile App Development](../mobile/README.md)
3. [API Documentation](./API.md)
4. [Performance Optimization](./PERFORMANCE.md)

## 🔗 Additional Resources

- [Express.js Documentation](https://expressjs.com/)
- [Prisma Documentation](https://www.prisma.io/docs/)
- [MongoDB with Mongoose](https://mongoosejs.com/)
- [Supabase Documentation](https://supabase.com/docs)

Happy coding! 🚀
