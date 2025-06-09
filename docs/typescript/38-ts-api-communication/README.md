# 38. Type-Safe Data Fetching and API Communication in TypeScript

Interacting with APIs is a fundamental part of most modern applications. TypeScript can significantly improve the reliability of API communication by ensuring that the data you send and receive conforms to expected structures. This tutorial explores various techniques and libraries for type-safe data fetching and API communication in TypeScript.

## Table of Contents
1.  [Introduction: The Challenge of API Data](#intro-api-data-challenge)
    *   Dynamic nature of API responses
    *   Risk of runtime errors due to unexpected data structures
2.  [Basic `fetch` with TypeScript](#basic-fetch-ts)
    *   Defining response types
    *   Using generics with `fetch` wrapper functions
    *   Handling HTTP errors
3.  [Runtime Type Validation for API Responses](#runtime-type-validation-api)
    *   Why compile-time types aren't enough for external data
    *   [**Type Guards and Assertion Functions**](#type-guards-assertions-api)
        *   Writing custom type guards for API response shapes
        *   Using assertion functions to validate data
    *   [**Schema Validation Libraries**](#schema-validation-libraries-api)
        *   **Zod:** Defining schemas and parsing/validating data
        *   **io-ts:** Functional approach to runtime type decoding and validation
        *   Others: Joi, Yup (often used with Formik)
4.  [Popular Data Fetching Libraries with TypeScript Support](#data-fetching-libraries-ts)
    *   [**Axios**](#axios-ts)
        *   Typed request and response configurations
        *   Interceptors with types
    *   [**React Query / TanStack Query**](#react-query-ts)
        *   Strongly typed query keys, data, and errors
        *   Inferring types from query functions
    *   [**SWR (stale-while-revalidate)**](#swr-ts)
        *   Typed data and error handling with hooks
    *   [**Apollo Client (for GraphQL)**](#apollo-client-ts-graphql)
        *   Generating types from GraphQL schemas (`graphql-codegen`)
        *   Typed query/mutation hooks and cache operations
    *   [**tRPC (Typed Remote Procedure Call)**](#trpc-ts)
        *   End-to-end typesafe APIs without code generation or schemas
5.  [Generating Types from API Schemas](#generating-types-api-schemas)
    *   [**OpenAPI (Swagger)**](#openapi-swagger-ts)
        *   Tools like `openapi-typescript` or `oazapfts` to generate TS types/clients
    *   [**GraphQL Schemas**](#graphql-schemas-ts)
        *   Using `graphql-code-generator` for client and server types
6.  [Creating Type-Safe API Client SDKs/Wrappers](#type-safe-api-sdks)
    *   Designing a reusable API client class or set of functions
    *   Using generics and mapped types for flexible SDKs
7.  [Error Handling Strategies with Types](#error-handling-api-ts)
    *   Defining specific error types for API failures (e.g., network error, validation error, server error)
    *   Using discriminated unions for error states
8.  [Handling Different Content Types](#content-types-api-ts)
    *   JSON (most common)
    *   FormData, URLSearchParams
    *   ArrayBuffer, Blob for binary data
9.  [Mocking API Calls for Testing with Types](#mocking-api-ts-testing)
    *   Using libraries like Mock Service Worker (MSW) or `jest.mock`
    *   Ensuring mocks conform to defined TypeScript types
10. [Key Takeaways](#key-takeaways-ts-api)

## 1. Introduction: The Challenge of API Data <a name="intro-api-data-challenge"></a>
API responses are external data sources. While you can define TypeScript interfaces for their expected shape, there's no guarantee at compile time that the actual data received at runtime will match. This can lead to runtime errors if the API changes or returns unexpected data.

## 2. Basic `fetch` with TypeScript <a name="basic-fetch-ts"></a>
```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

async function fetchUser(userId: number): Promise<User> {
  const response = await fetch(`/api/users/${userId}`);
  if (!response.ok) {
    // More specific error handling is better
    throw new Error(`HTTP error! status: ${response.status}`);
  }
  const data = await response.json();
  // At this point, 'data' is 'any'. We need to validate or assert its type.
  return data as User; // Type assertion - risky without validation!
}

// Safer generic fetch wrapper
async function fetchData<T>(url: string, options?: RequestInit): Promise<T> {
  const response = await fetch(url, options);
  if (!response.ok) {
    const errorData = await response.text(); // Or response.json() if error details are JSON
    throw new Error(`HTTP error! status: ${response.status}, message: ${errorData}`);
  }
  const data = await response.json();
  // Still requires validation for true type safety
  return data as T; 
}

// Usage
// const user = await fetchData<User>('/api/users/1');
```
**Note:** Using `as User` or `as T` is a type assertion. It tells TypeScript to trust you that the data matches the type, but it doesn't perform any runtime checks. This is where runtime validation becomes crucial.

## 3. Runtime Type Validation for API Responses <a name="runtime-type-validation-api"></a>
To bridge the gap between compile-time types and runtime data, we need runtime validation.

### Type Guards and Assertion Functions <a name="type-guards-assertions-api"></a>
```typescript
// Type Guard
function isUser(data: any): data is User {
  return (
    typeof data === 'object' &&
    data !== null &&
    typeof data.id === 'number' &&
    typeof data.name === 'string' &&
    typeof data.email === 'string'
  );
}

async function fetchUserWithGuard(userId: number): Promise<User> {
  const response = await fetch(`/api/users/${userId}`);
  if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
  const data: unknown = await response.json(); // Fetch as unknown first

  if (isUser(data)) {
    return data; // data is now User within this block
  } else {
    throw new Error('Invalid user data received from API');
  }
}

// Assertion Function
function assertIsUser(data: unknown): asserts data is User {
  if (!isUser(data)) {
    throw new Error('Invalid user data received from API (assertion failed)');
  }
}

async function fetchUserWithAssertion(userId: number): Promise<User> {
  const response = await fetch(`/api/users/${userId}`);
  if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
  const data: unknown = await response.json();
  assertIsUser(data);
  return data; // data is now User
}
```

### Schema Validation Libraries <a name="schema-validation-libraries-api"></a>
These libraries provide a more declarative and powerful way to define schemas and validate data.

**Zod:**
```typescript
import { z } from 'zod';

const UserSchema = z.object({
  id: z.number(),
  name: z.string(),
  email: z.string().email(),
  isActive: z.boolean().optional(),
});

// Infer TypeScript type from schema
type UserZ = z.infer<typeof UserSchema>;

async function fetchUserWithZod(userId: number): Promise<UserZ> {
  const response = await fetch(`/api/users/${userId}`);
  if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
  const data: unknown = await response.json();
  
  try {
    return UserSchema.parse(data); // .parse throws on error
    // return UserSchema.safeParse(data); // .safeParse returns a result object
  } catch (error) {
    console.error('Zod validation error:', error);
    throw new Error('Invalid user data (Zod)');
  }
}
```

**io-ts:**
```typescript
import * as t from 'io-ts';
import { isRight } from 'fp-ts/lib/Either';

const UserCodec = t.type({
  id: t.number,
  name: t.string,
  email: t.string, // More complex regex for email can be a custom type
  isActive: t.union([t.boolean, t.undefined]),
});

// Infer TypeScript type
type UserIO = t.TypeOf<typeof UserCodec>;

async function fetchUserWithIoTs(userId: number): Promise<UserIO> {
  const response = await fetch(`/api/users/${userId}`);
  if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
  const data: unknown = await response.json();

  const validationResult = UserCodec.decode(data);
  if (isRight(validationResult)) {
    return validationResult.right;
  } else {
    // import { PathReporter } from 'io-ts/lib/PathReporter';
    // console.error('io-ts validation error:', PathReporter.report(validationResult));
    throw new Error('Invalid user data (io-ts)');
  }
}
```

## 4. Popular Data Fetching Libraries with TypeScript Support <a name="data-fetching-libraries-ts"></a>

### Axios <a name="axios-ts"></a>
```typescript
import axios, { AxiosResponse } from 'axios';

// Using the User interface from before

async function fetchUserWithAxios(userId: number): Promise<User> {
  try {
    const response: AxiosResponse<User> = await axios.get(`/api/users/${userId}`);
    // Axios automatically converts to JSON and assumes the type if provided
    // For true safety, validate response.data here with Zod/io-ts
    return response.data;
  } catch (error) {
    if (axios.isAxiosError(error)) {
      throw new Error(`Axios error: ${error.message}, Code: ${error.code}`);
    } else {
      throw new Error(`Unexpected error: ${error}`);
    }
  }
}
```

### React Query / TanStack Query <a name="react-query-ts"></a>
Provides excellent TypeScript support out-of-the-box.
```typescript
import { useQuery, QueryFunctionContext } from '@tanstack/react-query';
// Assuming User interface and UserSchema (Zod) from before

const fetchUserQueryFn = async (ctx: QueryFunctionContext<[string, number]>): Promise<UserZ> => {
  const [_key, userId] = ctx.queryKey;
  const response = await fetch(`/api/users/${userId}`);
  if (!response.ok) throw new Error('Network response was not ok');
  const data: unknown = await response.json();
  return UserSchema.parse(data); // Validate here!
};

function UserProfile({ userId }: { userId: number }) {
  const { data, error, isLoading } = useQuery<UserZ, Error, UserZ, [string, number]>(
    ['user', userId], 
    fetchUserQueryFn
  );

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;
  if (!data) return null;

  return <div>Name: {data.name}, Email: {data.email}</div>;
}
```

### SWR <a name="swr-ts"></a>
Similar to React Query, with good TypeScript integration.
```typescript
import useSWR from 'swr';
// Assuming User interface and UserSchema (Zod) from before

const fetcher = async (url: string): Promise<UserZ> => {
  const res = await fetch(url);
  if (!res.ok) throw new Error('An error occurred while fetching the data.');
  const data: unknown = await res.json();
  return UserSchema.parse(data); // Validate!
};

function UserProfileSWR({ userId }: { userId: number }) {
  const { data, error, isLoading } = useSWR<UserZ, Error>(`/api/users/${userId}`, fetcher);

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;
  if (!data) return null;

  return <div>Name: {data.name}</div>;
}
```

### Apollo Client (for GraphQL) <a name="apollo-client-ts-graphql"></a>
Relies heavily on code generation from your GraphQL schema for type safety.
See Tutorial #30 (TypeScript with GraphQL) for more details.

### tRPC <a name="trpc-ts"></a>
Allows you to build end-to-end typesafe APIs without schemas or code generation. The router definition on the server becomes the source of truth for types on the client.

## 5. Generating Types from API Schemas <a name="generating-types-api-schemas"></a>
For REST APIs, OpenAPI (formerly Swagger) is a common standard. For GraphQL, the schema itself is the source.

### OpenAPI (Swagger) <a name="openapi-swagger-ts"></a>
*   `openapi-typescript`: Generates TypeScript types directly from an OpenAPI schema (v2 or v3).
    ```bash
    npx openapi-typescript ./path/to/your/schema.yaml -o ./src/generated/api-types.ts
    ```
*   `oazapfts` (and others): Can generate full client SDKs with methods for each endpoint, in addition to types.

### GraphQL Schemas <a name="graphql-schemas-ts"></a>
*   `graphql-code-generator`: Highly configurable tool to generate types for resolvers, queries, mutations, and hooks for various clients (Apollo, urql, React Query).
    ```bash
    # Example command (config usually in .graphqlrc.yml or codegen.yml)
    npx graphql-codegen --config codegen.yml 
    ```

## 6. Creating Type-Safe API Client SDKs/Wrappers <a name="type-safe-api-sdks"></a>
Encapsulate API logic into a reusable client.
```typescript
// Assuming generated types `ApiPaths` and `ApiMethods` from an OpenAPI schema
// And a validation function `validateAndTransform<T>(data: unknown, schema: ZodSchema<T>): T`

class ApiClient {
  private baseUrl: string;

  constructor(baseUrl: string) {
    this.baseUrl = baseUrl;
  }

  async get<P extends keyof ApiPaths['get']>(
    path: P,
    // This is highly conceptual; actual implementation depends on generated types
    // params: ApiPaths['get'][P]['parameters'], 
    // schema: ZodSchema<ApiPaths['get'][P]['response']>
  ): Promise<any /* ApiPaths['get'][P]['response'] */> {
    const response = await fetch(`${this.baseUrl}${path.toString()}`); // Add params serialization
    if (!response.ok) throw new Error('Network error');
    const data: unknown = await response.json();
    // return validateAndTransform(data, schema);
    return data; // Placeholder
  }
  // Add post, put, delete methods similarly
}
```

## 7. Error Handling Strategies with Types <a name="error-handling-api-ts"></a>
```typescript
type NetworkError = { type: 'NetworkError'; message: string; statusCode?: number };
type ValidationError = { type: 'ValidationError'; fieldErrors: Record<string, string[]> };
type UnknownApiError = { type: 'UnknownApiError'; originalError: unknown };

export type ApiError = NetworkError | ValidationError | UnknownApiError;

// In your fetching logic:
// if (validationFailed) return { type: 'ValidationError', ... };
// if (networkIssue) return { type: 'NetworkError', ... };

// Consuming code can use a switch on error.type
```

## 8. Handling Different Content Types <a name="content-types-api-ts"></a>
*   **JSON:** `response.json()` and `JSON.stringify()` for request body.
*   **FormData:** For multipart forms (file uploads). `new FormData()` and pass it as `body`.
*   **URLSearchParams:** For `application/x-www-form-urlencoded`. `new URLSearchParams()`.
*   **Binary Data:** `response.arrayBuffer()` or `response.blob()`. Type the result as `ArrayBuffer` or `Blob`.

## 9. Mocking API Calls for Testing with Types <a name="mocking-api-ts-testing"></a>
*   **Mock Service Worker (MSW):** Intercepts actual network requests. Define handlers that return mock data conforming to your TypeScript types.
*   **Jest `jest.mock('./apiService')`:** Mock the module that makes API calls. Ensure mocked implementations return data that matches your defined types.

```typescript
// Example with MSW (conceptual)
// src/mocks/handlers.ts
import { rest } from 'msw';
import { User } from '../types'; // Your User type

export const handlers = [
  rest.get('/api/users/:userId', (req, res, ctx) => {
    const { userId } = req.params;
    const mockUser: User = {
      id: Number(userId),
      name: 'Mocked User',
      email: 'mocked@example.com',
    };
    return res(ctx.status(200), ctx.json(mockUser));
  }),
];
```

## 10. Key Takeaways <a name="key-takeaways-ts-api"></a>
*   TypeScript types alone don't guarantee API response correctness; **runtime validation is essential**.
*   Libraries like Zod or io-ts provide robust ways to define schemas and validate external data.
*   Data fetching libraries (React Query, SWR, Axios) offer excellent TypeScript support, but you're still responsible for validating the raw response data within their fetcher functions.
*   Generating types from API schemas (OpenAPI, GraphQL) significantly reduces manual type definition and keeps client types synchronized with the API.
*   Typed error handling improves the robustness of your application's interaction with APIs.
*   tRPC offers a unique approach to end-to-end type safety without explicit schema generation for full-stack TypeScript projects.

By combining TypeScript's static typing with runtime validation and appropriate tooling, you can build much more reliable and maintainable applications that communicate with APIs.

---

⬅️ [Back to TypeScript with Web Components](../37-ts-web-components/README.md) | ➡️ [Next: Advanced Error Handling and Debugging in TypeScript](../39-ts-advanced-error-handling/README.md) *(Placeholder)*
