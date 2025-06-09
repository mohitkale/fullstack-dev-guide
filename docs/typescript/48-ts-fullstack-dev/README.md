# 48. TypeScript for Full-Stack Development (Frontend & Backend)

Using TypeScript across your entire stack—both frontend and backend—offers significant advantages, primarily by enabling end-to-end type safety and facilitating code sharing. This consistency can lead to more robust applications, improved developer experience, and reduced bugs.

This tutorial explores strategies, patterns, and tools for leveraging TypeScript in full-stack development.

## Table of Contents
1.  [Benefits of Full-Stack TypeScript](#benefits-fullstack-ts)
    *   End-to-End Type Safety.
    *   Shared Code and Types (DTOs, validation logic).
    *   Improved Developer Experience and Collaboration.
    *   Easier Refactoring.
2.  [Project Structure Strategies](#project-structure-fullstack)
    *   [**Monorepo Approach**](#monorepo-fullstack)
        *   Tools: Lerna, Nx, Yarn Workspaces, npm Workspaces, Turborepo.
        *   Benefits: Single source of truth, easier code sharing, atomic commits.
        *   Challenges: Build complexity, tooling setup.
        *   Example structure with a `shared` package.
    *   [**Separate Repositories Approach**](#separate-repos-fullstack)
        *   Managing shared types as a private NPM package.
        *   Pros: Independent deployment, simpler individual project builds.
        *   Cons: Overhead of package management, versioning challenges.
3.  [Sharing Code and Types](#sharing-code-types-fullstack)
    *   [**Data Transfer Objects (DTOs) / Interfaces**](#dtos-interfaces-fullstack)
        *   Defining API request/response shapes in a shared location.
        *   Example: User profile, API responses.
    *   [**Validation Logic**](#validation-logic-fullstack)
        *   Sharing validation rules (e.g., using Zod, Yup, io-ts) for both client-side pre-validation and server-side enforcement.
    *   [**Utility Functions**](#utility-functions-fullstack)
        *   Common helpers for string manipulation, date formatting, etc.
    *   [**Business Logic**](#business-logic-fullstack)
        *   Select pieces of core business logic that can be isomorphic.
4.  [API Layer Design with TypeScript](#api-layer-design-ts)
    *   [**REST APIs**](#rest-apis-fullstack-ts)
        *   Using frameworks like Express.js, NestJS, Fastify with TypeScript.
        *   Defining typed request handlers and responses.
    *   [**GraphQL APIs**](#graphql-apis-fullstack-ts)
        *   Tools like Apollo Server, TypeGraphQL, GraphQL Code Generator.
        *   Schema-first vs. Code-first approaches (see Tutorial #30).
    *   [**tRPC (TypeScript Remote Procedure Call)**](#trpc-fullstack-ts)
        *   End-to-end typesafe APIs without code generation.
        *   Defining routers on the backend, calling procedures on the frontend with full type inference.
        *   Benefits: Simplicity, excellent type safety, autocompletion.
5.  [Frontend Development with TypeScript](#frontend-dev-fullstack-ts)
    *   Frameworks: React, Angular, Vue, Svelte (all have excellent TS support).
    *   Consuming shared types for API calls.
    *   State management with TypeScript (Redux, Zustand, Pinia).
6.  [Backend Development with TypeScript](#backend-dev-fullstack-ts)
    *   Frameworks: Node.js with Express.js, NestJS, Fastify, AdonisJS.
    *   ORM/Query Builders with TypeScript: TypeORM, Prisma, Knex.js.
    *   Type-safe database interactions.
7.  [Build and Deployment Considerations](#build-deploy-fullstack-ts)
    *   Separate build processes for frontend and backend.
    *   Monorepo build orchestration (e.g., `nx build`, `turbo run build`).
    *   Environment variable management for different parts of the stack.
8.  [Example: Simple Monorepo with Shared Types](#example-monorepo-fullstack)
    *   Directory structure (`packages/shared`, `packages/frontend`, `packages/backend`).
    *   `packages/shared/src/types.ts`
    *   `packages/backend/src/server.ts` (using shared types)
    *   `packages/frontend/src/api.ts` (consuming shared types)
    *   Basic `tsconfig.json` configurations for sharing.
9.  [Challenges and Solutions](#challenges-solutions-fullstack)
    *   Managing dependencies in a monorepo.
    *   Ensuring type consistency across different `tsconfig.json` files.
    *   Build times and optimization.
10. [Conclusion](#conclusion-fullstack-ts)

## 1. Benefits of Full-Stack TypeScript <a name="benefits-fullstack-ts"></a>
*   **End-to-End Type Safety**: Catch errors at compile time that would otherwise surface at runtime, from database interactions to UI rendering.
*   **Shared Code**: Define types (DTOs, entities), validation logic, and utility functions once and use them on both client and server.
*   **Improved Developer Experience**: Better autocompletion, refactoring confidence, and clearer contracts between frontend and backend teams (or for solo full-stack developers).
*   **Reduced Misunderstandings**: Types act as a form of documentation for API contracts.

## 2. Project Structure Strategies <a name="project-structure-fullstack"></a>

### Monorepo Approach <a name="monorepo-fullstack"></a>
A monorepo stores all your project's code (frontend, backend, shared libraries) in a single repository.
*   **Tools**: Lerna, Nx, Yarn Workspaces, npm Workspaces, Turborepo.
*   **Example Structure**:
    ```
    my-fullstack-app/
    ├── packages/
    │   ├── frontend/        (e.g., React app)
    │   │   ├── src/
    │   │   └── package.json
    │   │   └── tsconfig.json
    │   ├── backend/         (e.g., Express app)
    │   │   ├── src/
    │   │   └── package.json
    │   │   └── tsconfig.json
    │   └── shared/          (shared types, utils)
    │       ├── src/
    │       └── package.json
    │       └── tsconfig.json
    ├── package.json         (root, for workspace config)
    └── tsconfig.base.json   (base TS config)
    ```
*   **Benefits**: Easier to share code (direct imports), atomic commits across stack, unified tooling.
*   **Challenges**: Can become complex to set up and manage build/CI processes for larger projects.

### Separate Repositories Approach <a name="separate-repos-fullstack"></a>
Frontend and backend (and any shared libraries) live in their own repositories.
*   Shared code is typically published as a private NPM package.
*   **Pros**: Simpler individual project builds, independent deployment schedules.
*   **Cons**: Overhead of managing and versioning the shared package, potential for type drift if not carefully managed.

## 3. Sharing Code and Types <a name="sharing-code-types-fullstack"></a>

### Data Transfer Objects (DTOs) / Interfaces <a name="dtos-interfaces-fullstack"></a>
Define the shape of data exchanged between client and server.
```typescript
// packages/shared/src/types/user.types.ts
export interface UserProfile {
  id: string;
  username: string;
  email: string;
  bio?: string;
}

export interface UpdateUserProfileDTO {
  username?: string;
  email?: string;
  bio?: string;
}
```

### Validation Logic <a name="validation-logic-fullstack"></a>
Use libraries like Zod to define schemas that can be used for validation on both ends.
```typescript
// packages/shared/src/validation/user.schemas.ts
import { z } from 'zod';

export const CreateUserSchema = z.object({
  username: z.string().min(3),
  email: z.string().email(),
  password: z.string().min(8),
});

export type CreateUserPayload = z.infer<typeof CreateUserSchema>;

// Backend usage (e.g., Express middleware)
// try { CreateUserSchema.parse(req.body); } catch (e) { /* handle error */ }

// Frontend usage (e.g., form validation)
// try { CreateUserSchema.parse(formData); } catch (e) { /* show errors */ }
```

## 4. API Layer Design with TypeScript <a name="api-layer-design-ts"></a>

### tRPC (TypeScript Remote Procedure Call) <a name="trpc-fullstack-ts"></a>
tRPC allows you to build end-to-end typesafe APIs without schema generation or code generation steps. It leverages TypeScript inference.

**Backend (defining a tRPC router):**
```typescript
// packages/backend/src/trpc.ts
import { initTRPC } from '@trpc/server';
import { z } from 'zod';
import { UserProfile } from '../../shared/src/types/user.types'; // Shared type

const t = initTRPC.create();

const users: UserProfile[] = [{ id: '1', username: 'Alice', email: 'alice@example.com' }];

export const appRouter = t.router({
  getUserById: t.procedure
    .input(z.object({ userId: z.string() }))
    .query(({ input }) => {
      return users.find(u => u.id === input.userId) || null;
    }),
  createUser: t.procedure
    .input(z.object({ username: z.string(), email: z.string().email() }))
    .mutation(({ input }) => {
      const newUser: UserProfile = { id: String(users.length + 1), ...input };
      users.push(newUser);
      return newUser;
    }),
});

export type AppRouter = typeof appRouter;
```

**Frontend (consuming the tRPC API):**
```typescript
// packages/frontend/src/trpcClient.ts
import { createTRPCProxyClient, httpBatchLink } from '@trpc/client';
import type { AppRouter } from '../../backend/src/trpc'; // Import type from backend!

export const trpc = createTRPCProxyClient<AppRouter>({
  links: [
    httpBatchLink({
      url: 'http://localhost:3001/trpc', // Your tRPC server URL
    }),
  ],
});

// Usage in a component
async function fetchUser() {
  // const user = await trpc.user.get.query({ userId: '1' }); // old syntax example
  const user = await trpc.getUserById.query({ userId: '1' });
  console.log(user?.username); // Autocompleted and type-checked!

  const newUser = await trpc.createUser.mutate({ username: 'Bob', email: 'bob@example.com' });
  console.log(newUser.id);
}
```
This provides incredible type safety and developer experience.

## 7. Build and Deployment Considerations <a name="build-deploy-fullstack-ts"></a>
*   **Monorepo Tooling**: Tools like Nx or Turborepo can manage complex build dependencies and cache build artifacts, speeding up CI/CD.
*   **`tsconfig.json`**: Each package (`frontend`, `backend`, `shared`) will typically have its own `tsconfig.json`, often extending a base configuration (`tsconfig.base.json` in the root).
    *   `shared` package: `module` might be `ESNext` for tree-shakability, `declaration: true`.
    *   `backend` package: `module` typically `CommonJS`, `target` for Node.js version.
    *   `frontend` package: `module` `ESNext` for bundlers, `jsx` settings for React/Vue.

## 8. Example: Simple Monorepo with Shared Types <a name="example-monorepo-fullstack"></a>
Refer to the structure in section 2.1.

**`packages/shared/src/types.ts`:**
```typescript
export interface Todo {
  id: string;
  text: string;
  completed: boolean;
}
```

**`packages/backend/tsconfig.json` (partial):**
```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "outDir": "dist",
    "module": "commonjs",
    "composite": true, // If other packages depend on it
    "rootDir": "src"
  },
  "include": ["src"],
  "references": [
    { "path": "../shared" }
  ]
}
```

**`packages/frontend/tsconfig.json` (partial):**
```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "outDir": "dist", // Or noEmit if bundler handles output
    "module": "esnext",
    "jsx": "react-jsx",
    "composite": true,
    "rootDir": "src"
  },
  "include": ["src"],
  "references": [
    { "path": "../shared" }
  ]
}
```
Using TypeScript project references (`references` in `tsconfig.json`) helps `tsc` understand dependencies between packages in a monorepo.

## 10. Conclusion <a name="conclusion-fullstack-ts"></a>
Adopting TypeScript for full-stack development introduces a powerful paradigm for building robust, maintainable, and scalable applications. By carefully structuring projects, sharing types and logic effectively, and leveraging tools like tRPC or GraphQL with code generation, teams can significantly improve development velocity and reduce errors. While it requires an initial setup investment, the long-term benefits of end-to-end type safety are substantial.

---

⬅️ [Back to TypeScript with Build Tools](../47-ts-build-tools/README.md) | ➡️ [Next: Advanced TypeScript Patterns and Best Practices](../49-ts-advanced-patterns-practices/README.md) *(Placeholder)*
