# 41. TypeScript with Serverless Architectures

Serverless architectures allow you to build and run applications and services without thinking about servers. Cloud providers like AWS (Lambda), Azure (Functions), and Google Cloud (Cloud Functions) manage the infrastructure. TypeScript can bring significant benefits to serverless development by providing type safety for event payloads, context objects, environment variables, and function logic.

This tutorial explores how to effectively use TypeScript in serverless environments.

## Table of Contents
1.  [Introduction to Serverless Computing](#intro-serverless)
    *   Key concepts: Functions as a Service (FaaS), event-driven, auto-scaling, pay-per-use.
    *   Popular platforms: AWS Lambda, Azure Functions, Google Cloud Functions.
2.  [Why TypeScript for Serverless?](#why-ts-serverless)
    *   Type safety for handler signatures (event, context).
    *   Improved code quality and maintainability.
    *   Easier refactoring.
    *   Better tooling and autocompletion.
    *   Type-safe environment variables.
3.  [Setting Up a TypeScript Serverless Project](#setup-ts-serverless)
    *   Project structure recommendations.
    *   `tsconfig.json` for serverless functions (e.g., `target`, `module`).
    *   Managing dependencies (`package.json`).
4.  [Typing Serverless Function Handlers](#typing-handlers-ts)
    *   [**AWS Lambda with TypeScript**](#aws-lambda-ts)
        *   Using `@types/aws-lambda` for event and context types (e.g., `APIGatewayProxyEvent`, `Context`).
        *   Example: Typed API Gateway handler.
    *   [**Azure Functions with TypeScript**](#azure-functions-ts)
        *   Using `@azure/functions` types (e.g., `Context`, `HttpRequest`).
        *   Example: Typed HTTP trigger.
    *   [**Google Cloud Functions with TypeScript**](#gcp-functions-ts)
        *   Using `@google-cloud/functions-framework` types or defining custom types.
        *   Example: Typed HTTP function.
5.  [Managing Dependencies](#managing-dependencies-serverless-ts)
    *   Including only necessary runtime dependencies.
    *   Using `devDependencies` for TypeScript and bundlers.
    *   Layering dependencies (e.g., AWS Lambda Layers).
6.  [Local Development and Testing](#local-dev-testing-serverless-ts)
    *   [**Unit Testing**](#unit-testing-serverless-ts)
        *   Mocking event payloads and context objects.
        *   Testing function logic in isolation.
    *   [**Integration Testing**](#integration-testing-serverless-ts)
        *   Testing interactions with other services (databases, external APIs).
    *   [**Local Emulation Tools**](#local-emulation-serverless-ts)
        *   Serverless Framework Offline plugin.
        *   AWS SAM CLI.
        *   Azure Functions Core Tools.
        *   Google Cloud Functions Framework.
7.  [Building and Packaging for Deployment](#build-deploy-serverless-ts)
    *   Transpiling TypeScript to JavaScript.
    *   [**Bundling and Tree-Shaking**](#bundling-tree-shaking-serverless-ts)
        *   Why bundle? (reduce package size, improve cold start)
        *   Tools: Webpack, esbuild, Rollup.
        *   Configuring bundlers for serverless (e.g., excluding AWS SDK).
    *   Creating deployment packages (e.g., .zip files).
8.  [Deployment Strategies](#deployment-strategies-serverless-ts)
    *   Manual deployment via cloud provider consoles/CLIs.
    *   [**Infrastructure as Code (IaC)**](#iac-serverless-ts)
        *   Serverless Framework.
        *   AWS SAM (Serverless Application Model).
        *   AWS CDK (Cloud Development Kit).
        *   Terraform, Pulumi.
9.  [Error Handling in TypeScript Serverless Functions](#error-handling-serverless-ts)
    *   Custom error types (see Tutorial #39).
    *   Structured logging for errors.
    *   Returning appropriate HTTP status codes for API functions.
    *   Retry mechanisms and dead-letter queues (DLQs).
10. [Type-Safe Environment Variables](#env-vars-serverless-ts)
    *   Using libraries like Zod or custom parsing/validation functions.
    *   Defining a typed object for environment variables.
11. [Common Patterns with TypeScript in Serverless](#common-patterns-serverless-ts)
    *   API backends with API Gateway.
    *   Event processing (e.g., S3 events, SQS messages).
    *   Database interactions (e.g., DynamoDB, RDS Proxy).
    *   Scheduled tasks (cron jobs).
12. [Cold Starts and Performance Considerations](#cold-starts-performance-serverless-ts)
    *   Impact of package size, runtime choice, and memory allocation.
    *   Strategies to mitigate cold starts (provisioned concurrency, bundler optimizations).
13. [Key Takeaways](#key-takeaways-ts-serverless)

## 1. Introduction to Serverless Computing <a name="intro-serverless"></a>
Serverless computing abstracts server management away from developers. You write functions, and the cloud provider handles provisioning, scaling, patching, and maintaining the underlying infrastructure. Key characteristics include event-driven execution, automatic scaling based on demand, and a pay-per-use pricing model.

## 2. Why TypeScript for Serverless? <a name="why-ts-serverless"></a>
*   **Type Safety:** Catch errors at compile time for event payloads, context objects, and return types specific to the serverless provider and trigger.
*   **Maintainability:** Clearer code structure and easier refactoring, especially as the number of functions grows.
*   **Developer Experience:** Autocompletion and better tooling support in IDEs.
*   **Configuration Safety:** Type-check environment variables and service configurations.

## 3. Setting Up a TypeScript Serverless Project <a name="setup-ts-serverless"></a>
A typical structure:
```
my-serverless-project/
├── src/
│   ├── functions/
│   │   ├── hello.ts
│   │   └── anotherFunction.ts
│   ├── libs/         // Shared utility code
│   └── types/        // Custom type definitions
├── package.json
├── tsconfig.json
├── serverless.yml    // Or samconfig.toml, etc. for IaC tools
└── dist/             // Output directory for compiled JS
```
**`tsconfig.json` example:**
```json
{
  "compilerOptions": {
    "target": "ES2020", // Or newer, depending on Node.js version supported by provider
    "module": "commonjs", // Or "ESNext" if using ES modules and bundler supports it
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "sourceMap": true, // Useful for debugging
    "resolveJsonModule": true
  },
  "include": ["src/**/*.ts"],
  "exclude": ["node_modules", "**/*.test.ts"]
}
```

## 4. Typing Serverless Function Handlers <a name="typing-handlers-ts"></a>

### AWS Lambda with TypeScript <a name="aws-lambda-ts"></a>
Install `@types/aws-lambda`:
```bash
npm install -D @types/aws-lambda
```
```typescript
// src/functions/hello.ts
import { APIGatewayProxyEvent, APIGatewayProxyResult, Context } from 'aws-lambda';

export const handler = async (event: APIGatewayProxyEvent, context: Context): Promise<APIGatewayProxyResult> => {
  console.log('Event:', JSON.stringify(event, null, 2));
  console.log('Context:', JSON.stringify(context, null, 2));

  try {
    const name = event.queryStringParameters?.name || 'World';
    return {
      statusCode: 200,
      body: JSON.stringify({
        message: `Hello, ${name}! From Lambda with TypeScript.`,
      }),
    };
  } catch (error) {
    console.error(error);
    return {
      statusCode: 500,
      body: JSON.stringify({ message: 'Internal Server Error' }),
    };
  }
};
```

### Azure Functions with TypeScript <a name="azure-functions-ts"></a>
Types are often part of the `@azure/functions` package.
```typescript
// src/functions/HttpTrigger.ts
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
};

export default httpTrigger;
```

### Google Cloud Functions with TypeScript <a name="gcp-functions-ts"></a>
Types can be found in `@google-cloud/functions-framework` or defined manually based on event source documentation.
```typescript
import { HttpFunction } from '@google-cloud/functions-framework';

interface RequestBody { // Custom type for request body
  name?: string;
}

export const helloHttp: HttpFunction = (req, res) => {
  const body = req.body as RequestBody; // Type assertion, consider validation
  const name = body.name || req.query.name || 'World';
  res.status(200).send(`Hello, ${name}! From GCF with TypeScript.`);
};
```

## 5. Managing Dependencies <a name="managing-dependencies-serverless-ts"></a>
Minimize package size by only including runtime dependencies. Use AWS Lambda Layers or similar features for shared, large dependencies.

## 6. Local Development and Testing <a name="local-dev-testing-serverless-ts"></a>
*   **Unit Testing:** Use Jest or Mocha. Mock event payloads and context. Example:
    ```typescript
    // src/functions/hello.test.ts
    import { handler } from './hello';
    import { APIGatewayProxyEvent, Context } from 'aws-lambda';

    describe('Hello Handler', () => {
      it('should return a 200 with a greeting', async () => {
        const event = { /* mock APIGatewayProxyEvent */ } as APIGatewayProxyEvent;
        const context = { /* mock Context */ } as Context;
        const result = await handler(event, context);
        expect(result.statusCode).toBe(200);
        expect(JSON.parse(result.body).message).toContain('Hello');
      });
    });
    ```
*   **Local Emulation:** Tools like Serverless Framework Offline, AWS SAM CLI (`sam local invoke`), Azure Functions Core Tools (`func start`), and GCF Framework allow running functions locally.

## 7. Building and Packaging for Deployment <a name="build-deploy-serverless-ts"></a>
Transpile TS to JS. Bundle to reduce size and improve cold starts.

### Bundling and Tree-Shaking <a name="bundling-tree-shaking-serverless-ts"></a>
*   **Webpack:** Highly configurable, mature.
*   **esbuild:** Extremely fast, good for serverless due to speed.
*   **Rollup:** Good for libraries, can also bundle functions.

Example `webpack.config.js` (simplified for serverless):
```javascript
// webpack.config.js
const path = require('path');
const slsw = require('serverless-webpack');
const nodeExternals = require('webpack-node-externals');

module.exports = {
  context: __dirname,
  mode: slsw.lib.webpack.isLocal ? 'development' : 'production',
  entry: slsw.lib.entries,
  devtool: slsw.lib.webpack.isLocal ? 'cheap-module-source-map' : 'source-map',
  resolve: {
    extensions: ['.mjs', '.json', '.ts'],
    symlinks: false,
    cacheWithContext: false,
  },
  output: {
    libraryTarget: 'commonjs',
    path: path.join(__dirname, '.webpack'),
    filename: '[name].js',
  },
  target: 'node',
  externals: [nodeExternals()], // Exclude node_modules
  module: {
    rules: [
      {
        test: /\.ts$/,
        loader: 'ts-loader',
        exclude: [[path.resolve(__dirname, 'node_modules'), path.resolve(__dirname, '.serverless'), path.resolve(__dirname, '.webpack')]],
        options: { transpileOnly: true, experimentalWatchApi: true },
      },
    ],
  },
};
```
Ensure your bundler excludes the AWS SDK if deploying to AWS Lambda (it's already available in the environment).

## 8. Deployment Strategies <a name="deployment-strategies-serverless-ts"></a>
Use IaC tools for managing and deploying serverless applications:
*   **Serverless Framework:** Popular multi-cloud framework.
*   **AWS SAM:** AWS-specific, uses CloudFormation.
*   **AWS CDK:** Define infrastructure with familiar programming languages (including TypeScript).
*   **Terraform/Pulumi:** General-purpose IaC tools with serverless support.

## 9. Error Handling in TypeScript Serverless Functions <a name="error-handling-serverless-ts"></a>
*   Implement robust `try...catch` blocks.
*   Use custom error classes for specific failure modes.
*   Log errors with structured data (correlation IDs, request details).
*   For API functions, return meaningful HTTP status codes and error messages.
*   Configure DLQs for event-driven functions to handle processing failures.

## 10. Type-Safe Environment Variables <a name="env-vars-serverless-ts"></a>
Validate and type environment variables at startup.
```typescript
import { z } from 'zod';

const EnvSchema = z.object({
  DATABASE_URL: z.string().url(),
  API_KEY: z.string().min(1),
  NODE_ENV: z.enum(['development', 'production', 'test']).default('development'),
});

// Parse at the beginning of your function or in a shared init module
// const env = EnvSchema.parse(process.env);
// Now use env.DATABASE_URL, etc., with type safety.
```

## 11. Common Patterns with TypeScript in Serverless <a name="common-patterns-serverless-ts"></a>
*   REST APIs using API Gateway + Lambda.
*   Processing S3 bucket events (e.g., image resizing).
*   Consuming messages from SQS/EventBridge queues.
*   GraphQL APIs with Apollo Server Lambda or similar.
*   Scheduled tasks (e.g., cron jobs via EventBridge Scheduled Events).

## 12. Cold Starts and Performance Considerations <a name="cold-starts-performance-serverless-ts"></a>
*   **Cold Start:** The latency incurred when a function is invoked for the first time or after a period of inactivity.
*   **Mitigation:**
    *   Keep package size small (bundling, tree-shaking).
    *   Choose an appropriate runtime and memory allocation.
    *   Initialize connections and heavy objects outside the handler (globally) if possible, but be mindful of state across invocations.
    *   Use Provisioned Concurrency (AWS) or similar features to keep instances warm.

## 13. Key Takeaways <a name="key-takeaways-ts-serverless"></a>
*   TypeScript significantly improves the development experience and reliability of serverless functions.
*   Use provider-specific type definitions (e.g., `@types/aws-lambda`, `@azure/functions`) for handlers.
*   Bundling (e.g., with esbuild or Webpack) is crucial for optimizing package size and cold starts.
*   IaC tools (Serverless Framework, SAM, CDK) simplify deployment and management.
*   Implement robust error handling, structured logging, and type-safe environment variable management.

By leveraging TypeScript's strengths, you can build more robust, maintainable, and scalable serverless applications.

---

⬅️ [Back to TypeScript and Functional Programming Concepts](../40-ts-functional-programming/README.md) | ➡️ [Next: TypeScript in Real-time Applications (WebSockets, etc.)](../42-ts-real-time-apps/README.md) *(Placeholder)*
