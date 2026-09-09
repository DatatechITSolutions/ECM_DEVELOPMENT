# ECM JavaScript Coding Standards — ES6+

**Parent:** ECM Core Software Development Standards  
**Applies to:** New JavaScript code and code that is materially modified  
**Language baseline:** ES6+  
**Project-specific rules:** A project may add stricter compatibility or framework rules, but should not weaken these standards without an explicit technical reason.

---

## Table of Contents

  - [1. Purpose](#1-purpose)
- [2. Standard hierarchy](#2-standard-hierarchy)
- [3. Variables and scope](#3-variables-and-scope)
  - [3.1 Use `const` by default](#31-use-const-by-default)
    - [Why](#why)
  - [3.2 Do not confuse `const` with immutability](#32-do-not-confuse-const-with-immutability)
  - [3.3 Minimise variable scope](#33-minimise-variable-scope)
  - [3.4 Never create accidental globals](#34-never-create-accidental-globals)
- [4. Equality and coercion](#4-equality-and-coercion)
  - [4.1 Use strict equality](#41-use-strict-equality)
  - [4.2 Be deliberate about truthiness](#42-be-deliberate-about-truthiness)
- [5. Strings](#5-strings)
  - [5.1 Prefer template literals for interpolation](#51-prefer-template-literals-for-interpolation)
  - [5.2 Do not confuse interpolation with escaping](#52-do-not-confuse-interpolation-with-escaping)
  - [5.3 Preserve localisation contracts](#53-preserve-localisation-contracts)
- [6. Functions and callbacks](#6-functions-and-callbacks)
  - [6.1 Prefer arrow functions for local callbacks](#61-prefer-arrow-functions-for-local-callbacks)
  - [6.2 Use normal functions when function semantics require them](#62-use-normal-functions-when-function-semantics-require-them)
  - [6.3 Prefer named functions for significant operations](#63-prefer-named-functions-for-significant-operations)
  - [6.4 Prefer default parameters when `undefined` means "use the default"](#64-prefer-default-parameters-when-undefined-means-use-the-default)
  - [6.5 Prefer rest parameters over `arguments`](#65-prefer-rest-parameters-over-arguments)
- [7. Collections](#7-collections)
  - [7.1 Choose the array operation by intention](#71-choose-the-array-operation-by-intention)
  - [7.2 Prefer `map` over `forEach + push` for transformations](#72-prefer-map-over-foreach-push-for-transformations)
  - [7.3 Use `for...of` for procedural iteration](#73-use-forof-for-procedural-iteration)
  - [7.4 Avoid indexed loops when the index has no meaning](#74-avoid-indexed-loops-when-the-index-has-no-meaning)
  - [7.5 Do not use `for...in` for arrays](#75-do-not-use-forin-for-arrays)
  - [7.6 Use `Array.isArray` for array checks](#76-use-arrayisarray-for-array-checks)
  - [7.7 Do not use `reduce` simply because it is concise](#77-do-not-use-reduce-simply-because-it-is-concise)
- [8. Objects and plain-data transformations](#8-objects-and-plain-data-transformations)
  - [8.1 Use property shorthand](#81-use-property-shorthand)
  - [8.2 Prefer object spread for clear shallow composition](#82-prefer-object-spread-for-clear-shallow-composition)
  - [8.3 Understand spread semantics](#83-understand-spread-semantics)
  - [8.4 Prefer explicit precedence](#84-prefer-explicit-precedence)
  - [8.5 Preserve external contract field names](#85-preserve-external-contract-field-names)
- [9. Destructuring](#9-destructuring)
- [10. Optional chaining and nullish coalescing](#10-optional-chaining-and-nullish-coalescing)
  - [10.1 Use optional chaining for safe property access](#101-use-optional-chaining-for-safe-property-access)
  - [10.2 Do not use optional chaining to hide required data](#102-do-not-use-optional-chaining-to-hide-required-data)
  - [10.3 Use `??` for `null` / `undefined` defaults](#103-use-for-null-undefined-defaults)
- [11. Immutability and mutation](#11-immutability-and-mutation)
  - [11.1 Prefer pure transformations for plain data](#111-prefer-pure-transformations-for-plain-data)
  - [11.2 Mutation is not universally forbidden](#112-mutation-is-not-universally-forbidden)
- [12. Classes](#12-classes)
  - [12.1 Do not introduce classes merely to group functions](#121-do-not-introduce-classes-merely-to-group-functions)
  - [12.2 Prefer private implementation details](#122-prefer-private-implementation-details)
  - [12.3 Prefer composition over deep inheritance hierarchies](#123-prefer-composition-over-deep-inheritance-hierarchies)
- [13. Asynchronous code](#13-asynchronous-code)
  - [13.1 Prefer `async` / `await` for orchestration](#131-prefer-async-await-for-orchestration)
  - [13.2 Do not serialise independent operations unnecessarily](#132-do-not-serialise-independent-operations-unnecessarily)
  - [13.3 Await deliberately](#133-await-deliberately)
  - [13.4 Do not wrap promises unnecessarily](#134-do-not-wrap-promises-unnecessarily)
  - [13.5 Handle async errors at the correct boundary](#135-handle-async-errors-at-the-correct-boundary)
- [14. Error handling](#14-error-handling)
  - [14.1 Throw `Error` objects](#141-throw-error-objects)
  - [14.2 Preserve the original failure when adding context](#142-preserve-the-original-failure-when-adding-context)
  - [14.3 Do not use exceptions for expected branching](#143-do-not-use-exceptions-for-expected-branching)
- [15. Modules](#15-modules)
  - [15.1 Prefer explicit module boundaries](#151-prefer-explicit-module-boundaries)
  - [15.2 Avoid modules with hidden initialisation side effects](#152-avoid-modules-with-hidden-initialisation-side-effects)
  - [15.3 Export the smallest useful public surface](#153-export-the-smallest-useful-public-surface)
- [16. Runtime compatibility](#16-runtime-compatibility)
- [17. Control-flow expressions](#17-control-flow-expressions)
  - [17.1 Avoid clever short-circuit statements for important behaviour](#171-avoid-clever-short-circuit-statements-for-important-behaviour)
  - [17.2 Keep ternaries simple](#172-keep-ternaries-simple)
- [18. Modern syntax should improve readability, not compress code](#18-modern-syntax-should-improve-readability-not-compress-code)
- [19. Native prototypes](#19-native-prototypes)
- [20. Regular expressions](#20-regular-expressions)
- [21. Dates and time](#21-dates-and-time)
- [22. Numbers](#22-numbers)
- [23. Data boundaries](#23-data-boundaries)
- [24. Security-sensitive JavaScript](#24-security-sensitive-javascript)
- [25. Comments and JSDoc](#25-comments-and-jsdoc)
- [26. Tooling](#26-tooling)
- [27. Testing implications](#27-testing-implications)
- [28. Project-specific exceptions](#28-project-specific-exceptions)
- [29. Pull-request checklist](#29-pull-request-checklist)
  - [Variables](#variables)
  - [Functions](#functions)
  - [Conditions](#conditions)
  - [Collections](#collections)
  - [Objects](#objects)
  - [Async](#async)
  - [Runtime](#runtime)
  - [Readability](#readability)
- [30. Rule of thumb](#30-rule-of-thumb)
- [31. Final principle](#31-final-principle)

---

## 1. Purpose

This document defines the JavaScript-specific standards that extend the **ECM Core Software Development Standards**.

The Core Standards define the department's general engineering principles: readable code, focused functions, clear control flow, meaningful naming, limited duplication, deliberate error handling, top-to-bottom readability, and continuous small improvements.

This guide does **not** redefine those principles.

Instead, it defines how those principles should normally be expressed using modern JavaScript.

> **Core Standards define how we design code. This guide defines how we express those standards in JavaScript ES6+.**

Project-specific guides may extend this document where a framework, runtime, browser baseline, legacy architecture, or external contract requires additional rules.

---

# 2. Standard hierarchy

```text
ECM Core Software Development Standards
                ↓
ECM JavaScript ES6+ Standards
                ↓
Framework / project standards
React / Node.js / ERMIS / other application standards
```

When rules conflict:

1. Preserve correctness and external contracts.
2. Follow project/runtime compatibility requirements.
3. Follow this JavaScript standard.
4. Follow local stylistic preferences only where the standards are silent.

A project-specific exception should be documented rather than silently establishing a second coding style.

---

# 3. Variables and scope

## 3.1 Use `const` by default

Prefer `const` whenever the binding will not be reassigned.

```js
const user = getUser();
const totalPrice = calculateTotal(items);
const activeUsers = users.filter((user) => user.isActive);
```

Use `let` only when reassignment is part of the algorithm.

```js
let retryCount = 0;

while (retryCount < MAX_RETRY_COUNT) {
  retryCount += 1;
}
```

Do not use `var` in new code.

```js
// Avoid
var userName = getUserName();

// Prefer
const userName = getUserName();
```

### Why

`const` and `let` are block-scoped and make mutation intent explicit.

> Default to `const`. Reassignment must be a deliberate choice.

---

## 3.2 Do not confuse `const` with immutability

`const` prevents reassignment of the binding. It does not make an object immutable.

```js
const user = {
  name: "Maria",
};

user.name = "Anna"; // valid JavaScript
```

Where mutation is not required, prefer creating new plain-data values instead.

```js
const updatedUser = {
  ...user,
  name: "Anna",
};
```

Do not force immutability onto APIs whose contract is intentionally stateful.

---

## 3.3 Minimise variable scope

Declare values as close as practical to where they are used.

```js
function calculateInvoice(invoice) {
  const subtotal = calculateSubtotal(invoice.items);
  const tax = calculateTax(subtotal);

  return subtotal + tax;
}
```

Avoid declaring variables at a broader scope merely because they might be needed later.

---

## 3.4 Never create accidental globals

Do not assign undeclared variables.

```js
// Avoid
currentUser = getCurrentUser();

// Prefer
const currentUser = getCurrentUser();
```

Do not add values to `window`, `globalThis`, or native prototypes unless the project explicitly defines such behaviour as part of an application-level API.

---

# 4. Equality and coercion

## 4.1 Use strict equality

Use:

```js
===
!==
```

Do not introduce loose equality in normal application code.

```js
// Avoid
if (userId == selectedId) {
}

// Prefer
if (userId === selectedId) {
}
```

If coercion is required, perform it explicitly.

```js
Number(userId) === selectedId;
String(statusCode) === expectedStatusCode;
```

This makes the conversion part of the visible contract.

---

## 4.2 Be deliberate about truthiness

Use truthy/falsy checks when the business rule genuinely concerns presence or truthiness.

```js
if (!user) {
  return;
}
```

Use explicit comparisons when different falsy values have different meanings.

```js
if (items.length === 0) {
  return;
}

if (retryCount === 0) {
  initialiseRetry();
}
```

Do not mechanically write:

```js
value === true
```

when `value` is already known to be boolean.

---

# 5. Strings

## 5.1 Prefer template literals for interpolation

```js
const message = `Order ${orderId} was created for ${userName}.`;
```

Prefer this over concatenation:

```js
const message =
  "Order " + orderId + " was created for " + userName + ".";
```

Template literals make boundaries between literal text and values clearer.

---

## 5.2 Do not confuse interpolation with escaping

Template literals do not make HTML, SQL, URLs, shell commands, or other contexts safe.

```js
const html = `<div>${userInput}</div>`;
```

The example above is unsafe when `userInput` is untrusted and rendered as HTML.

Use the appropriate escaping, encoding, parameterisation, or framework API for the target context.

---

## 5.3 Preserve localisation contracts

If translated strings use placeholders controlled by the localisation system, do not replace them with code-owned interpolation if doing so prevents translators from controlling word order or formatting.

---

# 6. Functions and callbacks

The Core Standards define function design. JavaScript adds language-specific choices about syntax and `this`.

## 6.1 Prefer arrow functions for local callbacks

```js
const activeUsers = users.filter((user) => user.isActive);

items.forEach((item) => {
  processItem(item);
});
```

Arrow functions are especially useful when lexical `this` is intended.

---

## 6.2 Use normal functions when function semantics require them

Do not use an arrow function when the function intentionally requires:

- dynamic `this`;
- its own `arguments`;
- constructor behaviour;
- a framework lifecycle or inheritance mechanism requiring ordinary function semantics.

```js
function createUser(name) {
  return {
    name,
  };
}
```

> Choose function syntax based on semantics, not fashion.

---

## 6.3 Prefer named functions for significant operations

Do not hide important business behaviour inside long anonymous callbacks.

```js
// Harder to understand
orders
  .filter((order) => {
    // many lines of eligibility logic
  })
  .map((order) => {
    // many lines of mapping logic
  });
```

Prefer:

```js
const eligibleOrders = orders.filter(isOrderEligible);
const orderSummaries = eligibleOrders.map(toOrderSummary);
```

The names reveal the business steps.

---

## 6.4 Prefer default parameters when `undefined` means "use the default"

```js
function createPagination(pageSize = 20) {
  // ...
}
```

Remember that default parameters do not replace `null`.

```js
createPagination(null); // pageSize is null
```

Use `??` when both `null` and `undefined` should trigger the fallback.

---

## 6.5 Prefer rest parameters over `arguments`

```js
function calculateTotal(...values) {
  return values.reduce((sum, value) => sum + value, 0);
}
```

Rest parameters are explicit and produce a real array.

---

# 7. Collections

## 7.1 Choose the array operation by intention

| Intention | Preferred construct |
| --- | --- |
| Transform each item | `map` |
| Keep matching items | `filter` |
| Find one item | `find` |
| Check whether any match | `some` |
| Check whether all match | `every` |
| Perform a side effect for each item | `forEach` |
| Iterate with `break`, `continue`, or sequential `await` | `for...of` |
| Accumulate into one value | `reduce` when it improves clarity |

Examples:

```js
const userNames = users.map((user) => user.name);

const activeUsers = users.filter((user) => user.isActive);

const selectedUser = users.find((user) => user.id === selectedUserId);

const hasAdministrator = users.some((user) => user.role === "admin");

const allUsersVerified = users.every((user) => user.isVerified);
```

---

## 7.2 Prefer `map` over `forEach + push` for transformations

Avoid:

```js
const ids = [];

users.forEach((user) => {
  ids.push(user.id);
});
```

Prefer:

```js
const ids = users.map((user) => user.id);
```

The second version communicates that the operation is a transformation.

---

## 7.3 Use `for...of` for procedural iteration

```js
for (const order of orders) {
  if (order.isCancelled) {
    continue;
  }

  await processOrder(order);
}
```

Use it when control flow is part of the algorithm.

---

## 7.4 Avoid indexed loops when the index has no meaning

Avoid:

```js
for (let index = 0; index < users.length; index += 1) {
  processUser(users[index]);
}
```

Prefer:

```js
for (const user of users) {
  processUser(user);
}
```

Keep indexed loops when the index itself is meaningful.

---

## 7.5 Do not use `for...in` for arrays

`for...in` enumerates property names.

```js
// Avoid
for (const index in users) {
  processUser(users[index]);
}
```

Use `for...of` or an appropriate array method.

---

## 7.6 Use `Array.isArray` for array checks

```js
if (Array.isArray(value)) {
  // ...
}
```

Prefer it over:

```js
value instanceof Array;
```

---

## 7.7 Do not use `reduce` simply because it is concise

Avoid turning straightforward operations into accumulator puzzles.

```js
// Technically valid, but unnecessarily complex
const activeUserNames = users.reduce((result, user) => {
  if (user.isActive) {
    result.push(user.name);
  }

  return result;
}, []);
```

Prefer the clearer pipeline:

```js
const activeUserNames = users
  .filter((user) => user.isActive)
  .map((user) => user.name);
```

---

# 8. Objects and plain-data transformations

## 8.1 Use property shorthand

```js
const request = {
  userId,
  orderId,
  status,
};
```

Avoid repeating identical key/value names.

---

## 8.2 Prefer object spread for clear shallow composition

```js
const updatedUser = {
  ...user,
  isActive: false,
};
```

This is generally clearer for plain data than hidden mutation.

---

## 8.3 Understand spread semantics

Object and array spread are shallow.

```js
const copy = {
  ...original,
};

copy.address === original.address; // true
```

Do not treat spread as a deep clone.

Avoid:

```js
JSON.parse(JSON.stringify(value));
```

as a general-purpose deep-cloning mechanism.

Use an approved cloning strategy only when a genuine deep clone is required.

---

## 8.4 Prefer explicit precedence

Later object properties overwrite earlier ones.

```js
const request = {
  ...defaults,
  ...options,
  userId,
};
```

In this example, `userId` intentionally wins.

Order spreads so that precedence is obvious.

---

## 8.5 Preserve external contract field names

Application naming conventions do not override API, database, message, or vendor contracts.

```js
const applicationId = response.ApplicationId;

const request = {
  ApplicationId: applicationId,
};
```

Map external naming into local naming at boundaries where useful, but do not silently change contracts.

---

# 9. Destructuring

Use destructuring when it reduces repetition or makes the required inputs obvious.

```js
const { firstName, lastName } = user;

const displayName = `${firstName} ${lastName}`;
```

Function parameters may also use destructuring when the object is small and well understood.

```js
function createDisplayName({ firstName, lastName }) {
  return `${firstName} ${lastName}`;
}
```

Avoid over-destructuring large objects or extracting values that are used only once when doing so makes the origin less clear.

---

# 10. Optional chaining and nullish coalescing

## 10.1 Use optional chaining for safe property access

```js
const userName = response?.user?.profile?.name;
```

Prefer it to repetitive existence chains whose only purpose is safe access.

---

## 10.2 Do not use optional chaining to hide required data

This is a design smell:

```js
const countryCode =
  order?.customer?.profile?.address?.country?.code;
```

when the operation cannot work without a country code.

If the data is required, validate it at the appropriate boundary and fail clearly.

Optional chaining is for legitimately optional data, not for suppressing contract violations.

---

## 10.3 Use `??` for `null` / `undefined` defaults

```js
const pageSize = config.pageSize ?? DEFAULT_PAGE_SIZE;
```

`??` preserves intentional falsy values such as `0`, `false`, and `""`.

Do not automatically replace `||` with `??`; they express different rules.

```js
value || fallback; // fallback for any falsy value
value ?? fallback; // fallback only for null or undefined
```

Choose the operator that matches the domain rule.

---

# 11. Immutability and mutation

## 11.1 Prefer pure transformations for plain data

Avoid unexpected mutation:

```js
function prepareUser(user) {
  user.displayName = createDisplayName(user);
  return user;
}
```

Prefer:

```js
function prepareUser(user) {
  return {
    ...user,
    displayName: createDisplayName(user),
  };
}
```

---

## 11.2 Mutation is not universally forbidden

Mutation can be appropriate when:

- a framework API is intentionally stateful;
- performance-sensitive code has demonstrated a need;
- a collection is being built locally and never exposed in an intermediate state;
- the mutation is explicit in the function's contract.

The standard is to avoid **surprising shared mutation**, not to ban assignment.

---

# 12. Classes

## 12.1 Do not introduce classes merely to group functions

Prefer modules and plain functions when no object identity or persistent state is needed.

```js
export function calculateTax(order) {
  // ...
}

export function calculateTotal(order) {
  // ...
}
```

A class is appropriate when the domain genuinely benefits from encapsulated state, lifecycle, identity, or polymorphic behaviour.

---

## 12.2 Prefer private implementation details

Where the project runtime supports it and the class design warrants it, keep implementation details private.

Do not expose members merely so another module can reach into the internals.

---

## 12.3 Prefer composition over deep inheritance hierarchies

Avoid inheritance trees created for code reuse alone.

Composition usually makes dependencies and behaviour more explicit.

---

# 13. Asynchronous code

## 13.1 Prefer `async` / `await` for orchestration

```js
async function loadDashboard(userId) {
  const user = await getUser(userId);
  const orders = await getOrders(userId);

  return {
    user,
    orders,
  };
}
```

This is normally easier to read than deeply nested promise chains.

---

## 13.2 Do not serialise independent operations unnecessarily

Avoid:

```js
const user = await getUser(userId);
const permissions = await getPermissions(userId);
```

when the operations are independent.

Prefer:

```js
const [user, permissions] = await Promise.all([
  getUser(userId),
  getPermissions(userId),
]);
```

Do this only when concurrent execution is semantically safe.

---

## 13.3 Await deliberately

Do not fire promises accidentally.

```js
// Risky if the promise must complete
saveOrder(order);

// Explicit
await saveOrder(order);
```

When intentionally starting work without awaiting it, make that decision obvious and ensure rejection is handled according to the project's error-handling strategy.

---

## 13.4 Do not wrap promises unnecessarily

Avoid:

```js
return new Promise((resolve, reject) => {
  getUser(userId)
    .then(resolve)
    .catch(reject);
});
```

Prefer:

```js
return getUser(userId);
```

---

## 13.5 Handle async errors at the correct boundary

Do not add `try/catch` around every `await`.

Catch an error where there is enough context to:

- recover;
- translate it into a meaningful domain error;
- add useful diagnostic context;
- present an appropriate user-facing result.

Otherwise, allow the error to propagate.

---

# 14. Error handling

The Core Error Handling standards apply.

JavaScript-specific additions:

## 14.1 Throw `Error` objects

Avoid:

```js
throw "Unable to load user";
```

Prefer:

```js
throw new Error("Unable to load user");
```

Project-specific error classes may be used where they add real handling value.

---

## 14.2 Preserve the original failure when adding context

Where supported by the project's runtime:

```js
throw new Error("Unable to submit order", {
  cause: error,
});
```

Do not destroy useful diagnostic information merely to replace it with a generic message.

---

## 14.3 Do not use exceptions for expected branching

Do not throw and catch errors as a replacement for ordinary conditions.

Expected business outcomes should normally be represented explicitly.

---

# 15. Modules

## 15.1 Prefer explicit module boundaries

For modern module-based projects, use ES modules.

```js
export function calculateTotal(items) {
  // ...
}
```

```js
import { calculateTotal } from "./calculate-total.js";
```

Project-specific standards may retain another module system for compatibility.

---

## 15.2 Avoid modules with hidden initialisation side effects

A module import should not unexpectedly:

- mutate global state;
- register unrelated behaviour;
- send network requests;
- modify native prototypes.

If a module intentionally performs setup, make that purpose obvious from the module and API design.

---

## 15.3 Export the smallest useful public surface

Do not export helpers merely because another file could theoretically use them.

Keep implementation details private to the module until they form part of a real contract.

---

# 16. Runtime compatibility

Modern syntax and modern runtime APIs are different concerns.

A transpiler may transform syntax while not providing every runtime API.

Examples of runtime features that require compatibility awareness include:

```text
Promise
fetch
Array.prototype.includes
Array.prototype.findLast
Object.entries
Object.fromEntries
structuredClone
URL
Intl
```

Before introducing a runtime API outside the established project baseline:

1. Check the supported browser/runtime matrix.
2. Check the build target.
3. Check whether a polyfill is provided.
4. Add compatibility tests where necessary.

> Never assume that "Babel/transpilation handles it" means every modern runtime API is available.

---

# 17. Control-flow expressions

The Core Standards already define guard clauses, clear conditions, and top-to-bottom readability.

JavaScript-specific guidance:

## 17.1 Avoid clever short-circuit statements for important behaviour

Avoid:

```js
isActive && hasPermission && processOrder();
```

Prefer:

```js
if (isActive && hasPermission) {
  processOrder();
}
```

Important application behaviour should read like control flow, not an expression puzzle.

---

## 17.2 Keep ternaries simple

Good:

```js
const statusLabel = isActive ? "Active" : "Inactive";
```

Avoid nested ternaries for multi-step branching.

---

# 18. Modern syntax should improve readability, not compress code

This may be valid:

```js
const ids =
  response?.items?.filter((item) => item.isActive).map((item) => item.id) ?? [];
```

But intermediate values may better communicate a business workflow:

```js
const items = response?.items ?? [];
const activeItems = items.filter((item) => item.isActive);
const ids = activeItems.map((item) => item.id);
```

Choose the representation that makes intent easiest to understand.

> Fewer lines are not automatically cleaner code.

---

# 19. Native prototypes

Do not extend native prototypes in application code.

Avoid:

```js
Array.prototype.unique = function () {
  // ...
};
```

Prefer standalone functions:

```js
function getUniqueValues(values) {
  return [...new Set(values)];
}
```

Prototype extensions create global behaviour, possible collisions, and hidden dependencies.

---

# 20. Regular expressions

Use a regular expression when it is genuinely the clearest solution.

For non-trivial patterns:

- give the regex a descriptive name;
- explain unusual business rules where the pattern alone cannot communicate them;
- add focused tests;
- avoid rebuilding a mature parser with a single unreadable expression.

```js
const POSTCODE_PATTERN = /^[A-Z0-9 ]+$/i;
```

Do not optimise for a one-line regex at the expense of maintainability.

---

# 21. Dates and time

JavaScript date/time logic is easy to misuse.

Standards:

- make timezone assumptions explicit;
- distinguish instants from calendar dates;
- do not parse ambiguous date strings;
- do not manually manipulate date strings when an approved date utility should own the rule;
- include units in duration variable names where useful (`timeoutMs`, `durationSeconds`);
- preserve backend/API date contracts at boundaries.

Project-specific guides should define the approved date/time library and serialisation format.

---

# 22. Numbers

Use `Number.isNaN` when checking specifically for `NaN`.

```js
if (Number.isNaN(result)) {
  // ...
}
```

Prefer `Number.isFinite` when a value must be a finite number.

Do not rely on implicit numeric conversion when the source may be a string.

```js
const pageNumber = Number(rawPageNumber);

if (!Number.isInteger(pageNumber)) {
  // handle invalid value
}
```

---

# 23. Data boundaries

JavaScript has no compile-time guarantee that external data matches assumptions.

Treat these as untrusted boundaries:

- HTTP responses;
- browser storage;
- query strings;
- user input;
- messages/events;
- third-party SDK data;
- parsed JSON.

Validate data where a malformed value could cause incorrect business behaviour or security issues.

Do not spread unchecked external data deep into the application and assume its shape.

---

# 24. Security-sensitive JavaScript

Coding style does not replace security controls.

General rules:

- do not insert untrusted strings into raw HTML;
- avoid `eval` and `new Function`;
- do not build executable code from user input;
- use parameterised APIs rather than string-building commands/queries;
- validate URLs and redirects when values are externally controlled;
- do not expose secrets in browser JavaScript;
- use the framework/platform's safe DOM APIs;
- treat data from local storage as untrusted input.

Security-specific project standards take precedence where stricter.

---

# 25. Comments and JSDoc

The Core Comments standard applies.

Use JSDoc where it adds information that the function signature or name does not express, particularly for:

- public APIs;
- non-obvious external contracts;
- JavaScript consumed by tooling requiring JSDoc;
- important constraints;
- deprecation guidance.

Avoid documenting the obvious.

```js
/**
 * Returns the order total in cents.
 */
function calculateOrderTotal(order) {
  // ...
}
```

Do not use JSDoc as a substitute for clear naming and focused functions.

---

# 26. Tooling

Projects should automate mechanical style decisions.

At minimum, the JavaScript toolchain should normally provide:

- automated formatting;
- linting;
- unit-test execution;
- production build validation.

For modern JavaScript projects, tools such as Prettier and ESLint may be used, but the exact tool is a project-level decision.

Code review should focus primarily on:

- correctness;
- behaviour;
- architecture;
- readability;
- maintainability;
- testing;
- security;

rather than hand-enforcing whitespace and punctuation.

---

# 27. Testing implications

New JavaScript should be written so that important behaviour can be tested without excessive setup.

Prefer:

- pure business functions;
- explicit dependencies;
- clear inputs and outputs;
- small boundary adapters;
- deterministic transformations.

Avoid hidden dependencies on:

- globals;
- current time;
- random values;
- browser state;
- implicit singleton mutation;

unless those dependencies are intentionally isolated behind an abstraction.

---

# 28. Project-specific exceptions

A project may need to restrict modern JavaScript because of:

- legacy framework semantics;
- browser support;
- embedded runtimes;
- module-system limitations;
- vendor APIs;
- existing localisation behaviour;
- backend contracts.

The project guide should document those exceptions explicitly.

Example:

```text
JavaScript standard:
Use arrow functions for ordinary callbacks.

Project exception:
Framework lifecycle methods must use ordinary functions because the
framework supplies dynamic `this`.
```

This preserves one department standard while allowing technically necessary project behaviour.

---

# 29. Pull-request checklist

## Variables

- [ ] No new `var`.
- [ ] `const` is the default.
- [ ] `let` is used only when reassignment is required.
- [ ] No accidental globals were introduced.

## Functions

- [ ] Function syntax matches the required `this` / `arguments` semantics.
- [ ] Significant callback logic is extracted when naming improves clarity.
- [ ] Default and rest parameters are used appropriately.

## Conditions

- [ ] Comparisons normally use `===` / `!==`.
- [ ] Truthiness is used only when its semantics are intended.
- [ ] `?.` is not hiding required data.
- [ ] `??` is used only for nullish fallback semantics.

## Collections

- [ ] Array methods communicate the operation's intention.
- [ ] `map` is used for transformations rather than `forEach + push`.
- [ ] `for...in` is not used for arrays.
- [ ] `reduce` is used only when it remains clear.

## Objects

- [ ] Plain-data transformations avoid surprising mutation.
- [ ] Spread is not mistaken for deep cloning.
- [ ] Object-spread precedence is intentional.
- [ ] External field names/contracts remain intact.

## Async

- [ ] Promises are awaited or intentionally handled.
- [ ] Independent async operations are not needlessly serialised.
- [ ] Promises are not wrapped unnecessarily.
- [ ] Errors are handled at a meaningful boundary.

## Runtime

- [ ] New runtime APIs are supported by the project's target environments.
- [ ] Transpilation has not been mistaken for polyfilling.

## Readability

- [ ] Modern syntax has improved clarity rather than merely shortened code.
- [ ] Important business behaviour remains easy to debug and read top-to-bottom.

---

# 30. Rule of thumb

Prefer modern JavaScript that makes intent explicit:

```text
const / let
strict equality
template literals
arrow callbacks
native array methods
for...of
destructuring
object/property shorthand
object and array spread
optional chaining
nullish coalescing
default parameters
rest parameters
async / await
ES modules where the project architecture supports them
```

while remaining deliberate about:

```text
this semantics
mutation
runtime compatibility
external contracts
localisation
error boundaries
security
framework-specific behaviour
```

---

# 31. Final principle

Modern JavaScript is not a goal by itself.

> **Use modern language features when they make intent clearer, state and scope safer, and behaviour easier to understand. Do not use modern syntax merely to make code shorter or more fashionable.**

The Core Standards remain the governing design principles. This guide defines their normal JavaScript expression.
