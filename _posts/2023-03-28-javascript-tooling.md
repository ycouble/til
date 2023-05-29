---
title: Javascript Tooling
description: Notes on some tooling
date: March 28, 2023
categories: [web, links]
layout: post
toc: false
comments: true
hide: false
search_exclude: false
---

- Bundler : Resolve all dependencies and package all the application into a single file.
  - Reduces time spent by browser searching and loading files
  - Enables some tree shaking (unused code in a dependency)
  - One big file
  - ex: esbuild
- Transpiler : Convert code into a supported standard target
  - Enables modern languages (TS, JSX)
  - ex: babel, tsc
- Linter / Formatter : Static code analysis / formatting
  - Share and enforce code standards
  - Early debug
  - ex: eslint
- Package Manager : Install and manage external modules
  - ex: yarn, npm, pnpm

source: https://www.youtube.com/watch?v=z0cyuYwBImQ
