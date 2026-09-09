# ECM Software development guide

## Table of Contents

- [ECM Coding standards](#standards)
  - [Naming conventions](#naming-conventions)
    - [Naming Conventions Based on Purpose and Data Type](#naming-conventions-based-on-purpose-and-data-type)
    - [Collections](#collections)
    - [Numbers and Quantities](#numbers-and-quantities)
    - [Functions and Methods](#functions-and-methods)
    - [Event Handlers](#event-handlers)
    - [Identifiers](#identifiers)
  - [Functions](#functions)
    - [Functions Should Do One Thing](#functions-should-do-one-thing)
    - [Keep Functions Small](#keep-functions-small)
    - [Use Descriptive Function Names](#use-descriptive-function-names)
    - [Keep One Level of Abstraction per Function](#keep-one-level-of-abstraction-per-function)
    - [Prefer Fewer Function Arguments](#prefer-fewer-function-arguments)
    - [Avoid Boolean Flag Arguments When They Select Behaviour](#avoid-boolean-flag-arguments-when-they-select-behaviour)
    - [Avoid Unexpected Side Effects](#avoid-unexpected-side-effects)
    - [Separate Commands from Queries](#separate-commands-from-queries)
    - [Function Checklist](#function-checklist)
  - [Conditionals and Control Flow](#conditionals-and-control-flow)
    - [Keep Conditions Simple and Intention-Revealing](#keep-conditions-simple-and-intention-revealing)
    - [Prefer Positive Conditions](#prefer-positive-conditions)
    - [Use Guard Clauses](#use-guard-clauses)
    - [Make Guard Clauses Meaningful](#make-guard-clauses-meaningful)
    - [Avoid Unnecessary `else` Statements](#avoid-unnecessary-else-statements)
    - [Choose the Appropriate Conditional Structure](#choose-the-appropriate-conditional-structure)
    - [Avoid Long `if / else if` Chains](#avoid-long-if-else-if-chains)
    - [Use Ternary Operators for Simple Decisions](#use-ternary-operators-for-simple-decisions)
    - [Avoid Assignment Inside Conditions](#avoid-assignment-inside-conditions)
    - [Be Explicit About Comparisons](#be-explicit-about-comparisons)
    - [Avoid Clever Control Flow](#avoid-clever-control-flow)
    - [Conditional and Control Flow Checklist](#conditional-and-control-flow-checklist)
  - [Comments](#comments)
    - [Explain Why, Not What](#explain-why-not-what)
    - [Prefer Clear Code Over Explanatory Comments](#prefer-clear-code-over-explanatory-comments)
    - [Keep Comments Accurate](#keep-comments-accurate)
    - [Do Not Keep Commented-Out Code](#do-not-keep-commented-out-code)
    - [Use TODO Comments Carefully](#use-todo-comments-carefully)
    - [Comment Checklist](#comment-checklist)
  - [Formatting & Code Structure](#formatting-code-structure)
    - [Use Consistent Formatting](#use-consistent-formatting)
    - [Keep Related Code Together](#keep-related-code-together)
    - [Separate Different Concepts](#separate-different-concepts)
    - [Keep Files Focused](#keep-files-focused)
    - [Avoid Excessively Long Lines](#avoid-excessively-long-lines)
    - [Use Automated Formatting and Linting](#use-automated-formatting-and-linting)
    - [Formatting & Code Structure Checklist](#formatting-code-structure-checklist)
  - [Magic Values](#magic-values)
    - [Avoid Unexplained Values](#avoid-unexplained-values)
  - [Error Handling](#error-handling)
    - [Do Not Ignore Errors](#do-not-ignore-errors)
    - [Keep Error Handling Clear](#keep-error-handling-clear)
    - [Provide Meaningful Errors](#provide-meaningful-errors)
    - [Avoid Ambiguous Error Values](#avoid-ambiguous-error-values)
    - [Do Not Use Exceptions for Normal Control Flow](#do-not-use-exceptions-for-normal-control-flow)
    - [Error Handling Checklist](#error-handling-checklist)
  - [Duplication / DRY](#duplication-dry)
    - [Avoid Duplicating Business Logic](#avoid-duplicating-business-logic)
    - [Do Not Remove Duplication Blindly](#do-not-remove-duplication-blindly)
    - [DRY Checklist](#dry-checklist)
  - [Top-to-Bottom Readability](#top-to-bottom-readability)
    - [Lead With Intent](#lead-with-intent)
  - [The Boy Scout Rule](#the-boy-scout-rule)
    - [Keep Improvements Relevant](#keep-improvements-relevant)

---

<a id="standards"></a>

## ECM Coding standards

<a id="coding-standards"></a>

### Coding Standards

Coding standards are a foundational element of professional software development, providing a shared framework that ensures code is consistent, readable, and maintainable across individuals and teams. In environments where multiple developers contribute to the same codebase, the absence of clear standards often leads to inconsistency, misunderstandings, and increased difficulty in maintaining or extending the system. By establishing agreed-upon conventions for naming, structure, formatting, and design practices, coding standards reduce cognitive load, improve collaboration, and enable developers to focus on solving problems rather than deciphering code. Ultimately, they serve not only as guidelines for writing code, but as a mechanism for promoting clarity, quality, and long-term sustainability in software systems.

<a id="naming-conventions"></a>

#### Naming conventions

**Variables**

`camelCase` - Variables & Functions (Javascript, Typescript, C# etc.)

    let userName;
    function calculateTotalPrice() {}

`snake_case` - Used in variables for Python and in Databases

```
user_name  =  "John"
total_price  =  100
```

`PascalCase` - Classes and Types

```
class  UserAccount {}
class  PaymentService {}
```

`UPPER_CASE` - Constants and Immutable values

```
const  MAX_RETRY_COUNT  =  3;
const  API_BASE_URL  =  "https://api.example.com";
```

`kebab-case` - File names and URLs

```
user-profile-page
api-endpoints.md
```

Boolean conventions
Valid boolean variables begin with the following prefixes:

- `is` → state
- `has` → possession
- `can` → capability
- `should` → decision logic
- `was` → past state

Examples with State (`is`)

```
const  isActive  =  true;
const  isLoggedIn  =  false;
const  isPaymentProcessed  =  true;
```

Examples with Possession (`has`)

```
const  hasPermission  =  true;
const  hasChildren  =  false;
```

Examples with Capability (`can`)

```
const  canEdit  =  true;
const  canRetry  =  false;
```

Examples with Decision (`should`)

```
const  shouldRetry  =  true;
const  shouldDisplayError  =  false;
```

Examples with Past State (`was`)

```
const  wasPaid  =  true;
const  wasSent  =  false;
```

❌Examples to avoid

```
const  active  =  true;
const  flag  =  false;
const  status  =  true;
if (!isNotUserActive) { ... }
```

These force the reader to guess:

- Active _what_?
- Flag _for what_?
- Status _means what?_
- `!isNotActive` means _not not user active_, which enforces extra mental decoding

✔ Better

```
const  isUserActive  =  true;
const  hasValidSession  =  false;
const  isPaymentSuccessful  =  true;
if (!isUserActive) { ... }
```

Boolean Functions Should Read Like Questions

✔ Good examples

```
function  isUserValid(user) { ... }
function  hasAccess(user) { ... }
function  canProcessPayment(order) { ... }
```

❌ Bad example
`function  userCheck(user) { ... }`

<a id="naming-conventions-based-on-purpose-and-data-type"></a>

#### Naming Conventions Based on Purpose and Data Type

A good name should communicate what something represents, what kind of value it contains, or what action it performs without requiring the reader to inspect its implementation.

<a id="collections"></a>

### Collections

Collections should generally use **plural nouns**, indicating that the variable contains multiple instances of something.

- `users` → collection of users
- `orders` → collection of orders
- `activeUsers` → collection of active users
- `pendingPayments` → collection of pending payments

```js
const users = [];
const orders = [];
const activeUsers = [];
const pendingPayments = [];
```

Prefer:

```js
const users = [];
```

over:

```js
const userArray = [];
```

The type or implementation usually already tells us that the value is an array. Include the data structure in the name when the structure itself communicates important information about its behaviour:

- `Map` / `Dictionary` → key-value lookup: `usersById`, `productById`
- `Set` → unique values: `uniqueEmails`, `selectedIds`
- `Queue` → FIFO processing: `requestQueue`, `jobQueue`
- `Stack` → LIFO history: `undoStack`, `navigationStack`
- `Tree` → hierarchy: `categoryTree`, `folderTree`
- `Graph` → relationships: `dependencyGraph`, `socialGraph`

```js
const requestQueue = [];
const undoStack = [];
const usersById = new Map();
const uniqueEmails = new Set();
```

**Convention:** `plural noun` → collection of entities

---

<a id="numbers-and-quantities"></a>

### Numbers and Quantities

Numeric variable names should communicate **what the number represents or measures**.

- `Count` → number of items
  - `userCount`
  - `retryCount`
- `Total` → accumulated amount
  - `orderTotal`
  - `totalPrice`
- `Index` → position within a collection
  - `userIndex`
  - `selectedIndex`
- `Percentage` / `Rate` → ratio or percentage
  - `completionPercentage`
  - `taxRate`
- `Limit` / `Max` / `Min` → boundary or constraint
  - `pageLimit`
  - `maxRetries`
  - `minPasswordLength`

Avoid vague numeric names:

```js
const number = 5;
const value = 100;
const n = 3;
```

Prefer:

```js
const userCount = 5;
const totalPrice = 100;
const retryCount = 3;
```

---

##### Units of Measurement

When a numeric value represents a measurement, include the unit in the name when the unit cannot otherwise be clearly inferred.

- `Ms` → milliseconds
- `Seconds` → seconds
- `Minutes` → minutes
- `Px` → pixels
- `Km` → kilometres
- `Bytes` → bytes

```js
const timeoutMs = 5000;
const durationSeconds = 30;
const distanceKm = 15;
const fileSizeBytes = 2048;
const heightPx = 500;
```

Avoid:

```js
const timeout = 5000;
```

The reader cannot immediately determine whether `5000` represents milliseconds, seconds, or another unit.

Prefer:

```js
const timeoutMs = 5000;
```

**Convention:** `<meaning><unit>` → `timeoutMs`, `heightPx`, `durationMinutes`

---

##### Dates and Times

Date and time variables should communicate what point in time or period they represent.

- `At` → specific point in time
  - `createdAt`
  - `updatedAt`
  - `expiresAt`
- `Date` → calendar date
  - `startDate`
  - `endDate`
  - `birthDate`
- `Duration` → amount of elapsed time
  - `sessionDuration`
  - `requestDuration`

```js
const createdAt = new Date();
const updatedAt = new Date();
const expiresAt = new Date();

const startDate = new Date();
const endDate = new Date();
```

---

<a id="functions-and-methods"></a>

### Functions and Methods

Function and method names should normally describe an **action** and therefore begin with a verb.

- `get` → retrieve something
- `create` → create something
- `calculate` → calculate a value
- `send` → send something
- `validate` → validate something
- `delete` → delete something

```js
getUser();
createOrder();
calculateTotal();
sendEmail();
validatePayment();
deleteAccount();
```

Avoid vague or noun-only function names:

```js
function user() {}
function data() {}
function payment() {}
```

Prefer:

```js
function getUser() {}
function loadData() {}
function processPayment() {}
```

**General rule:**

> Nouns describe data; verbs describe behaviour.

Functions that return booleans should follow the boolean naming conventions:

```js
isUserValid();
hasPermission();
canEditOrder();
shouldRetry();
```

---

<a id="event-handlers"></a>

### Event Handlers

Functions responsible for handling events should use the `handle` prefix followed by the event or action being handled.

- `handleSubmit`
- `handleClick`
- `handleUserSelection`
- `handlePaymentSuccess`

```js
function handleSubmit() {
  // Handle form submission
}

function handleUserSelection() {
  // Handle user selection
}
```

In frontend applications, particularly React, a useful convention is:

- `onX` → event or callback exposed by a component
- `handleX` → internal function responsible for handling that event

```jsx
function UserForm({ onSubmit }) {
  const handleSubmit = () => {
    // Process the form
    onSubmit();
  };

  return <button onClick={handleSubmit}>Submit</button>;
}
```

---

<a id="identifiers"></a>

### Identifiers

Identifiers should include the entity or concept they identify when the surrounding context does not already make this obvious.

- `userId`
- `orderId`
- `transactionId`
- `paymentId`

```js
const userId = 123;
const orderId = 456;
const transactionId = "abc123";
```

Avoid overly generic identifiers when multiple entities are involved:

```js
const id = 123;
```

Prefer:

```js
const userId = 123;
```

Using `id` is acceptable within a small scope where the entity being identified is already unambiguous.

---

##### Constants

Constants representing fixed configuration values, limits, or application-wide rules should use descriptive `UPPER_CASE` names.

```js
const MAX_RETRY_COUNT = 3;
const DEFAULT_PAGE_SIZE = 20;
const SESSION_TIMEOUT_MS = 30_000;
```

The name should explain **what the constant controls or represents**.

Avoid:

```js
const MAX = 3;
const LIMIT = 20;
const TIMEOUT = 30_000;
```

Prefer:

```js
const MAX_RETRY_COUNT = 3;
const DEFAULT_PAGE_SIZE = 20;
const SESSION_TIMEOUT_MS = 30_000;
```

In JavaScript and TypeScript, not every variable declared using `const` should use `UPPER_CASE`.

```js
const user = getUser();
const totalPrice = calculateTotal();

const MAX_RETRY_COUNT = 3;
```

`const` prevents the variable binding from being reassigned. It does not necessarily mean that the variable represents a fixed application-level constant.

---

##### Naming Convention Summary

| Category        | Convention               | Examples                                    |
| --------------- | ------------------------ | ------------------------------------------- |
| Entity          | Singular noun            | `user`, `order`, `payment`                  |
| Collection      | Plural noun              | `users`, `orders`, `payments`               |
| Boolean         | Question-like            | `isActive`, `hasAccess`, `canEdit`          |
| Quantity        | Meaning + quantity       | `userCount`, `totalPrice`, `retryLimit`     |
| Unit            | Meaning + unit           | `timeoutMs`, `distanceKm`, `fileSizeBytes`  |
| Date / Time     | Temporal meaning         | `createdAt`, `startDate`, `expiresAt`       |
| Function        | Verb + subject           | `createUser`, `calculateTotal`, `sendEmail` |
| Predicate       | Boolean convention       | `isValid`, `hasPermission`, `canEdit`       |
| Event Handler   | `handle` + event         | `handleSubmit`, `handleClick`               |
| Callback / Prop | `on` + event             | `onSubmit`, `onUserSelect`                  |
| Identifier      | Entity + `Id`            | `userId`, `orderId`, `paymentId`            |
| Constant        | Descriptive `UPPER_CASE` | `MAX_RETRY_COUNT`, `DEFAULT_PAGE_SIZE`      |

<a id="functions"></a>

### Functions

Functions are one of the primary building blocks of readable and maintainable software. A well-designed function should communicate its purpose clearly, perform a focused piece of work, and hide unnecessary implementation details from the code that uses it. When functions become too large, perform several unrelated operations, or introduce unexpected side effects, they become harder to understand, test, reuse, and safely modify.

The objective is not to make every function as short as possible. The objective is to ensure that each function has a **clear and focused responsibility**.

<a id="functions-should-do-one-thing"></a>

#### Functions Should Do One Thing

A function should have one clear responsibility and perform that responsibility well.

❌ Avoid functions that combine several unrelated responsibilities:

```js
function processUserRegistration(user) {
  validateUser(user);

  saveUserToDatabase(user);

  sendWelcomeEmail(user);

  writeAuditLog(user);
}
```

Although the function above is short, it coordinates several distinct operations. Whether this is appropriate depends on its level of abstraction. If `processUserRegistration` represents the high-level registration workflow, coordinating those operations may be its single responsibility. The implementation details of those operations, however, should remain in focused functions. See [Keep One Level of Abstraction per Function](#keep-one-level-of-abstraction-per-function).

> A useful question is: **Can I describe what this function does without using the word “and”?**

If the answer is no, the function may be doing too much.

---

<a id="keep-functions-small"></a>

#### Keep Functions Small

Small functions are generally easier to read, understand, test, and modify. Large functions often indicate that several responsibilities or levels of abstraction have been combined.

There is no universal maximum number of lines that makes a function clean. Function size should be treated as a **signal**, not a strict rule.

When a function becomes difficult to understand without scrolling, contains several logical sections, or requires comments to explain different stages of its behaviour, consider extracting those sections into appropriately named functions.

❌ Avoid:

```js
function createOrder(order) {
  // validate customer
  // validate products
  // calculate subtotal
  // calculate tax
  // apply discounts
  // save order
  // send confirmation
  // update inventory
}
```

✔ Prefer expressing the workflow through smaller operations:

```js
function createOrder(order) {
  validateOrder(order);

  calculateOrderTotal(order);

  saveOrder(order);

  updateInventory(order);

  sendOrderConfirmation(order);
}
```

The high-level function now communicates the workflow without exposing every implementation detail.

---

<a id="use-descriptive-function-names"></a>

#### Use Descriptive Function Names

A function name should explain the action being performed. A developer should normally be able to understand the purpose of a function without reading its implementation.

❌ Avoid vague names:

```js
function process(data) {}

function handle(value) {}

function execute() {}

function doStuff() {}
```

✔ Prefer intention-revealing names:

```js
function processPayment(payment) {}

function validateEmailAddress(emailAddress) {}

function calculateOrderTotal(order) {}

function sendPasswordResetEmail(user) {}
```

Longer descriptive names are usually preferable to short ambiguous names.

Function names should begin with a verb. See [Functions and Methods](#functions-and-methods) for verb prefixes and boolean naming conventions.

> A function name should describe **what the function does**, not force the reader to discover it from the implementation.

---

<a id="keep-one-level-of-abstraction-per-function"></a>

#### Keep One Level of Abstraction per Function

A function should avoid mixing high-level business operations with low-level implementation details.

❌ Avoid:

```js
function registerUser(user) {
  validateUser(user);

  database.users.insert({
    id: generateId(),

    name: user.name,

    email: user.email,
  });

  sendWelcomeEmail(user);
}
```

The function moves from a high-level operation (`validateUser`) to low-level database implementation details and then back to another high-level operation (`sendWelcomeEmail`).

✔ Prefer:

```js
function registerUser(user) {
  validateUser(user);

  saveUser(user);

  sendWelcomeEmail(user);
}
```

The details of persistence belong inside `saveUser`:

```js
function saveUser(user) {
  database.users.insert({
    id: generateId(),

    name: user.name,

    email: user.email,
  });
}
```

This allows the high-level function to describe the business workflow while lower-level functions contain the implementation details.

---

<a id="prefer-fewer-function-arguments"></a>

#### Prefer Fewer Function Arguments

Functions with fewer arguments are generally easier to understand and use correctly.

As the number of parameters increases, developers must remember their meaning, order, and relationships.

✔ Easy to understand:

```js
function getUser(userId) {}
```

❌ Harder to understand:

```js
function createUser(name, email, age, country, role, department) {}
```

When several values naturally belong together, consider passing an object:

```js
function createUser(userDetails) {}

createUser({
  name: "John",

  email: "john@example.com",

  age: 30,

  country: "Cyprus",

  role: "Developer",

  department: "Engineering",
});
```

This makes the call easier to read and reduces dependency on positional argument order.

Do not introduce an object merely to satisfy an arbitrary argument-count rule. Use one when the values form a meaningful concept or when it materially improves clarity.

---

<a id="avoid-boolean-flag-arguments-when-they-select-behaviour"></a>

#### Avoid Boolean Flag Arguments When They Select Behaviour

A boolean argument can be a warning sign when it causes a function to perform different operations depending on whether the value is `true` or `false`.

❌ Avoid unclear calls:

```js
processOrder(order, true);
```

The meaning of `true` is impossible to understand without inspecting the function definition.

A flag may also indicate that the function has more than one responsibility:

```js
function processOrder(order, isExpress) {
  if (isExpress) {
    processExpressOrder(order);
  } else {
    processStandardOrder(order);
  }
}
```

✔ When the behaviours are genuinely different, prefer explicit operations:

```js
processExpressOrder(order);

processStandardOrder(order);
```

Not every boolean parameter is inherently wrong. A boolean that represents genuine data can be valid. The concern is primarily with flags that switch a function between distinct behaviours.

---

<a id="avoid-unexpected-side-effects"></a>

#### Avoid Unexpected Side Effects

A function should not unexpectedly modify state or perform additional operations that are not implied by its name.

❌ Avoid:

```js
function validateUser(user) {
  const isValid = Boolean(user.email);

  saveUser(user);

  return isValid;
}
```

A developer calling `validateUser()` would reasonably expect validation, not database persistence.

✔ Prefer:

```js
function validateUser(user) {
  return Boolean(user.email);
}
```

If the user needs to be saved, make that operation explicit:

```js
if (validateUser(user)) {
  saveUser(user);
}
```

> A function should behave in a way that is consistent with the expectations created by its name.

---

<a id="separate-commands-from-queries"></a>

#### Separate Commands from Queries

Where practical, distinguish between functions that **return information** and functions that **change state**.

A query answers a question:

```js
function getUser(userId) {
  return users.find((user) => user.id === userId);
}
```

A command performs an action:

```js
function deactivateUser(user) {
  user.isActive = false;
}
```

Avoid functions whose purpose is unclear because they both return information and unexpectedly modify state.

```js
function getUser(userId) {
  const user = findUser(userId);

  user.lastAccessedAt = new Date();

  return user;
}
```

There are legitimate cases where an operation must both return a value and change state, particularly around persistence, APIs, and framework behaviour. The important rule is that such behaviour should be intentional and clear from the function's contract.

---

<a id="function-checklist"></a>

#### Function Checklist

Before considering a function complete, ask:

- Does the function have one clear responsibility?

- Does its name clearly describe what it does?

- Is it small enough to understand easily?

- Does it operate at a consistent level of abstraction?

- Are its arguments necessary and easy to understand?

- Does it avoid unclear boolean flag arguments?

- Does it avoid unexpected side effects?

- Are complex conditions expressed using meaningful names? See [Keep Conditions Simple and Intention-Revealing](#keep-conditions-simple-and-intention-revealing).

- Can deep nesting be simplified with guard clauses? See [Use Guard Clauses](#use-guard-clauses).

- Is duplicated business logic extracted appropriately? See [Duplication / DRY](#duplication-dry).

- Can the function be understood without relying on explanatory comments?

- Does the surrounding code read naturally from high-level intent to implementation detail? See [Top-to-Bottom Readability](#top-to-bottom-readability).

The goal is not to satisfy these rules mechanically. They are signals that help developers identify functions that may be difficult to understand or maintain. The ultimate standard is whether another developer can read the function, understand its intent, and modify it safely.

<a id="conditionals-and-control-flow"></a>

### Conditionals and Control Flow

Conditionals and control flow determine how a program makes decisions

and moves between different execution paths. Although conditional

statements are fundamental to programming, poorly structured conditions

can quickly make code difficult to understand, test, and maintain. The

goal is not simply to make a condition work, but to express the

underlying business rule as clearly as possible.

Clean control flow should allow a developer to understand \*\*why a branch

is taken\*\* without having to mentally decode a complex expression.

<a id="keep-conditions-simple-and-intention-revealing"></a>

#### Keep Conditions Simple and Intention-Revealing

A conditional should be easy to understand at a glance. When a condition represents a meaningful business rule, give it a name that communicates the **intention** before the implementation details.

❌ Avoid complex conditions:

```js
if (
  user &&
  user.isActive &&
  !user.isSuspended &&
  user.hasPermission &&
  order.status !== "Cancelled"
) {
  processOrder(order);
}
```

✔ Prefer extracting the meaning of the condition:

```js
const canProcessOrder =
  user?.isActive &&
  !user.isSuspended &&
  user.hasPermission &&
  order.status !== "Cancelled";

if (canProcessOrder) {
  processOrder(order);
}
```

For rules that are reused or contain more substantial logic, extract a function:

```js
function canUserPurchase(user) {
  return user.age >= 18 && user.accountStatus === "Active" && !user.isBlocked;
}

if (canUserPurchase(user)) {
  allowPurchase();
}
```

The reader can now understand the **business meaning** of the condition without interpreting each individual boolean expression.

---

<a id="prefer-positive-conditions"></a>

#### Prefer Positive Conditions

Positive conditions are generally easier to understand than negative

ones.

❌ Avoid unnecessary negative logic:

```js
if (!isNotActive) {
  activateAccount();
}
```

✔ Prefer:

```js
if (isActive) {
  activateAccount();
}
```

Double negatives require additional mental processing and increase the

chance of misunderstanding a condition.

---

<a id="use-guard-clauses"></a>

#### Use Guard Clauses

Deeply nested conditions hide the execution path and bury the **happy path** — the normal successful flow of the function. A **guard clause** exits early when a required condition is not satisfied, keeping the main behaviour clear and unindented.

❌ Avoid unnecessary nesting:

```js
function processOrder(user, order) {
  if (user) {
    if (user.isActive) {
      if (order) {
        if (order.status === "Pending") {
          processPayment(order);
        }
      }
    }
  }
}
```

✔ Prefer guard clauses:

```js
function processOrder(user, order) {
  if (!user) return;

  if (!user.isActive) return;

  if (!order) return;

  if (order.status !== "Pending") return;

  processPayment(order);
}
```

Each additional nesting level forces the reader to remember more context. Guard clauses handle exceptional cases first, leaving the primary behaviour immediately visible.

---

<a id="make-guard-clauses-meaningful"></a>

#### Make Guard Clauses Meaningful

When validation rules become more complex, extract them into functions

with meaningful names.

```js
function submitOrder(user, order) {
  if (!isEligibleUser(user)) return;

  if (!hasOrderItems(order)) return;

  processOrder(order);
}

function isEligibleUser(user) {
  return user?.isActive && !user.isSuspended;
}

function hasOrderItems(order) {
  return order.items.length > 0;
}
```

This allows the high-level function to describe the workflow without

exposing every implementation detail.

---

<a id="avoid-unnecessary-else-statements"></a>

#### Avoid Unnecessary `else` Statements

When an `if` branch returns, throws, or otherwise terminates execution,

an `else` block is usually unnecessary.

❌ Avoid:

```js
function getDiscount(user) {
  if (user.isPremium) {
    return 0.2;
  } else {
    return 0.1;
  }
}
```

✔ Prefer:

```js
function getDiscount(user) {
  if (user.isPremium) {
    return 0.2;
  }

  return 0.1;
}
```

This reduces nesting and makes the control flow easier to scan.

---

<a id="choose-the-appropriate-conditional-structure"></a>

#### Choose the Appropriate Conditional Structure

Different types of decisions are clearer with different control-flow

structures.

Use `if` when evaluating boolean expressions or a small number of

related conditions:

```js
if (isUserAdmin) {
  showAdminPanel();
}
```

Use `if / else if` when conditions are different expressions:

```js
if (score >= 90) {
  return "Excellent";
}

if (score >= 70) {
  return "Good";
}

return "Needs Improvement";
```

Use `switch` when comparing the same value against several discrete

possibilities:

```js
switch (order.status) {
  case "Pending":
    processPendingOrder(order);

    break;

  case "Completed":
    displayReceipt(order);

    break;

  case "Cancelled":
    displayCancellation(order);

    break;

  default:
    handleUnknownStatus(order.status);
}
```

If a `switch` has more than eight cases, consider replacing it with a

map lookup. Maps scale better, are easier to scan, and keep dispatch

logic separate from the behaviour being selected.

❌ Be cautious with:

```js
function getCountryLabel(countryCode) {
  switch (countryCode) {
    case "GB":
      return "United Kingdom";

    case "US":
      return "United States";

    case "DE":
      return "Germany";

    case "FR":
      return "France";

    case "ES":
      return "Spain";

    case "IT":
      return "Italy";

    case "NL":
      return "Netherlands";

    case "BE":
      return "Belgium";

    case "PT":
      return "Portugal";

    // ... additional cases

    default:
      return "Unknown";
  }
}
```

✔ Prefer:

```js
const countryLabels = {
  GB: "United Kingdom",
  US: "United States",
  DE: "Germany",
  FR: "France",
  ES: "Spain",
  IT: "Italy",
  NL: "Netherlands",
  BE: "Belgium",
  PT: "Portugal",
  // ... additional entries
};

function getCountryLabel(countryCode) {
  return countryLabels[countryCode] ?? "Unknown";
}
```

The goal is not to prefer one syntax universally. Choose the structure

that communicates the decision most clearly.

---

<a id="avoid-long-if-else-if-chains"></a>

#### Avoid Long `if / else if` Chains

Long conditional chains can indicate that behaviour is being selected

manually in too many places.

❌ Be cautious with:

```js
if (paymentType === "card") {
  processCardPayment();
} else if (paymentType === "paypal") {
  processPayPalPayment();
} else if (paymentType === "bank") {
  processBankTransfer();
} else if (paymentType === "cash") {
  processCashPayment();
}
```

For simple cases, a `switch` may improve readability:

```js
switch (paymentType) {
  case "card":
    processCardPayment();

    break;

  case "paypal":
    processPayPalPayment();

    break;

  case "bank":
    processBankTransfer();

    break;

  case "cash":
    processCashPayment();

    break;
}
```

If this type of branching appears repeatedly throughout the application,

it may indicate that the design should eventually be refactored so that

behaviour is encapsulated rather than repeatedly selected through

conditionals.

The rule is:

> Repeated or continuously growing conditional logic is a signal to
> review the design.

---

<a id="use-ternary-operators-for-simple-decisions"></a>

#### Use Ternary Operators for Simple Decisions

Ternary operators can make simple value selection concise and readable.

✔ Good:

```js
const displayName = user.name ? user.name : "Anonymous";
```

Or, when appropriate:

```js
const accountLabel = isActive ? "Active" : "Inactive";
```

Avoid using ternaries for complex branching.

❌ Avoid:

```js
const message = isActive
  ? hasPermission
    ? isVerified
      ? "Access granted"
      : "Verification required"
    : "Permission denied"
  : "Account inactive";
```

Nested ternaries are difficult to scan and should generally be replaced

with clearer control flow.

---

<a id="avoid-assignment-inside-conditions"></a>

#### Avoid Assignment Inside Conditions

Assignments inside conditions are easy to misread and can introduce

subtle bugs.

❌ Avoid:

```js
if ((user = getUser())) {
  displayUser(user);
}
```

✔ Prefer:

```js
const user = getUser();

if (user) {
  displayUser(user);
}
```

Separating the assignment from the decision makes both operations

explicit.

---

<a id="be-explicit-about-comparisons"></a>

#### Be Explicit About Comparisons

Prefer conditions that communicate exactly what is being checked.

Instead of relying on truthiness when the distinction matters:

```js
if (users.length) {
  displayUsers(users);
}
```

consider:

```js
if (users.length > 0) {
  displayUsers(users);
}
```

The second version explicitly communicates that the code is checking

whether the collection contains items.

This does not mean truthy/falsy checks should never be used. They are

appropriate when the intended rule genuinely is about truthiness:

```js
if (!user) {
  return;
}
```

The important principle is to avoid relying on implicit behaviour when

doing so makes the business rule less clear.

---

<a id="avoid-clever-control-flow"></a>

#### Avoid Clever Control Flow

Code should optimise for readability rather than the smallest possible

number of characters.

❌ Avoid:

```js
isActive && hasPermission && processUser();
```

When the expression represents important application behaviour, prefer

an explicit conditional:

```js
if (isActive && hasPermission) {
  processUser();
}
```

Concise syntax is not automatically cleaner syntax.

---

<a id="conditional-and-control-flow-checklist"></a>

#### Conditional and Control Flow Checklist

Before considering conditional logic complete, ask:

- Is the condition understandable without mentally decoding it?

- Does the condition communicate the underlying business rule?

- Have complex conditions been given meaningful names?

- Have unnecessary negative or double-negative conditions been

avoided?

- Is the code deeply nested?

- Could guard clauses make the main execution path clearer?

- Are unnecessary `else` blocks present?

- Is the chosen conditional structure appropriate for the decision?

- Are there long or repeated conditional chains that may indicate a

design problem?

- Are ternary operators limited to simple value selection?

- Are assignments kept separate from conditions?

- Are important comparisons explicit?

- Have business-significant magic values been given meaningful names? See [Magic Values](#magic-values).

- Is the happy path easy to identify?

- Does the control flow read naturally from top to bottom?

> **The goal of clean control flow is not to eliminate conditionals. It
> is to make every decision in the code easy to understand.**

<a id="comments"></a>

### Comments

Comments should be used carefully. Well-written code should communicate

**what it does** through meaningful names, small functions, and clear

structure. A comment should not be used to compensate for code that is

difficult to understand.

<a id="explain-why-not-what"></a>

#### Explain Why, Not What

Avoid comments that simply repeat what the code already says.

❌ Avoid:

```js
// Increment retry count

retryCount++;
```

The comment adds no information.

Comments are more valuable when they explain **why** something is

necessary.

✔ Better:

```js
// The external API allows a maximum of three retry attempts.

const MAX_RETRY_COUNT = 3;
```

---

<a id="prefer-clear-code-over-explanatory-comments"></a>

#### Prefer Clear Code Over Explanatory Comments

If a comment is required to explain what a piece of code does, first

consider whether the code itself can be improved.

❌ Avoid:

```js
// Check whether the user is active and has permission to edit

if (user.isActive && user.permissions.includes("edit")) {
  // ...
}
```

✔ Prefer:

```js
const canEdit = user.isActive && user.permissions.includes("edit");

if (canEdit) {
  // ...
}
```

The code now communicates its intention without requiring a comment.

---

<a id="keep-comments-accurate"></a>

#### Keep Comments Accurate

An outdated comment can be more harmful than no comment because it gives

the reader incorrect information.

When changing code, always check whether nearby comments are still

valid. If a comment no longer provides useful or accurate information,

update or remove it.

---

<a id="do-not-keep-commented-out-code"></a>

#### Do Not Keep Commented-Out Code

❌ Avoid:

```js
// const oldTotal = calculateOldTotal(order);

// processLegacyPayment(order);
```

Commented-out code creates noise and uncertainty. Source control systems

such as Git already preserve previous versions of the code, so obsolete

code should normally be deleted rather than commented out.

---

<a id="use-todo-comments-carefully"></a>

#### Use TODO Comments Carefully

`TODO` comments should describe a specific piece of work that still

needs to be completed.

✔ Good:

```js
// TODO: Replace temporary validation when the new API endpoint is available.
```

Avoid vague TODOs:

```js
// TODO: Fix this
```

Where possible, TODOs should also be tracked through the team's

issue-tracking system.

---

<a id="comment-checklist"></a>

#### Comment Checklist

Before adding a comment, ask:

- Can the code be made clearer instead?

- Does the comment explain **why**, rather than repeat **what**?

- Is the comment accurate and necessary?

- Am I commenting out code that should simply be deleted?

- Is the TODO specific and actionable?

> **Comments should clarify intent or context that the code itself
> cannot reasonably express. They should not be a substitute for writing
> clear code.**

<a id="formatting-code-structure"></a>

### Formatting & Code Structure

Consistent formatting and clear code structure make a codebase easier to

read, navigate, review, and maintain. Developers should be able to

understand the shape of a file quickly without first having to interpret

every individual line.

Formatting should not depend on personal preference. Teams should agree

on conventions and, where possible, enforce them automatically using

tools such as formatters and linters.

<a id="use-consistent-formatting"></a>

#### Use Consistent Formatting

Indentation, spacing, quotation styles, semicolons, and line breaks

should be consistent throughout the codebase.

❌ Avoid inconsistent formatting:

```js
function calculateTotal(items) {
  const total = items.reduce((sum, item) => sum + item.price, 0);

  return total;
}
```

✔ Prefer:

```js
function calculateTotal(items) {
  const total = items.reduce((sum, item) => sum + item.price, 0);

  return total;
}
```

Formatting rules should normally be automated rather than manually

enforced during code reviews.

---

<a id="keep-related-code-together"></a>

#### Keep Related Code Together

Code that belongs to the same concept should be located close together.

```js
const MAX_RETRY_COUNT = 3;

function retryRequest() {
  // ...
}

function hasReachedRetryLimit(retryCount) {
  return retryCount >= MAX_RETRY_COUNT;
}
```

Avoid scattering closely related logic across a file without a clear

reason. The reader should not need to repeatedly jump between unrelated

sections to understand a simple behaviour.

---

<a id="separate-different-concepts"></a>

#### Separate Different Concepts

Use whitespace to visually separate different steps or concepts.

❌ Harder to scan:

```js
function processOrder(order) {
  validateOrder(order);

  const total = calculateTotal(order.items);

  saveOrder(order);

  sendConfirmation(order);
}
```

✔ Clearer:

```js
function processOrder(order) {
  validateOrder(order);

  const total = calculateTotal(order.items);

  saveOrder(order);

  sendConfirmation(order);
}
```

Whitespace should communicate structure, not be added randomly.

---

<a id="keep-files-focused"></a>

#### Keep Files Focused

A file should have a clear purpose and contain closely related

functionality.

For example:

```text

user/
├── user-service.js
├── user-validation.js
└── user-mapper.js

```

Be cautious when a single file contains unrelated business logic,

utility functions, API calls, validation rules, and UI behaviour.

A large file is not automatically bad, but excessive size can be a

signal that the file has accumulated too many responsibilities.

---

<a id="avoid-excessively-long-lines"></a>

#### Avoid Excessively Long Lines

Long lines are harder to scan and often hide multiple concepts.

❌ Avoid:

```js
const canProcessOrder = user.isActive && !user.isSuspended && user.hasPermission && order.status === "Pending" && order.items.length > 0 && user.hasValidPaymentMethod;
```

✔ Prefer breaking long expressions across lines:

```js
const canProcessOrder =
  user.isActive &&
  !user.isSuspended &&
  user.hasPermission &&
  order.status === "Pending" &&
  order.items.length > 0 &&
  user.hasValidPaymentMethod;
```

The exact line-length limit should be agreed by the team and preferably

enforced by formatting tools.

---

<a id="use-automated-formatting-and-linting"></a>

#### Use Automated Formatting and Linting

Formatting should not consume significant development or code-review

time.

Tools such as **Prettier** and **ESLint** can automatically enforce many

agreed conventions in JavaScript and TypeScript projects.

The team should configure these tools once and apply the same

configuration consistently across the project.

> Code reviews should focus primarily on behaviour, design,
> maintainability, and correctness rather than debates about spaces,
> indentation, or semicolons.

---

<a id="formatting-code-structure-checklist"></a>

#### Formatting & Code Structure Checklist

Before considering a change complete, ask:

- Is the formatting consistent with the rest of the codebase?

- Is related code located together?

- Are different concepts visually separated where useful?

- Does each file have a clear purpose?

- Can the code be read naturally from high-level behaviour to implementation details? See [Top-to-Bottom Readability](#top-to-bottom-readability).

- Are any lines unnecessarily difficult to scan?

- Are formatting rules automated where possible?

> **Good formatting should make the structure of the code visible before
> the reader has to understand every implementation detail.**

<a id="magic-values"></a>

### Magic Values

A **magic value** is a number, string, or other literal whose meaning is

not immediately clear from the code. Business-significant values should

be given descriptive names so that the reader understands \*\*why the

value exists\*\*.

<a id="avoid-unexplained-values"></a>

#### Avoid Unexplained Values

❌ Avoid:

```js
if (retryCount >= 3) {
  stopRetrying();
}
```

The reader has to guess why `3` is important.

✔ Prefer:

```js
const MAX_RETRY_COUNT = 3;

if (retryCount >= MAX_RETRY_COUNT) {
  stopRetrying();
}
```

The rule is now explicit and can be changed in one place.

The same principle applies to meaningful strings:

```js
const ORDER_STATUS_CANCELLED = "cancelled";

if (order.status === ORDER_STATUS_CANCELLED) {
  preventPayment(order);
}
```

Not every literal needs to become a constant. Values such as `0` in

`items.length === 0` are usually self-explanatory. Extract a value when

giving it a name adds useful **business or technical meaning**.

> **If a reader has to ask "Why this value?", give the value a
> meaningful name.**

<a id="error-handling"></a>

### Error Handling

Errors are part of normal software operation and should be handled

deliberately. Good error handling keeps failure behaviour clear without

obscuring the main business logic.

<a id="do-not-ignore-errors"></a>

#### Do Not Ignore Errors

❌ Avoid:

```js
try {
  await saveOrder(order);
} catch (error) {
  // Do nothing
}
```

Silently swallowing an error makes failures difficult to diagnose and

may leave the application in an unexpected state.

Handle the error where the application has enough context to respond

appropriately, or allow it to propagate to a layer that does.

---

<a id="keep-error-handling-clear"></a>

#### Keep Error Handling Clear

Separate the successful workflow from failure handling where practical.

```js
async function submitOrder(order) {
  try {
    await processOrder(order);
  } catch (error) {
    handleOrderError(error);
  }
}
```

This keeps the primary operation easy to identify.

---

<a id="provide-meaningful-errors"></a>

#### Provide Meaningful Errors

Errors should communicate what failed and provide enough context for the

appropriate layer to diagnose or handle the problem.

❌ Avoid:

```js
throw new Error("Something went wrong");
```

✔ Prefer:

```js
throw new Error("Unable to process payment");
```

Do not expose sensitive internal information to end users. Technical

details should be logged or handled through the application's

error-reporting strategy where appropriate.

---

<a id="avoid-ambiguous-error-values"></a>

#### Avoid Ambiguous Error Values

Avoid using unexplained return values to represent failure.

❌ Avoid:

```js
return -1;
```

The caller must know that `-1` has a special meaning.

Prefer an explicit result, a well-defined error type, or an exception

according to the conventions of the application.

---

<a id="do-not-use-exceptions-for-normal-control-flow"></a>

#### Do Not Use Exceptions for Normal Control Flow

Exceptions should represent exceptional or failure conditions, not

ordinary decisions.

❌ Avoid:

```js
try {
  getUser();
} catch {
  createUser();
}
```

✔ Prefer explicit logic when the absence of a user is an expected case:

```js
const user = getUser();

if (!user) {
  createUser();
}
```

---

<a id="error-handling-checklist"></a>

#### Error Handling Checklist

- Is the error handled rather than silently ignored?

- Is the error message meaningful?

- Is the successful path still easy to understand?

- Are ambiguous error codes or values avoided?

- Are exceptions reserved for genuine failure conditions?

- Is sensitive information kept out of user-facing errors?

> **Handle failures explicitly, provide useful context, and keep error
> handling from obscuring the normal flow of the code.**

<a id="duplication-dry"></a>

### Duplication / DRY

**DRY (Don't Repeat Yourself)** means that important knowledge or

business rules should have a single, clear representation in the

codebase. Duplication becomes a problem when the same logic must be

changed in several places to keep the system consistent.

<a id="avoid-duplicating-business-logic"></a>

#### Avoid Duplicating Business Logic

❌ Avoid:

```js
const checkoutTotal = price + price * 0.19;

const invoiceTotal = price + price * 0.19;
```

✔ Prefer:

```js
const TAX_RATE = 0.19;

function calculatePriceWithTax(price) {
  return price + price * TAX_RATE;
}

const checkoutTotal = calculatePriceWithTax(price);

const invoiceTotal = calculatePriceWithTax(price);
```

If the tax rule changes, there is now one clear place to update it.

<a id="do-not-remove-duplication-blindly"></a>

#### Do Not Remove Duplication Blindly

Two pieces of code looking similar does not automatically mean they

represent the same concept. Creating an abstraction too early can couple

unrelated behaviours and make future changes harder.

> **Remove duplication when the repeated code represents the same
> knowledge or business rule---not simply because two blocks happen to
> look alike.**

<a id="dry-checklist"></a>

#### DRY Checklist

- Is the same business rule implemented in multiple places?

- Would a future change require updating several copies of the same

logic?

- Can the shared behaviour be given a clear, meaningful name?

- Does the abstraction represent one concept, or am I combining

unrelated code just because it looks similar?

<a id="top-to-bottom-readability"></a>

### Top-to-Bottom Readability

Code should be organised so that a reader can understand the **high-level intent first** and move progressively into implementation details. A function or file should tell a story rather than force the reader to assemble the workflow from scattered low-level operations. High-level business operations should not be unnecessarily mixed with low-level implementation details in the same section of code — see [Keep One Level of Abstraction per Function](#keep-one-level-of-abstraction-per-function).

<a id="lead-with-intent"></a>

#### Lead With Intent

Structure files and modules so the reader encounters high-level behaviour before implementation details.

❌ Harder to understand — low-level details appear before the workflow:

```js
function validateEmail(email) {
  // Validation details
}

function insertUserRecord(user) {
  // Persistence details
}

function registerUser(user) {
  validateEmail(user.email);

  insertUserRecord(user);

  sendWelcomeEmail(user);
}
```

✔ Prefer — high-level functions first, then their supporting details:

```js
function registerUser(user) {
  validateUser(user);

  saveUser(user);

  sendWelcomeEmail(user);
}

function validateUser(user) {
  // Validation details
}

function saveUser(user) {
  // Persistence details
}

function sendWelcomeEmail(user) {
  // Email details
}
```

Within a function, apply the same principle. See [Keep One Level of Abstraction per Function](#keep-one-level-of-abstraction-per-function).

The reader immediately understands the registration workflow and can inspect each implementation detail only when necessary.

> **Write code so that the reader can move from "what happens" to "how it happens" naturally from top to bottom.**

<a id="the-boy-scout-rule"></a>

### The Boy Scout Rule

The **Boy Scout Rule** applies a simple principle to software
development:

> **Leave the code cleaner than you found it.**

A codebase does not become difficult to maintain because of one large
decision alone. Quality often declines through many small compromises
that accumulate over time. Developers should therefore make small, safe improvements while working in existing code.

For example:

- Rename an unclear variable.

- Remove an obsolete comment.

- Simplify an unnecessarily complex condition.

- Remove unused code.

- Fix inconsistent formatting.

- Extract an obvious piece of duplicated logic.

<a id="keep-improvements-relevant"></a>

#### Keep Improvements Relevant

The Boy Scout Rule does not mean rewriting every file you touch. Large

unrelated refactors increase risk and make code reviews harder.

Improvements should be \*\*small, safe, relevant to the area being

changed, and supported by appropriate tests\*\*.

> **Every change is an opportunity to improve the codebase slightly
> without unnecessarily increasing the scope of the task.**
