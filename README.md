---
title: A Brief History of Types
---

# A Brief History of Types

## (With TypeScript)

By [@blakeembrey](https://github.com/blakeembrey)

Note:
- Blake, software engineer @ Opendoor, changing real estate
- Past: `typings`, `ts-node`, GitHub Types, TypeStrong
- This talk: history of TypeScript, package managers, other languages, finishing up

---

## In the Beginning (Oct 2012)

* `any`, `string`, `number`, `bool`, `interface`, `void`, `[]`, function, globals
* `/// <reference path="" />`
* `.d.ts` files
* Generics, TypeScript modules (`export =`, `import =`), enums (v0.9, Jun 2013)

Note:
- Basic types
- Interfaces
- Declaration files
- Soon generics and TS modules

----

```ts
interface Options {
  async?: boolean;
  contents?: { [key: string]: any };
  onError?(): void;
}

var options: Options = {
  contents: {
    key: 'any value'
  }
};

export = Options;
```

---

## 1.3 (Nov 2014)

* Tuple types
* `protected`

Note:
- 1.1 is a largely a rewrite
- No 1.2 release

----

```ts
var x: [string, number];
x = ['hello', 10]; // OK.
x = [10, 'hello']; // Error.

class Thing {
  protected doSomething() { /* ... */ }
}

class MyThing extends Thing {
  public myMethod() { this.doSomething() } // OK.
}

new MyThing().doSomething(); // Error.
```

---

## 1.4 (Jan 2015)

* Union types
  * Stricter generics
  * Better type inference
* `let` and `const`
* Template strings
* Type Guards (`typeof` and `instanceof`)
* Type aliases with `type`
* Inlined enums with `const enum`

----

```ts
type Cmd = string | string[];

function formatCmd (cmd: Cmd) {
  if (typeof cmd === 'string') {
    return cmd.trim();
  }

  return cmd.join(' ');
}

const word = 'world';

let res = formatCmd(['hello', word]);
res = formatCmd(`hello ${word}`);
```

---

## 1.5 (Jul 2015)

* ES6 modules
* ES6 destructuring
* The `namespace` keyword
* `for..of`
* Decorators
* Computed properties (`{ [foobar]: true }`)
* Introduced `tsconfig.json`

Note:
- Alignment with ES6
- Configuration files, used CLI before
- I started using TypeScript seriously

----

```ts
export interface ArgvOptions {
  [key: string]: string;
}

export function toCmd (opts: ArgvOptions) {
  const res: string[] = [];

  for (const k of Object.keys(opts)) {
    res.push(`--${k}=${opts[k]}`);
  }

  return res.join(' ');
}
```

---

## 1.6 (Sep 2015)

* JSX and `as [type]`
* Intersection types
* Local type declarations
* `abstract` classes
* Generic `type` keyword
* ES6 generators
* User-defined type guards

Note:
- Community requests, JSX, intersections, generators and user type guards
- Finishing up `typings` which abuses ES6 modules and the `module` keyword

----

```ts
declare function extend<T, U>(first: T, second: U): T & U;

extend({ a: 'a' }, { b: 'b' }).b // OK.

function isCat(a: any): a is Cat {
  return a.name === 'kitty';
}

var x: Cat | Dog;
if (isCat(x)) {
  x.meow(); // OK.
}
```

---

## 1.7 (Nov 2015)

* `async`/`await`
* `this` typing
* ES7 exponent (`**`)

Note:
- `async` and `await` come right after

----

```ts
async function printer(elements: Array<Promise<string>>) {
  for (const element of elements) {
    console.log(await element);
  }
}
```

---

## 1.8 (Jan 2016)

* Type parameter constraints (`foo<T extends U, U>()`)
* Control flow analysis
* Augment global and module scopes
* String literal types
* `--allowJs`
* `this`-based type guards
* Comments in `tsconfig.json`

Note:
- JavaScript support
- String literals
- `this` type guards
- Control flow analysis, unreachable code, unused labels, implicit returns

----

```ts
function assign<T extends U, U>(target: T, source: U): T {
  for (const id in source) target[id] = source[id];
  return target;
}

assign({ a: 1, b: 2}, { a: 10 }); // OK.
assign({ a: 1, b: 2}, { e: 0 }); // Error.

interface AnimationOptions {
  deltaX: number;
  deltaY: number;
  easing: "ease-in" | "ease-out" | "ease-in-out";
}
```

---

## 2.0 (Jul 2016)

* NPM [`@types`](https://blogs.msdn.microsoft.com/typescript/2016/06/15/the-future-of-declaration-files/)
* `null` and `undefined` types
* Dot property type guards
* Non-null assertion operator (`!`)
* Control flow outside of conditional
* Tagged union types
* `never` bottom type
* `readonly`
* `this` type for functions
* Globbing in `tsconfig.json`

Note:
- A year after `typings`
- Install types from NPM
- ES6 modules via first-class type resolution, `node_modules/@types`
- File globbing solves common confusion
- `null` and `undefined` types, `--strictNullChecks`
- `never` bottom type - what's a bottom type?

----

```ts
(x as number | undefined).toFixed(2); // Error.

type Foo = (x?: string) => string // x is `string | undefined`.

function area(s: Shape) {
  switch (s.kind) {
    case "square": return s.size * s.size;
    case "circle": return Math.PI * s.radius * s.radius;
  }
}

function assert(message: string): never {
  throw new Error(message);
}
```

---

## 2.1 (Nov 2016)

* `keyof` and lookup types
* Mapped types
* `Partial`, `Readonly`, `Record`, `Pick`
* Object spread
* Literal type inference

Note:
- Interesting usages of types here
- Dynamically create types from other types

----

```ts
interface Person {
  name: string;
  age: number;
  location: string;
}

type K = keyof Person; // "name" | "age" | "location"
type P = Person['name']; // string

function get<T, K extends keyof T>(obj: T, key: K) {
  return obj[key]; // T[K]
}

type Partial<T> = { [P in keyof T]?: T[P] };
```

---

## 2.2 (Feb 2017)

* `object` type
* Dot property access for string index signatures

Note:
- Before `object`, no way to represent non-primitive types
- In March, an [issue](https://github.com/Microsoft/TypeScript/issues/14833) is created in TypeScript
- Type system is [turing complete](https://gist.github.com/hediet/63f4844acf5ac330804801084f87a6d4) (since 2.1)

----

```ts
declare function create(o: object | null): void;

create(42); // Error.
create({ a: 1 }); // OK.
```

---

## 2.3 (Apr 2017)

* Async iterators and generators
* Generic parameter defaults
* `--strict`

----

```ts
async function f() {
  for await (const x of g()) {
     console.log(x);
  }
}

declare function create<
  T extends HTMLElement = HTMLDivElement,
  U = T[]
>(element?: T, children?: U): Container<T, U>;
```

---

## 2.4 (Jun 2017)

* Dynamic `import()`
* String enums

----

```ts
async function getZipFile(name: string, files: File[]): Promise<File> {
  const zipUtil = await import('./utils/create-zip-file');
  // Use `zipUtil`.
}
```

---

## 2.5 (Aug 2017)

* `@type` assertion comments in JavaScript

----

```ts
var x = /** @type {SomeType} */ (AnyParenthesizedExpression);
```

---

## 2.6 (Oct 2017)

* Strict functions ([contravariance](https://www.stephanboyer.com/post/132/what-are-covariance-and-contravariance))
* `@ts-ignore` comments

Note:
- TypeScript arguments are bivariant

----

```ts
declare let f1: (x: Animal) => void;
declare let f2: (x: Dog) => void;
declare let f3: (x: Cat) => void;
f1 = f2;  // Error with `--strictFunctionTypes`.
f2 = f1;  // OK.
f2 = f3;  // Error.
```

---

## 2.7 (Jan 2018)

* Constant properties (`{ [FOO](): string }`)
* `unique symbol`
* Fixed length tuples
* ES numeric separators (`1_000_000`)

----

```ts
import { inspect } from 'util';
// const inspect: { readonly custom: unique symbol };

class Box {
  constructor(public value: string) {}

  [inspect.custom]() {
    return `Box<${this.value}>`;
  }
}
```

---

## 2.8 (Mar 2018)

* Conditional types
* `Exclude`, `Extract`, `NonNullable`, `ReturnType`, `InstanceType`

----

```ts
type Exclude<T, U> = T extends U ? never : T;
type Extract<T, U> = T extends U ? T : never;
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;

type T1 = Exclude<"a" | "b" | 1 | 2, string>; // 1 | 2
type T2 = Extract<"a" | "b" | "c", "a" | "f">; // "a"
type T3 = ReturnType<() => string>; // string
```

---

## 2.9 (May 2018)

* `number` and `symbol` properties with `keyof` and mapped types
* Generic types in tagged templates

Note:
- Mapped types with all properties

----

```ts
const s = Symbol();

type Foo = {
  a: string;   // String-like name
  5: string;   // Number-like name
  [s]: string; // Symbol-like name
}

type K1 = keyof Foo; // "a" | 5 | typeof s
type K2 = Extract<keyof Foo, string>; // "a"
type K3 = Extract<keyof Foo, number>; // 5
type K4 = Extract<keyof Foo, symbol>; // typeof s
```

---

## 3.0 (Jul 2018)

* Project references
* Tuples in rest parameters and spread expressions
  * Optional elements in tuples
  * Rest elements in tuples
* `unknown` top type

Note:
- Tuples for rest parameters is huge
- Spread types in functions
- Top type `unknown` - what is that?

----

```ts
declare function foo(a: number, b: string): void;

const args: [number, string] = [42, "hello"];

foo(...args);

declare function spreadmap<T extends any[], U>(
  iterable: Iterable<T>,
  func: (...args: T) => U
): Iterable<U>;

// Only equality operators are allowed with unknown.
declare function isFunction(x: unknown): x is Function;
```

---

## 3.1 (Sep 2018)

* Mapped types on tuples and arrays
* Property declarations on functions
* `typesVersions` in `package.json`

Note:
- Box or unboxing on arrays or variadic functions

----

```ts
declare function all<T extends any[]>(
  promises: { [K in keyof T]: PromiseLike<T[K]> | T[K] }
): Promise<T>;

all<[1, 2, 3]>([Promise.resolve(1), Promise.resolve(2), 3])
  .then(([a, b, c]) => { /* 1, 2, 3 */ });

declare function zip<T extends any[]>(
  ...iterables: { [K in keyof T]: Iterable<T[K]> }
): Iterable<T>;

zip(['a', 'b', 'c'], [1, 2, 3]) // Iterable<[string, number]>
```

---

# Why TypeScript?

## Real-world JavaScript

* Standardization
* Declaration files
* Release frequency
* _Written in TypeScript_
* _Team responsiveness_

Note:
- Adoption driven by:
- Standardization with JavaScript community
- Declaration files
- Release frequency
- Lots of other factors:
- E.g. TypeScript issues and responsiveness

----

# Standardization

* ES6 modules (1.5)
* `tsconfig.json` (1.5)
* ESNext features (1.5+)
* JSX support (1.6)
* Node modules and `package.json` (2.0)
* Project references (3.0)
* Babel preset (new)
* Incremental typing (always)

----

# Declaration files

* Write `.d.ts` files for `.js` modules
* Publish `.ts` modules as `.js` and `.d.ts` files
* Community created `.d.ts` files for incremental adoption of TS in JS modules
* Enabled [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) and [Typings](https://github.com/typings/typings)
* Added intellisense to JavaScript in VS Code

Note:
- Biggest driver of adoption
- Use libraries you did before TypeScript
- Enabled intellisense for JavaScript

----

# Release frequency

* 2015 - Unions, ES6 modules, JSX, `async`/`await`
* 2016 - NPM `@types`, mapped and lookup types
* 2017 - `object`, `import()`
* 2018 - Constant properties, conditional types, project references, mapped tuples and arrays

Note:
- TypeScript is publicly 5 years old
- 4 years of rapid types improvements
- Type most things in JavaScript

---

# Type Management

* [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped)
* [TSD](https://github.com/DefinitelyTyped/tsd)
* [Typings](https://github.com/typings/typings)
* [`@types`](https://www.npmjs.com/~types)

Note:
- Most valuable community contribution

----

# DefinitelyTyped

* First commit October 2012
* Provided "global" types (no module system yet)
* Maintained by Microsoft since `@types`

Note:
- Really basic global types
- Hard to create complex projects without conflicts

----

# TSD

* First commit October 2012
* Queried and copied files from DefinitelyTyped
* Deprecated with Typings

Note:
- Used GitHub API, searches and downloads files from DefinitelyTyped

----

# Typings

![Typings API traffic](static/downloads-over-time.png)

* Released October 2015
* Built using ES6 module declarations and `module`
* Recursive resolution of modules and versions
* Used idempotent URLs and introduced registry

Note:
- TypeScript 1.5 introduced ES6 modules
- Trick TypeScript into having a type module system
- `typings.json` pointer to entry files, e.g. NPM + `node_modules`

----

# `@types`

![`@types/react` downloads over time](static/types-react-over-time.png)

* Deprecated `typings`
* DefinitelyTyped to `@types/*` on NPM
* Added `typings` keyword to `package.json`
* Updates DefinitelyTyped to ES6 modules

Note:
- Official solution
- Typings resolution independent of module resolution
- `paths` in `tsconfig.json` for modules
- `types` in `tsconfig.json` for globals
- The top couple of packages have more downloads than all of Typings per day

---

# Alternatives

* Flow
* ScalaJS
* ReasonML
* LiveScript
* _Many others..._

Note:
- Some embraced types in JavaScript, e.g. Flow and TypeScript
- Some embraced existing languages, e.g. ReasonML and LiveScript
- Others are based on existing languages, e.g. LiveScript

----

# Flow

## And `flow-typed`

* Written in OCaml
* Released [November 2014](https://code.fb.com/web/flow-a-new-static-type-checker-for-javascript/)
* Introduced `.flow` declaration files December 2015
* Released `flow-typed` in October 2016
* Promotes checking in files and global declarations
* Best practice today is to [copy and paste types between declarations](https://github.com/flow-typed/flow-typed/issues/2842)

Note:
- Flow is OCaml
- Presented as JS with inferred types
- Hasn't captured developer mindshare
- Released after TypeScript
- Declaration files and `typed-typed` were late to the party
- Never quite solved the DX

---

# Python

* [MyPy](http://mypy-lang.org/)
* [PEP 484 - Type Hints](https://www.python.org/dev/peps/pep-0484/) (Sep 2014)
* `typing` module

Note:
- Dynamic language with popular static typing
- Originally built on top of Python before becoming native
- Accepted May 2015
- Released Sep 2015
- Import from `typing` module
- Python can introspect types
- Interesting to see Python evolve with native types

----

```py
from typing import Iterable

def print_numbered(items: Iterable[int]) -> None:
    for n, x in enumerate(items):
        print(n + 1, x)
```

---

# Questions?

What we learned:

* History of types in TypeScript
* Impact of supporting JavaScript
* Power of developer experience
* Other JS languages with types
* Other dynamic languages with types

[github.com/blakeembrey/a-brief-history-of-types-with-typescript](https://github.com/blakeembrey/a-brief-history-of-types-with-typescript)
