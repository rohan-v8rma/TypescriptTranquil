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

