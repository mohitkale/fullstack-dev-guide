# 30. TypeScript with GraphQL (Client and Server)

GraphQL's strongly typed nature makes it a perfect match for TypeScript. Using TypeScript with GraphQL allows you to build robust, type-safe APIs and client applications, catching many potential errors at compile time and improving developer experience with autocompletion and type checking.

This tutorial explores how TypeScript enhances GraphQL development on both the server and client sides, covering schema definition, type generation from schemas, writing typed resolvers, and consuming GraphQL APIs with type-safe clients.

## Table of Contents
1.  [Why TypeScript for GraphQL?](#why-ts-graphql)
    *   End-to-End Type Safety
    *   Improved Developer Experience (DX)
    *   Easier Refactoring
    *   Auto-generated Types
2.  [GraphQL Schema Definition with TypeScript in Mind](#schema-definition-ts)
    *   Schema Definition Language (SDL)-First Approach
    *   Code-First Approach (e.g., TypeGraphQL, Nexus)
3.  [Server-Side TypeScript with GraphQL](#server-side-ts-graphql)
    *   [Setting up a GraphQL Server with TypeScript](#setup-graphql-server-ts)
        *   Core Libraries: `graphql`, `graphql-tools`
        *   Popular Frameworks:
            *   **Apollo Server:** Versatile, widely used.
            *   **TypeGraphQL:** Code-first approach using classes and decorators.
            *   **NestJS (GraphQL Module):** Integrates GraphQL seamlessly into the NestJS ecosystem.
            *   **GraphQL Yoga:** Lightweight and feature-rich GraphQL server.
    *   [Writing Resolvers with TypeScript](#writing-resolvers-ts)
        *   Typing resolver arguments (`parent`, `args`, `context`, `info`)
        *   Ensuring resolver return types match the schema
    *   [Context Typing](#context-typing-graphql)
        *   Providing type-safe context to resolvers (e.g., for authentication, data loaders)
    *   [Generating Types from SDL (Server-Side)](#generating-types-sdl-server)
        *   Using tools like GraphQL Code Generator
4.  [Client-Side TypeScript with GraphQL](#client-side-ts-graphql)
    *   [Generating Types from Schema and Operations](#generating-types-client)
        *   **GraphQL Code Generator:** The de-facto standard for generating types for queries, mutations, subscriptions, and schema types.
        *   Plugins for TypeScript, React Apollo, urql, etc.
    *   [Typed GraphQL Clients](#typed-graphql-clients)
        *   **Apollo Client:** Comprehensive state management and GraphQL client.
            *   Using generated types with `useQuery`, `useMutation`.
        *   **urql:** A highly customizable and extensible GraphQL client.
        *   **Relay:** Facebook's GraphQL client, often used with its own compiler.
    *   [Writing Typed Queries, Mutations, and Subscriptions](#typed-operations-client)
        *   Ensuring variables and response data are type-checked.
5.  [End-to-End Type Safety](#end-to-end-type-safety-graphql)
    *   The ideal scenario: Schema -> Server Types -> Client Types
    *   Benefits of a single source of truth (the GraphQL schema)
6.  [Popular Tools and Libraries](#popular-tools-libraries-graphql-ts)
    *   `graphql-js`: The JavaScript reference implementation of GraphQL.
    *   `@graphql-tools/schema`: For creating executable GraphQL schemas.
    *   `GraphQL Code Generator` (`@graphql-codegen/cli`)
    *   `Apollo Server`, `Apollo Client`
    *   `TypeGraphQL`, `Nexus Schema`
    *   `urql`
7.  [Best Practices](#best-practices-ts-graphql)
    *   Keep your schema as the single source of truth.
    *   Automate type generation in your development workflow.
    *   Use strict TypeScript settings.
    *   Type your resolver context thoroughly.
    *   Leverage generated types for variables and results on the client.
8.  [Key Takeaways](#key-takeaways-ts-graphql)

## 1. Why TypeScript for GraphQL? <a name="why-ts-graphql"></a>
*   **End-to-End Type Safety:** From database to backend resolver to frontend component, TypeScript helps ensure data consistency.
*   **Improved Developer Experience (DX):** Autocompletion for queries, mutations, resolver arguments, and result data. Catch schema mismatches early.
*   **Easier Refactoring:** Changing schema types? TypeScript will highlight where your server or client code needs updates.
*   **Auto-generated Types:** Tools can generate TypeScript types directly from your GraphQL schema, saving significant effort and reducing errors.

## 2. GraphQL Schema Definition with TypeScript in Mind <a name="schema-definition-ts"></a>

### Schema Definition Language (SDL)-First Approach
This is the most common approach. You define your schema in `.graphql` files using the GraphQL SDL.
```graphql
# schema.graphql
type User {
  id: ID!
  username: String!
  email: String
}

type Query {
  getUser(id: ID!): User
  allUsers: [User!]
}

# ... mutations, etc.
```
Tools like GraphQL Code Generator can then generate TypeScript types from these SDL files for both server and client.

### Code-First Approach
You define your schema programmatically using TypeScript code, often with classes and decorators or fluent APIs.
*   **TypeGraphQL:**
    ```typescript
    import { ObjectType, Field, ID, Resolver, Query, Arg } from "type-graphql";

    @ObjectType()
    class User {
      @Field(type => ID)
      id: string;

      @Field()
      username: string;

      @Field({ nullable: true })
      email?: string;
    }

    @Resolver(User)
    class UserResolver {
      // Sample data
      private users: User[] = [
        { id: "1", username: "Alice", email: "alice@example.com" },
        { id: "2", username: "Bob" },
      ];

      @Query(returns => User, { nullable: true })
      async getUser(@Arg("id", type => ID) id: string): Promise<User | undefined> {
        return this.users.find(user => user.id === id);
      }

      @Query(returns => [User])
      async allUsers(): Promise<User[]> {
        return this.users;
      }
    }
    // ... build schema with buildSchema({ resolvers: [UserResolver] })
    ```
*   **Nexus Schema:** Uses a fluent API to define types.
    ```typescript
    import { objectType, queryType, makeSchema, stringArg, nonNull } from 'nexus';

    const User = objectType({
      name: 'User',
      definition(t) {
        t.nonNull.id('id');
        t.nonNull.string('username');
        t.string('email');
      },
    });

    const Query = queryType({
      definition(t) {
        t.field('getUser', {
          type: User,
          args: {
            id: nonNull(stringArg()),
          },
          resolve: (parent, { id }, ctx) => { /* ... */ }
        });
      },
    });

    // const schema = makeSchema({ types: [User, Query], ... });
    ```
Code-first approaches can offer better type inference and reduce boilerplate by generating the SDL from your TypeScript code.

## 3. Server-Side TypeScript with GraphQL <a name="server-side-ts-graphql"></a>

### Setting up a GraphQL Server with TypeScript <a name="setup-graphql-server-ts"></a>
Popular choices include:
*   **Apollo Server:**
    ```typescript
    import { ApolloServer } from '@apollo/server';
    import { startStandaloneServer } from '@apollo/server/standalone';
    import { readFileSync } from 'fs';
    import path from 'path';

    // Assuming you have a schema.graphql and resolvers.ts
    const typeDefs = readFileSync(path.join(__dirname, 'schema.graphql'), 'utf-8');
    // import resolvers from './resolvers'; // Your resolver map

    interface MyContext {
      // Define your context type, e.g., for auth
      token?: string;
    }

    const server = new ApolloServer<MyContext>({
      typeDefs,
      // resolvers,
    });

    async function startServer() {
      const { url } = await startStandaloneServer(server, {
        listen: { port: 4000 },
        context: async ({ req }) => ({
          token: req.headers.authorization,
        }),
      });
      console.log(`🚀 Server ready at ${url}`);
    }
    startServer();
    ```

### Writing Resolvers with TypeScript <a name="writing-resolvers-ts"></a>
Resolvers are functions that provide the data for each field in your schema.

GraphQL Code Generator can generate types for your resolvers based on your schema:
```typescript
// Example generated types (conceptual)
interface ResolversParentTypes {
  Query: {};
  User: UserDbModel; // Assuming UserDbModel is your database model type
  String: string;
  ID: string;
}

interface QueryResolvers<ContextType = any, ParentType = ResolversParentTypes['Query']> {
  getUser: (parent: ParentType, args: { id: string }, context: ContextType, info: GraphQLResolveInfo) => Promise<ResolversParentTypes['User'] | null>;
  allUsers: (parent: ParentType, args: {}, context: ContextType, info: GraphQLResolveInfo) => Promise<Array<ResolversParentTypes['User']>>;
}

// Your resolvers.ts
import { QueryResolvers } from './generated/graphql'; // Path to generated types

const resolvers: { Query: QueryResolvers<MyContext> } = {
  Query: {
    async getUser(_parent, { id }, context, _info) {
      // `id` is typed as string
      // `context` is MyContext
      // Return type should match UserDbModel or be compatible
      console.log('Token from context:', context.token);
      // Fetch user from database by id
      return { id, username: 'Mock User', email: 'mock@example.com' }; // Should match User type
    },
    async allUsers(_parent, _args, context, _info) {
      return [{ id: '1', username: 'User One' }];
    },
  },
};

export default resolvers;
```

### Context Typing <a name="context-typing-graphql"></a>
The `context` object is shared across all resolvers in a single request. Typing it is crucial for accessing request-specific data like authentication info or data loaders.
```typescript
// Already shown in Apollo Server example
interface MyContext {
  token?: string;
  // dataLoaders: MyDataLoaders;
}
```

## 4. Client-Side TypeScript with GraphQL <a name="client-side-ts-graphql"></a>

### Generating Types from Schema and Operations <a name="generating-types-client"></a>
**GraphQL Code Generator (`@graphql-codegen/cli`)** is indispensable.
1.  Install dependencies:
    ```bash
    npm install --save-dev @graphql-codegen/cli @graphql-codegen/typescript @graphql-codegen/typescript-operations @graphql-codegen/typescript-react-apollo # (or other client plugins)
    ```
2.  Create `codegen.yml` configuration:
    ```yaml
    overwrite: true
    schema: "http://localhost:4000/graphql" # Or path to local schema.graphql
    documents: "src/**/*.graphql" # Path to your .graphql query/mutation files
    generates:
      src/generated/graphql.tsx: # Output file
        plugins:
          - "typescript"
          - "typescript-operations"
          - "typescript-react-apollo" # If using React Apollo
        config:
          withHooks: true # For React Apollo hooks
          # Other plugin-specific configs
    ```
3.  Run codegen: `npm run codegen` (add this script to `package.json`)

This generates types for your schema entities and for each query/mutation/subscription you define in `.graphql` files.

### Typed GraphQL Clients
*   **Apollo Client:**
    ```typescript
    // src/components/UserProfile.tsx
    import React from 'react';
    import { gql } from '@apollo/client'; // Or import from generated types if using near-operation-file preset
    import { useGetUserQuery, GetUserQuery, GetUserQueryVariables } from '../generated/graphql';

    // Define your query in a .graphql file or use gql tag
    // If using .graphql file, codegen will pick it up.
    // Example: src/graphql/queries/getUser.graphql
    // query GetUser($id: ID!) {
    //   getUser(id: $id) {
    //     id
    //     username
    //     email
    //   }
    // }

    interface UserProfileProps {
      userId: string;
    }

    const UserProfile: React.FC<UserProfileProps> = ({ userId }) => {
      const { loading, error, data } = useGetUserQuery({
        variables: { id: userId }, // Typed variables!
      });

      if (loading) return <p>Loading...</p>;
      if (error) return <p>Error: {error.message}</p>;

      // `data` is typed as GetUserQuery | undefined
      const user = data?.getUser;

      if (!user) return <p>User not found.</p>;

      return (
        <div>
          <h1>{user.username}</h1> {/* Autocompletion for user properties! */}
          <p>ID: {user.id}</p>
          {user.email && <p>Email: {user.email}</p>}
        </div>
      );
    };

    export default UserProfile;
    ```

## 5. End-to-End Type Safety <a name="end-to-end-type-safety-graphql"></a>
The GraphQL schema acts as a contract. TypeScript + GraphQL Code Generator allows you to enforce this contract:
1.  **Schema:** The single source of truth (SDL or code-first generated SDL).
2.  **Server:** Resolvers implement the schema, with types generated from the SDL ensuring arguments and return values match.
3.  **Client:** Queries and mutations are validated against the schema, and generated types ensure client-side code correctly handles API responses and variables.

Any breaking change in the schema will cause TypeScript errors on the server or client during development, preventing runtime issues.

## 6. Popular Tools and Libraries <a name="popular-tools-libraries-graphql-ts"></a>
*   **`graphql-js`**: The reference implementation of GraphQL.
*   **`@graphql-tools/schema`**: Utilities for creating and working with GraphQL schemas.
*   **`GraphQL Code Generator`**: Generates types and code from your schema and operations.
*   **Server Libraries**: Apollo Server, TypeGraphQL, NestJS (GraphQL), GraphQL Yoga.
*   **Client Libraries**: Apollo Client, urql, Relay.
*   **Schema Definition (Code-First)**: TypeGraphQL, Nexus Schema.

## 7. Best Practices <a name="best-practices-ts-graphql"></a>
*   **Single Source of Truth:** Treat your GraphQL schema (SDL) as the contract.
*   **Automate Type Generation:** Integrate `graphql-codegen` into your build/dev process.
*   **Strict TypeScript:** Enable `strict: true` in `tsconfig.json` for maximum type safety.
*   **Type Resolver Context:** Ensure your server's context object is well-typed.
*   **Client-Side Typing:** Use generated types for query/mutation variables and results.
*   **Fragment Colocation:** For larger applications, define GraphQL fragments alongside the components that use them. GraphQL Code Generator can help with this.
*   **Error Handling:** Define how errors are represented in your GraphQL schema (e.g., using union types or a dedicated error field) and handle them type-safely.

## 8. Key Takeaways <a name="key-takeaways-ts-graphql"></a>
*   TypeScript and GraphQL are a powerful combination for building type-safe APIs and applications.
*   The GraphQL schema is central to achieving end-to-end type safety.
*   GraphQL Code Generator is an essential tool for automating the generation of TypeScript types for both server and client.
*   Typed resolvers on the server and typed operations on the client significantly reduce runtime errors and improve developer productivity.
*   Whether you choose an SDL-first or code-first approach, TypeScript enhances the robustness and maintainability of your GraphQL projects.

---

⬅️ [Back to TypeScript for Full-Stack Development (Node.js, Deno)](../29-ts-fullstack-dev/README.md) | ➡️ [Next: Advanced TypeScript Patterns and Idioms](../31-ts-advanced-patterns/README.md) *(Placeholder)*
