# INDEX

- [INDEX](#index)
- [TypeScript related terms](#typescript-related-terms)
  - [Transpiling](#transpiling)
  - [Ambient types](#ambient-types)
- [Using locally installed typescript compiler](#using-locally-installed-typescript-compiler)
- [Making VS code perform intellisense based on TypeScript version in project](#making-vs-code-perform-intellisense-based-on-typescript-version-in-project)
- [`tsconfig.json` being ignored when source files manually specified](#tsconfigjson-being-ignored-when-source-files-manually-specified)
- [Faulty error when `.ts` and compiled `.js` file are open together in editor](#faulty-error-when-ts-and-compiled-js-file-are-open-together-in-editor)
- [Why we compile TS to `ESNext` (TODO)](#why-we-compile-ts-to-esnext-todo)

# TypeScript related terms

## Transpiling

- Transpiling, short for "source to source compiling," refers to the process of converting source code from one higher-level programming language to another higher-level programming language. 

- Unlike traditional compilation, which typically involves converting higher-level code to lower-level code (such as machine code), transpiling allows you to write code in one language and then convert it into another language that is also at a higher level of abstraction.

- For example, TypeScript is a higher-level language that adds static typing and additional features to JavaScript. When you transpile TypeScript code, you convert it into JavaScript code, which is another higher-level language. 
  
  This allows you to write code in TypeScript, taking advantage of its features, and then transpile it into JavaScript that can be executed by JavaScript engines.

## Ambient types

- In the context of TypeScript, the phrase "types can be ambient" refers to the ability to define type information for existing JavaScript code or external libraries without having to modify the original code itself. 

  It allows TypeScript to understand and provide type information for JavaScript code, enabling static type checking and code intelligence features.

- Ambient types are typically defined in separate TypeScript declaration files (with a `.d.ts` extension) that contain only type declarations. 

  These type declarations describe the shape and behavior of the JavaScript code in terms of TypeScript types.
  
  Here's an example of an ambient type declaration for an existing JavaScript library:

  ```typescript
  // my-library.d.ts
  declare namespace MyLibrary {
    function doSomething(arg: string): void;
    function calculate(a: number, b: number): number;
    // ... other type declarations
  }
  ```

  With this ambient type declaration, you can now use the `MyLibrary` functions in your TypeScript code and benefit from type checking:

  ```typescript
  import { MyLibrary } from 'my-library';

  MyLibrary.doSomething('Hello');
  const result = MyLibrary.calculate(5, 10);
  ```

- By making types ambient, TypeScript allows developers to gradually introduce type safety to their JavaScript codebases and leverage the benefits of static typing without requiring extensive modifications to existing code. 

  It enables seamless integration with JavaScript libraries and frameworks, making it easier to work with existing codebases in a type-safe manner.

> ***Note***: Definitions for most of the popular JavaScript libraries have already been written by the [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) community so for most purposes:
> - The definition file already exists.
> 
>   Or at the very least, 
> - You have a vast list of well reviewed TypeScript declaration templates already available.

# Using locally installed typescript compiler

- `tsc` is a binary of the `typescript` module that we need to explicitly reference from the sub-folder of its module folder. 

- We instead declare a script in `package.json`, since the `node_modules/.bin` directory is already added to the PATH for those scripts, allowing direct access to the binaries of the installed modules:
  ```json
  {
    "scripts": {
        "tsc": "tsc" 
    }
  }
  ```

- We pass the name of the file we want to compile to JavaScript using the following syntax:
  ```
  npm run tsc -- index.ts
  ```

# Making VS code perform intellisense based on TypeScript version in project

Add the following to `.vscode/settings.json`:

```json
{
  "typescript.tsdk": "./node_modules/typescript/lib"
}
```

This makes the typescript version you have installed and the recommendations VSCode provides consistent.

# `tsconfig.json` being ignored when source files manually specified
    
- When you manually specify source files to be compiled by the `tsc` binary like this:

    ```
    npm run tsc -- index.ts
    ```

    the `tsconfig.json` file is ignored. 

- This behavior is intended to provide flexibility when using the TypeScript compiler.

    According to this thread on [GitHub](https://github.com/microsoft/TypeScript/issues/6591), the reason for this behavior is to avoid conflicts when mixing command-line source files with the configuration specified in the `tsconfig.json` file. 

- If you explicitly provide source files as command-line arguments, the compiler assumes that you want to compile only those specific files and ignores the `tsconfig.json` file.
 
- If we try to specify both the source files as well as the config files, this is what we get:
  
  ```console
  rohan-verma@g3-3500:~$ npm run tsc -- ./index.ts --project tsconfig.json

  > tsc
  > tsc ./index.ts --project tsconfig.json

  error TS5042: Option 'project' cannot be mixed with source files on a command line.
  rohan-verma@g3-3500:~$ 
  ```

- So, the best approach is to just run `tsc`, without specifying any source files, so that the compiler automatically searches for the `tsconfig.json` file in the current directory and its parent directories and use the configuration specified in that file.

# Faulty error when `.ts` and compiled `.js` file are open together in editor

- In some instances, the error `Duplicate function implementation.ts(2393)` is caused by a bug in Visual Studio Code. 

- If you are using VS Code and have both the `.ts` and the transpiled `.js` files open in the editor, it may trigger the error; because of it considering the function definition in the `.ts` file and the compiled `.js` file as separate.

- Try closing the transpiled `.js` file and only keep the `.ts` file open to see if the error goes away.

# Why we compile TS to `ESNext` (TODO)

