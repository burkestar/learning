# Javascript

- [ECMAScript language specification](https://ecma-international.org/technical-committees/tc39/?tab=published-standards) - published ~yearly
- [Mozilla Guide for Javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
    - [Javascript Reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference)
    - [Browser Support for Javascript APIs](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Browser_support_for_JavaScript_APIs)
- [javascript.info](https://javascript.info)
- [Roadmap for Learning Javascript](https://roadmap.sh/javascript)
- [Certified Full Stack Developer Curriculum on FreeCodeCamp](https://www.freecodecamp.org/learn/full-stack-developer/)
- [Javascript30](https://javascript30.com) - 30 projects to practice
- [Awesome Javascript](https://github.com/sorrycc/awesome-javascript) roundup of libraries, tools and resources
- Cheat sheets: [dev.to](https://devhints.io/es6)

## Books

- [Eloquent Javascript](https://eloquentjavascript.net) - free online or buy print edition
- [You Don't Know JS](https://github.com/getify/You-Dont-Know-JS)
- [Javascript - The Good Parts](https://andersonguelphjs.github.io/OReilly_JavaScript_The_Good_Parts_May_2008.pdf) - outdated but a classic

## Videos

- [Net Ninja Youtube Channel](https://www.youtube.com/channel/UCW5YeuERMmlnqo4oq8vwUpg) videos to learn

## Notes

- [ES2022 (ES13)](https://262.ecma-international.org/13.0/index.html?_gl=1*1aydoxf*_ga*MTk2NzAyNTM0Ny4xNzU1NjgxMzQw*_ga_TDCK4DWEPP*czE3NTU2ODEzNDAkbzEkZzEkdDE3NTU2ODE5NzckajYwJGwwJGgw) is the most recent version with good browser coverage (see [feature summary](https://caniuse.com/?feats=mdn-javascript_builtins_array_at,mdn-javascript_builtins_regexp_hasindices,mdn-javascript_builtins_object_hasown,mdn-javascript_builtins_error_cause,mdn-javascript_operators_await_top_level,mdn-javascript_classes_private_class_fields,mdn-javascript_classes_private_class_methods,mdn-javascript_classes_static_class_fields,mdn-javascript_classes_static_initialization_blocks))
- ES6 (ES2015) has the most comprehensive browser support and is a good starting point for core language features
- In general, the N-2 version is a choice for new projects as it will be recent and have good browser support.
- When writing code for newer versions of the language, use a transpiler.

### Design Goals

- "language for the masses"
- Easy to use and accessible for developers
- Suitable for dynamic and interactive web experiences
- Lightweight and interpreted, rather than compiled
- Compatibility with wider range of browsers
- Interoperable with other web standards - HTML and Document Object Model (DOM) for structuring web pages and CSS for styling elements
- Safety to run untrusted code on users' devices within a sandbox
- Performance

### Language features

- High level, interpreted scripting language
- Just-in-time (JIT) compiled
- Conforms to ECMAScript standard
- Dynamic, weak, duck typing
- Multi-paradigm for functional, object-oriented and procedural programming styles.
- Prototype-based object orientation
- First class functions
- Event-driven to handle user interactions
- Asynchronous
- Client-side and server-side
- Cross-platform (OS, browser, server, desktop, mobile)
- Variable scoping: global (defined outside function or block), function (locally scope using `var`), block (using `let` and `const`)
- Variable hoisting where variable and function declarations are conceptually moved to top of containing scope when compiling so that variables can be used and functions called before they are declared in code without causing a `ReferenceError`.  `var` and `function` are hoisted with `undefined` value until initialized. `let`, `const` and `class` are hoisted but not initialized and will raise `ReferenceError` if used in "Temporal Dead Zone" before their declaration line.
- Zero-based array indexing

### Tools and Frameworks

Runtime Environments:

- [NodeJS](https://nodejs.org/en/about) is an open source, cross-platform runtime built on Chrome's V8 engine for high performance and is used for running javascript outside a browser such as for server-side web applications.  Asynchronous, event-driven.  Fast, scalable.
- [Deno](https://deno.com) built on V8 and Rust, providing modern and secure runtime for Javascript and Typescript
- [Bun](https://bun.com) aims for NodeJS compatibility, web compatibility, support for Typescript and JSX and being an all-in-one toolkit with bundler, test runner, package manager and transpiler. Written in Zig for speed.
- Browser-based: Chrome V8, Firefox Spidermonkey, Safari JavaScriptCore

Package Managers:

- [YARN](https://yarnpkg.com/) - fast
- [pnpm](https://pnpm.io/) - fast, disk space efficient
- [npm](https://www.npmjs.com/) - Node Package Manager

Bundlers:

- [Vite](https://vite.dev/) - instant server start using native ES modules, fast hot-module replacement, support for Typescript and JSX, zero configuration, uses Rollup bundler with tree-shaking, and plugins
- [Parcel](https://github.com/parcel-bundler/parcel) - fast, zero config, written in Rust, built on SWC
- [Rolldown](https://rolldown.rs) - written in Rust, Rollup compatible, esbuild compatible, bundler in vite
- [Webpack](https://github.com/webpack/webpack)

Transpilers:

- Transpilers convert code written in one version or dialect of the language into another for backwards compatibility with older browsers and runtimes. Newer features (superset of javascript) are translated into javascript.
- [esbuild](https://esbuild.github.io) written in Go for speed (10-100x faster than webpack) and simplicity
- [SWC](https://swc.rs) Speedy Web Compiler, written in Rust for speed (20x faster than Babel single threaded)
- [Babel](https://babel.dev) is slower but more modular with configurability and plugins
- [tsc](https://www.typescriptlang.org/docs/handbook/compiler-options.html) Typescript converter. Superset of javascript that adds static typing. 

Formatters:

- [biome](https://biomejs.dev) - 97% compatible with Prettier and 35x faster. Supports javascript, typescript, JSX, TSX, HTML, CSS and GraphQL.  Formatter and linter.
- [prettier](https://prettier.io) - opinionated, widely supported

Frontend Frameworks:

- React.js
- Next.js - extends React with server-side rendering, file-system routing, API routes, and server components
- Angular
- Vue.js
- Nuxt - full stack framework for Vue.js
- Svelte
- SolidJS

Backend Frameworks:

- Express.js
- NestJS

Testing:

- [vitest](https://vitest.dev) - Vite powered, Jest compatible, uses Chai for assertions, tinyspy for mocking, code coverage using v8 or istanbul, smart watch to re-run tests based on changes, ESM, Typescript and JSX support.
- Jest - zero-config, suitable for unit and component testing
- Mocha, Chai, Jasmine - simple unit testing and assertions
- Cypress for end-to-end browser-based testing, runs in browser.
- Playwright for end-to-end browser-based testing, runs outside the browser. Supports a variety of languages and browsers. More complex to setup but handles more complex scenarios (cross-browser, multi-tab/window, mobile emulation)
- Istanbul for code coverage

Template engines:

- JSX
- TSX
- Handlebars
- Pug
- Mustache

Data visualization:

- [Nivo](https://nivo.rocks) uses d3 and react
- [Echarts](https://echarts.apache.org/en/index.html)
- Recharts uses react components
- d3
- three.js

Utilities:

- lodash
- underscore

Misc:

- Axios - promise-based HTTP client

