# 20. TypeScript and Testing (Jest, Mocha)

Testing is a critical part of software development, ensuring code quality, reliability, and maintainability. When working with TypeScript, you can leverage popular JavaScript testing frameworks like Jest and Mocha, along with some TypeScript-specific configurations, to write effective tests.

## Table of Contents
1.  [Why Test TypeScript Code?](#why-test-ts)
2.  [Choosing a Testing Framework](#choosing-framework)
    *   Jest
    *   Mocha (with Chai and ts-node)
3.  [Testing with Jest](#testing-with-jest)
    *   Installation and Setup
    *   Configuration (`jest.config.js`)
    *   Using `ts-jest`
    *   Writing Your First TypeScript Test with Jest
    *   Mocking Modules and Functions
    *   Snapshot Testing
4.  [Testing with Mocha](#testing-with-mocha)
    *   Installation and Setup (Mocha, Chai, ts-node, @types)
    *   Configuration (e.g., `.mocharc.json` or command-line options)
    *   Writing Your First TypeScript Test with Mocha and Chai
    *   Using `ts-node` for on-the-fly transpilation
    *   Mocking (e.g., with Sinon.JS)
5.  [Common Testing Concepts in TypeScript](#common-testing-concepts)
    *   Unit Tests
    *   Integration Tests
    *   End-to-End (E2E) Tests (Brief Mention)
    *   Code Coverage
    *   Typing Your Tests
6.  [Best Practices for Testing TypeScript](#best-practices-testing-ts)
    *   Test the Public API
    *   Write Clear and Maintainable Tests
    *   Aim for High Test Coverage (but focus on quality)
    *   Use Type Safety in Tests
    *   Integrate Testing into Your CI/CD Pipeline
7.  [Key Takeaways](#key-takeaways-ts-testing)

## 1. Why Test TypeScript Code? <a name="why-test-ts"></a>
While TypeScript's static type system catches many errors at compile time, it doesn't eliminate the need for runtime testing. Tests are crucial for:
*   **Verifying Logic:** Ensuring functions, components, and modules behave as expected under various conditions.
*   **Preventing Regressions:** Catching bugs introduced by new changes or refactoring.
*   **Facilitating Refactoring:** Providing a safety net when making changes to the codebase.
*   **Documentation:** Tests can serve as living documentation, illustrating how code is intended to be used.
*   **Design Feedback:** Writing testable code often leads to better-designed, more modular code.

## 2. Choosing a Testing Framework <a name="choosing-framework"></a>

### Jest
*   **All-in-one:** Jest is a popular testing framework developed by Facebook. It comes with a test runner, assertion library, mocking support, and code coverage built-in.
*   **Ease of Setup:** Generally easy to set up, especially for React projects (Create React App includes it by default).
*   **Snapshot Testing:** Powerful feature for testing UI components or complex data structures.
*   **Parallel Test Execution:** Runs tests in parallel by default, which can speed up test runs.

### Mocha (with Chai and ts-node)
*   **Flexible and Extensible:** Mocha is a mature and highly flexible testing framework. It's a test runner, and you typically pair it with assertion libraries (like Chai) and mocking libraries (like Sinon.JS).
*   **Choice of Libraries:** Allows you to choose your preferred libraries for assertions, mocking, etc.
*   **Requires More Setup:** Typically involves configuring more pieces compared to Jest.
*   **`ts-node`:** Often used with Mocha to execute TypeScript tests directly without a separate pre-compilation step.

Both are excellent choices. Jest is often favored for its integrated experience, while Mocha offers more flexibility if you prefer to pick and choose your testing toolkit.

## 3. Testing with Jest <a name="testing-with-jest"></a>

### Installation and Setup
```bash
npm install --save-dev jest typescript ts-jest @types/jest
# or
yarn add --dev jest typescript ts-jest @types/jest
```
*   `jest`: The Jest testing framework.
*   `typescript`: TypeScript itself.
*   `ts-jest`: A TypeScript preprocessor for Jest, allowing it to transpile TypeScript files on the fly.
*   `@types/jest`: Type definitions for Jest's global variables (e.g., `describe`, `it`, `expect`).

### Configuration (`jest.config.js` or `jest` section in `package.json`)
Create a `jest.config.js` file in your project root:
```javascript
// jest.config.js
module.exports = {
  preset: 'ts-jest', // Use ts-jest preset
  testEnvironment: 'node', // Or 'jsdom' for browser-like environment (e.g., for React)
  roots: ['<rootDir>/src'], // Look for tests in the src directory
  testMatch: [
    '**/__tests__/**/*.+(ts|tsx|js)',
    '**/?(*.)+(spec|test).+(ts|tsx|js)',
  ], // Pattern for test files
  transform: {
    '^.+\.(ts|tsx)$': 'ts-jest',
  }, // Transform .ts and .tsx files using ts-jest
  moduleFileExtensions: ['ts', 'tsx', 'js', 'jsx', 'json', 'node'],
  // collectCoverage: true, // Optional: enable code coverage
  // coverageDirectory: "coverage", // Optional: directory for coverage reports
};
```

Add a test script to your `package.json`:
```json
{
  "scripts": {
    "test": "jest"
  }
}
```

### Using `ts-jest`
`ts-jest` will use your `tsconfig.json` by default. You can customize its behavior in `jest.config.js` if needed (e.g., specifying a different `tsconfig.json` for tests).

### Writing Your First TypeScript Test with Jest
Let's say you have a simple utility function:
```typescript
// src/utils/math.ts
export function add(a: number, b: number): number {
  return a + b;
}
```

Create a test file:
```typescript
// src/utils/__tests__/math.test.ts
import { add } from '../math';

describe('Math utils', () => {
  describe('add function', () => {
    it('should return the sum of two numbers', () => {
      expect(add(2, 3)).toBe(5);
    });

    it('should work with negative numbers', () => {
      expect(add(-1, -5)).toBe(-6);
    });

    it('should work with zero', () => {
      expect(add(10, 0)).toBe(10);
    });
  });
});
```
Run tests: `npm test` or `yarn test`.

### Mocking Modules and Functions
Jest provides powerful mocking capabilities.

**Mocking a module:**
```typescript
// src/services/api.ts
export const fetchData = async (endpoint: string) => {
  // Imagine this makes a real API call
  return { data: `Data from ${endpoint}` };
};

// src/services/__tests__/api.test.ts
import { fetchData } from '../api';

// Mock the entire module or specific functions
jest.mock('../api', () => ({
  ...jest.requireActual('../api'), // Import and retain original implementations if needed
  fetchData: jest.fn(), // Mock fetchData
}));

describe('API Service', () => {
  it('fetchData should call the mocked implementation', async () => {
    // Cast to jest.Mock to access mock-specific properties/methods
    (fetchData as jest.Mock).mockResolvedValue({ data: 'mocked data' });

    const result = await fetchData('test-endpoint');
    expect(fetchData).toHaveBeenCalledWith('test-endpoint');
    expect(result.data).toBe('mocked data');
  });
});
```

### Snapshot Testing
Useful for UI components or large objects.
```typescript
// src/components/Button.tsx (Example React component)
// ... component definition ...

// src/components/__tests__/Button.test.tsx
// import React from 'react';
// import renderer from 'react-test-renderer';
// import Button from '../Button';

// describe('Button Component', () => {
//   it('renders correctly', () => {
//     const tree = renderer.create(<Button label="Click Me" />).toJSON();
//     expect(tree).toMatchSnapshot();
//   });
// });
```
On the first run, Jest creates a snapshot file. Subsequent runs compare the output to the saved snapshot.

## 4. Testing with Mocha <a name="testing-with-mocha"></a>

### Installation and Setup (Mocha, Chai, ts-node, @types)
```bash
npm install --save-dev mocha chai ts-node @types/mocha @types/chai sinon @types/sinon
# or
yarn add --dev mocha chai ts-node @types/mocha @types/chai sinon @types/sinon
```
*   `mocha`: The Mocha test runner.
*   `chai`: An assertion library (common choice with Mocha).
*   `ts-node`: Allows running TypeScript files directly in Node.js (transpiles on the fly).
*   `@types/mocha`, `@types/chai`: Type definitions.
*   `sinon`: A popular library for spies, stubs, and mocks (optional, if needed).
*   `@types/sinon`: Type definitions for Sinon.

### Configuration (e.g., `.mocharc.json` or command-line options)
Create a `.mocharc.json` (or `.js`, `.yaml`) in your project root:
```json
// .mocharc.json
{
  "extension": ["ts", "tsx"],
  "spec": "src/**/*.test.ts", // Pattern for test files
  "require": "ts-node/register" // Use ts-node to run .ts files
}
```

Add a test script to `package.json`:
```json
{
  "scripts": {
    "test": "mocha"
  }
}
```

### Writing Your First TypeScript Test with Mocha and Chai
Using the same `math.ts` example:
```typescript
// src/utils/math.ts
export function add(a: number, b: number): number {
  return a + b;
}
```

Test file:
```typescript
// src/utils/__tests__/math.test.ts
import { expect } from 'chai'; // Using Chai for assertions
import { add } from '../math';

describe('Math utils (Mocha/Chai)', () => {
  describe('add function', () => {
    it('should return the sum of two numbers', () => {
      expect(add(2, 3)).to.equal(5);
    });

    it('should work with negative numbers', () => {
      expect(add(-1, -5)).to.equal(-6);
    });

    it('should work with zero', () => {
      expect(add(10, 0)).to.equal(10);
    });
  });
});
```
Run tests: `npm test` or `yarn test`.

### Using `ts-node` for on-the-fly transpilation
As configured in `.mocharc.json` (`"require": "ts-node/register"`), Mocha will use `ts-node` to execute your TypeScript test files directly. `ts-node` respects your `tsconfig.json`.

### Mocking (e.g., with Sinon.JS)
Mocha itself doesn't provide mocking. You'd use a library like Sinon.JS.
```typescript
// src/services/api.ts (same as before)
// ...

// src/services/__tests__/api.test.ts
import { expect } from 'chai';
import sinon from 'sinon';
import * as ApiService from '../api'; // Import as namespace for stubbing

describe('API Service (Mocha/Sinon)', () => {
  let fetchDataStub: sinon.SinonStub;

  beforeEach(() => {
    // Stub the fetchData function from the imported module
    fetchDataStub = sinon.stub(ApiService, 'fetchData');
  });

  afterEach(() => {
    // Restore the original function after each test
    fetchDataStub.restore();
  });

  it('fetchData should call the stubbed implementation', async () => {
    fetchDataStub.resolves({ data: 'mocked data from sinon' });

    const result = await ApiService.fetchData('test-endpoint');

    expect(fetchDataStub.calledOnceWith('test-endpoint')).to.be.true;
    expect(result.data).to.equal('mocked data from sinon');
  });
});
```

## 5. Common Testing Concepts in TypeScript <a name="common-testing-concepts"></a>

*   **Unit Tests:** Test individual functions, classes, or modules in isolation.
*   **Integration Tests:** Test how multiple parts of your application work together.
*   **End-to-End (E2E) Tests:** Test the entire application flow from the user's perspective (e.g., using tools like Cypress, Playwright). These typically run against a fully built and running application.
*   **Code Coverage:** Measures how much of your code is executed by your tests. Tools like Jest and Istanbul (often used with Mocha) can generate coverage reports.
*   **Typing Your Tests:** Leverage TypeScript's type system within your test files. Use types for test data, mock implementations, and function signatures to catch errors in your tests themselves.

## 6. Best Practices for Testing TypeScript <a name="best-practices-testing-ts"></a>
*   **Test the Public API:** Focus tests on the exported, public interface of your modules, not internal implementation details. This makes tests less brittle to refactoring.
*   **Write Clear and Maintainable Tests:** Tests should be easy to read and understand. Use descriptive names for `describe` and `it` blocks.
*   **Aim for High Test Coverage (but focus on quality):** High coverage is good, but ensure your tests are meaningful and cover important edge cases, not just lines of code.
*   **Use Type Safety in Tests:** Annotate test variables, mock function signatures, and expected return types. This can catch issues in test setup or assertions.
*   **Isolate Tests:** Each test should be independent and not rely on the state or outcome of other tests.
*   **Integrate Testing into Your CI/CD Pipeline:** Automate test execution on every commit or pull request to catch regressions early.

## 7. Key Takeaways <a name="key-takeaways-ts-testing"></a>
*   Testing is essential for TypeScript projects to ensure runtime correctness and maintainability.
*   **Jest** offers an all-in-one testing solution with `ts-jest` for TypeScript support.
*   **Mocha**, paired with libraries like **Chai** (assertions) and **Sinon.JS** (mocking), provides a flexible testing setup, using **`ts-node`** for TypeScript execution.
*   Both frameworks allow you to write unit, integration, and (with other tools) E2E tests for TypeScript code.
*   Leverage TypeScript's type system within your tests for better reliability.
*   Follow testing best practices to create effective and maintainable test suites.

By incorporating robust testing practices into your TypeScript development workflow, you can build more reliable and higher-quality applications.

---

⬅️ [Back to TypeScript Build Tools and Integration](../19-ts-build-tools-integration/README.md) | ➡️ [Next: TypeScript Decorators (Deep Dive)](../21-ts-decorators-deep-dive/README.md) *(Placeholder)*
