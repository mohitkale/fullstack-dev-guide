# 47. TypeScript with Build Tools (Webpack, Rollup, esbuild)

While the TypeScript compiler (`tsc`) can transpile TypeScript to JavaScript, for most modern web applications, you'll integrate TypeScript into a more comprehensive build pipeline using tools like Webpack, Rollup, or esbuild. These tools offer features like bundling, code splitting, minification, hot module replacement (HMR), and asset management, which are essential for production-ready applications.

This tutorial explores how to configure and use TypeScript with these popular build tools.

## Table of Contents
1.  [Why Use Build Tools with TypeScript?](#why-build-tools-ts)
    *   Beyond `tsc`: Bundling, optimizations, HMR.
    *   Integrating with other assets (CSS, images).
    *   Ecosystem and plugins.
2.  [TypeScript with Webpack](#ts-webpack)
    *   [**Introduction to Webpack**](#intro-webpack)
        *   Core concepts: Entry, Output, Loaders, Plugins, Mode.
    *   [**Setting up TypeScript with Webpack**](#setup-ts-webpack)
        *   Installing dependencies: `webpack`, `webpack-cli`, `typescript`, `ts-loader` (or `babel-loader` with `@babel/preset-typescript`).
        *   Basic `webpack.config.js` for TypeScript.
        *   Using `ts-loader`:
            *   Configuration options (`configFile`, `transpileOnly`).
            *   Integrating with `tsconfig.json`.
        *   Using `babel-loader` with `@babel/preset-typescript`:
            *   Pros and Cons (faster transpilation, no type checking).
            *   Configuration.
            *   Using `fork-ts-checker-webpack-plugin` for type checking in a separate process.
    *   [**Source Maps**](#webpack-sourcemaps)
        *   Configuring `devtool` option in Webpack.
        *   Ensuring `sourceMap` is enabled in `tsconfig.json`.
    *   [**Development vs. Production Builds**](#webpack-dev-prod)
        *   Using Webpack modes.
        *   Code minification (`TerserWebpackPlugin`).
        *   Tree shaking.
    *   [**Hot Module Replacement (HMR)**](#webpack-hmr)
        *   Setting up `webpack-dev-server`.
    *   [**Example `webpack.config.js`**](#webpack-example-config)
3.  [TypeScript with Rollup](#ts-rollup)
    *   [**Introduction to Rollup**](#intro-rollup)
        *   Focus on ES Modules, smaller bundles, library development.
    *   [**Setting up TypeScript with Rollup**](#setup-ts-rollup)
        *   Installing dependencies: `rollup`, `@rollup/plugin-typescript`, `typescript`.
        *   Basic `rollup.config.js` for TypeScript.
        *   Using `@rollup/plugin-typescript`:
            *   Configuration options (referencing `tsconfig.json`).
            *   Handling `tslib` (helper library).
        *   Using `@rollup/plugin-babel` with `@babel/preset-typescript` (similar to Webpack).
    *   [**Output Formats**](#rollup-output-formats)
        *   `esm`, `cjs`, `umd`, `iife`.
    *   [**Tree Shaking**](#rollup-tree-shaking)
        *   Rollup's strength.
    *   [**Code Splitting**](#rollup-code-splitting)
    *   [**Example `rollup.config.js`**](#rollup-example-config)
4.  [TypeScript with esbuild](#ts-esbuild)
    *   [**Introduction to esbuild**](#intro-esbuild)
        *   Speed and simplicity, written in Go.
        *   Transpiler and bundler.
    *   [**Setting up TypeScript with esbuild**](#setup-ts-esbuild)
        *   Installing dependencies: `esbuild`, `typescript` (for type checking).
        *   Using esbuild CLI.
        *   Using esbuild JavaScript API.
        *   Configuration options (`entryPoints`, `bundle`, `outfile`, `platform`, `format`, `loader`).
    *   [**Type Checking with esbuild**](#esbuild-type-checking)
        *   esbuild focuses on speed and does not perform type checking by default.
        *   Running `tsc --noEmit` separately or alongside.
    *   [**Source Maps and Minification**](#esbuild-sourcemaps-minify)
        *   Built-in and fast.
    *   [**Development Server and Watch Mode**](#esbuild-dev-watch)
    *   [**Example esbuild setup (JS API)**](#esbuild-example-setup)
5.  [Choosing the Right Build Tool](#choosing-build-tool-ts)
    *   Webpack: Mature, extensive ecosystem, feature-rich, good for complex applications.
    *   Rollup: Excellent for libraries, efficient ES module bundling, good tree shaking.
    *   esbuild: Extremely fast, simpler configuration, good for CLIs, serverless functions, or when build speed is paramount.
    *   Vite: (Mention) Next-generation frontend tooling that uses esbuild for dev and Rollup for production builds.
6.  [Integrating with `tsconfig.json`](#build-tools-tsconfig)
    *   How build tools respect or override `tsconfig.json` settings.
    *   `compilerOptions.module` vs. build tool output format.
    *   `compilerOptions.noEmit` when build tool handles transpilation.
7.  [Conclusion](#conclusion-ts-build-tools)

## 1. Why Use Build Tools with TypeScript? <a name="why-build-tools-ts"></a>
While `tsc` is great for transpiling TS to JS and type checking, modern development often requires more:
*   **Bundling**: Combining multiple files into one or a few for efficient loading in browsers.
*   **Code Splitting**: Creating smaller chunks that can be loaded on demand.
*   **Minification**: Reducing file size for faster downloads.
*   **Tree Shaking**: Eliminating unused code.
*   **Asset Management**: Handling CSS, images, fonts, etc.
*   **Development Experience**: Hot Module Replacement (HMR), development servers.
*   **Ecosystem**: A vast array of plugins for various tasks (linting, testing, environment variables).

## 2. TypeScript with Webpack <a name="ts-webpack"></a>
Webpack is a highly configurable and powerful module bundler.

### Setting up TypeScript with Webpack <a name="setup-ts-webpack"></a>
**Installation:**
```bash
npm install --save-dev webpack webpack-cli typescript ts-loader
# or for Babel approach
# npm install --save-dev webpack webpack-cli typescript babel-loader @babel/core @babel/preset-typescript fork-ts-checker-webpack-plugin
```

**Basic `webpack.config.js` using `ts-loader`:**
```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  mode: 'development', // or 'production'
  entry: './src/index.ts',
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
  resolve: {
    extensions: ['.tsx', '.ts', '.js'], // Resolve these extensions
  },
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
  devtool: 'inline-source-map', // For better debugging
};
```
`ts-loader` uses your `tsconfig.json` by default.

**Using `babel-loader` with `@babel/preset-typescript`:**
Babel can strip TypeScript types and transpile modern JavaScript. It's often faster than `ts-loader` but doesn't perform type checking.
```javascript
// webpack.config.js (Babel part)
// ... other config
const ForkTsCheckerWebpackPlugin = require('fork-ts-checker-webpack-plugin');

module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'babel-loader', // Make sure .babelrc or babel.config.js is set up
        exclude: /node_modules/,
      },
    ],
  },
  plugins: [
    new ForkTsCheckerWebpackPlugin(), // Performs type checking in a separate process
  ],
  // ...
};
```
**`.babelrc` or `babel.config.js`:**
```json
{
  "presets": [
    "@babel/preset-env",
    "@babel/preset-typescript"
  ]
}
```
`fork-ts-checker-webpack-plugin` is crucial here for type safety.

## 3. TypeScript with Rollup <a name="ts-rollup"></a>
Rollup is a module bundler for JavaScript which compiles small pieces of code into something larger and more complex, such as a library or application. It's particularly well-suited for building libraries due to its efficient ES module output and tree-shaking capabilities.

### Setting up TypeScript with Rollup <a name="setup-ts-rollup"></a>
**Installation:**
```bash
npm install --save-dev rollup @rollup/plugin-typescript typescript tslib
```

**Basic `rollup.config.js`:**
```javascript
// rollup.config.js
import typescript from '@rollup/plugin-typescript';
import { terser } from 'rollup-plugin-terser'; // For minification

export default {
  input: 'src/index.ts',
  output: [
    {
      file: 'dist/bundle.esm.js',
      format: 'esm', // ES Module format
      sourcemap: true,
    },
    {
      file: 'dist/bundle.cjs.js',
      format: 'cjs', // CommonJS format
      sourcemap: true,
    },
    {
      file: 'dist/bundle.umd.js',
      format: 'umd', // UMD format for browser/Node.js
      name: 'MyLibrary', // Required for UMD
      sourcemap: true,
      globals: { // if you have external dependencies
        // 'lodash': '_'
      }
    }
  ],
  plugins: [
    typescript({ 
        tsconfig: './tsconfig.json', 
        tslib: require.resolve('tslib'), // Ensure tslib is resolved correctly
        declaration: true, // Generate .d.ts files
        declarationDir: 'dist/types'
    }),
    terser() // Minify output for production
  ],
  external: [] // Add external dependencies here (e.g., 'lodash')
};
```
`@rollup/plugin-typescript` will use your `tsconfig.json`.

## 4. TypeScript with esbuild <a name="ts-esbuild"></a>
esbuild is an extremely fast JavaScript bundler and minifier, written in Go. Its main goal is to bring a new era of build tool performance.

### Setting up TypeScript with esbuild <a name="setup-ts-esbuild"></a>
**Installation:**
```bash
npm install --save-dev esbuild typescript
```

**Using esbuild CLI:**
```bash
esbuild src/index.ts --bundle --outfile=dist/bundle.js --platform=browser --format=esm --sourcemap --minify
# For type checking, run tsc separately
tsc --noEmit
```

**Using esbuild JavaScript API (e.g., in a `build.js` script):**
```javascript
// build.js
const esbuild = require('esbuild');
const { exec } = require('child_process');

async function build() {
  try {
    await esbuild.build({
      entryPoints: ['src/index.ts'],
      bundle: true,
      outfile: 'dist/bundle.js',
      platform: 'browser', // 'node', 'neutral'
      format: 'esm', // 'cjs', 'iife'
      sourcemap: true,
      minify: true,
      // loader: { '.png': 'dataurl' } // Example for handling other file types
    });
    console.log('Build successful!');

    // Run type checking separately
    console.log('Running type checks...');
    exec('tsc --noEmit', (err, stdout, stderr) => {
      if (stdout) console.log(stdout);
      if (stderr) console.error(stderr);
      if (err) {
        console.error('Type check failed!');
      } else {
        console.log('Type check successful!');
      }
    });

  } catch (e) {
    console.error('Build failed:', e);
    process.exit(1);
  }
}

build();
```
Run this script with `node build.js`.

**Type Checking:** esbuild prioritizes speed and does **not** perform type checking. You must run `tsc --noEmit` separately to check types.

## 5. Choosing the Right Build Tool <a name="choosing-build-tool-ts"></a>
*   **Webpack**: Best for complex applications requiring a rich plugin ecosystem, advanced code splitting, and HMR. Can be complex to configure.
*   **Rollup**: Ideal for libraries, especially those targeting ES Modules. Simpler configuration than Webpack for this use case, excellent tree shaking.
*   **esbuild**: Choose for its incredible speed. Great for CLIs, serverless functions, or projects where build time is critical. Simpler configuration but less mature plugin ecosystem. Remember to handle type checking separately.
*   **Vite**: A modern frontend tool that uses esbuild during development for fast HMR and Rollup for production builds. Offers a great out-of-the-box experience for TypeScript projects (React, Vue, etc.).

## 6. Integrating with `tsconfig.json` <a name="build-tools-tsconfig"></a>
*   Most TypeScript loaders/plugins for build tools will respect your `tsconfig.json` settings (e.g., `strict`, `paths`, `target` for syntax downleveling).
*   **`compilerOptions.module`**: This setting in `tsconfig.json` might be less relevant if the build tool handles module formatting (e.g., Rollup's `format` option, esbuild's `format` option). It's often set to `ESNext` in `tsconfig.json` to let the build tool manage final module output.
*   **`compilerOptions.noEmit`**: Set this to `true` in `tsconfig.json` if the build tool (e.g., Babel via `babel-loader`, or esbuild) is responsible for transpiling and emitting JavaScript files, and `tsc` is only used for type checking.
*   **`compilerOptions.isolatedModules: true`**: Often recommended or required when using transpilers like Babel or esbuild that process files individually without full type system awareness. This ensures each file can be transpiled safely in isolation.

## 7. Conclusion <a name="conclusion-ts-build-tools"></a>
Integrating TypeScript with modern build tools like Webpack, Rollup, or esbuild is essential for building robust, optimized, and scalable applications. Each tool has its strengths, and the choice depends on your project's specific needs, complexity, and performance requirements. Understanding how to configure these tools for TypeScript will significantly enhance your development workflow.

---

⬅️ [Back to Advanced TypeScript Configuration (tsconfig deep dive)](../46-ts-advanced-tsconfig/README.md) | ➡️ [Next: TypeScript for Full-Stack Development (Frontend & Backend)](../48-ts-fullstack-dev/README.md) *(Placeholder)*
