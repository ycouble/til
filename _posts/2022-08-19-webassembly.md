---
title: Web Assembly
description: Lecture notes on Web Assembly
date: August 19, 2022
categories: [web, notes]
layout: post
toc: false
comments: true
hide: false
search_exclude: false
---

- Problem that WebAssembly addresses : make the browser as generic as a computer, i.e. execute most programs
- Principles

  - But: Make it possible to reuse components coded in C, C++, Rust, etc. directly in the browser
  - How: Convert C/CPP/Rust into LLVM representation
  - Theoretical workflow: compile/export c/cpp file in wasm format, serve wasm to client browser, interact with wasm module from javascript code

    ```mermaid
    flowchart LR;
        subgraph web[webservice]
            direction TB
            jscode[service.js]--interacts-->wasm
        end
        code[opencv.cpp]--export-->wasm[opencv.wasm]
        web--served-->client

    ```

  - WebAssembly is not a virtual machine, it is processed

- 2 formats: Wat (text file), Wasm (binary)
- Explorer: https://mbebenita.github.io/WasmExplorer/
- JS / WA Interactions:
  - Fetch the file and read it (binary)
  - Use of the WebAssembly module (native) initialize WA module
  - Instantiate module
  - Then, whenever functions defined in the WA are called, the WA runtime will execute and deliver results to the JS runtime through a share memory
- Limitations:
  - No DOM interactions but possible to interact with external functions
  - Interoperability between JS and WA only through integers and floats --> glue code
  - No garbage collector (memory management is important)
  - WA Runtime dependent on the browser

# Quetions

- Is it possible to export **anything** from a C/CPP/etc. program ? What are the limitations ?

# References

- https://www.youtube.com/watch?v=P1xIKrrncS8
- https://webassembly.org/
- https://developer.mozilla.org/fr/docs/WebAssembly
