![Build](https://github.com/TheLartians/modern-wasm-starter/workflows/Build/badge.svg)

# Modern WASM Starter

A starter template to easily create WebAssembly projects for npm using type-safe C++ code with automatic declarations.
This project should remove most of the boilerplate code required to create a modern type-safe WebAssembly project.

## Features

- Integrated node.js packaging and dependency management through npm
- Type safety through TypeScript
- CMake build system 
- Integrated C++ dependency management using [CPM.cmake](https://github.com/TheLartians/CPM.cmake) 
- Automatic bindings and typescript declarations using the [Glue](https://github.com/TheLartians/Glue) library
- Integrated test suite using [jest](https://jestjs.io)
- Code formatting through [prettier](https://prettier.io)
- Semi-automatic memory management using scoped 

## Usage

### Build WebAssembly code

To be able to build WebAssembly code from C++ using Emscripten, you must first [install and activate the emsdk](https://emscripten.org/docs/getting_started/downloads.html).
To compile the WebAssembly, run the following commands from the project's root directory.

```bash
npm install
npm run build:wasm
```

This will create the files `source/WasmModule.js` and `source/WasmModule.d.ts` from the C++ code in the [wasm](wasm) directory.
To bind your own project to JavaScript, add it as a CPM.cmake dependency in the [CMakeLists.txt](wasm/CMakeLists.txt) and define the bindings [wasmGlue.cpp](wasm/source/wasmGlue.cpp) source file.
After running the `build:wasm` script, the TypeScript declarations should be automatically updated. 

### Run tests

The following command will build and run the test suite.

```bash
npm run test
```

For more rapid developing, tests can also be run in watch mode.

```bash
npm start
```

### Run prettier

The following command will run prettier on the TypeScript source code.

```
npm run fix:style
```

## Memory management

As JavaScript has no destructors, any created C++ objects must be deleted manually, or your webapp will suffer a memory leak.
To simplify this, the project introduces scopes that semi-automatically take care of memory management , that can be used as illustrated below.

```ts
import { withGreeter } from "modern-wasm-starter";

withGreeter((greeterModule) => {
  // construct a new C++ Greeter instance
  const greeter = new greeterModule.Greeter("Wasm");

  // call a member function
  console.log(greeter.greet(greeterModule.LanguageCode.EN));
});
// any created instances will be deleted at the end of the function (exception-safe)
```

For additional techniques, such as local scopes or persisting values outside of the scope, see the [tests](__tests__/wasm.ts) or [API](source/wasmWrapper.ts).
