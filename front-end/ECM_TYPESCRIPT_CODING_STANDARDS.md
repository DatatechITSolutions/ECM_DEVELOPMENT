# ECM TypeScript Coding Standards

**Parents:**  
1. ECM Core Software Development Standards  
2. ECM JavaScript Coding Standards — ES6+

**Applies to:** New TypeScript code and code that is materially modified  
**Primary goal:** Use TypeScript to make invalid states and incorrect interactions harder to represent.

---

## Table of Contents

  - [1. Purpose](#1-purpose)
- [2. The central TypeScript principle](#2-the-central-typescript-principle)
- [3. Compiler configuration](#3-compiler-configuration)
- [4. Prefer inference when the type is obvious](#4-prefer-inference-when-the-type-is-obvious)
- [5. Do not use `any` as a default escape hatch](#5-do-not-use-any-as-a-default-escape-hatch)
- [6. Prefer `unknown` for untrusted values](#6-prefer-unknown-for-untrusted-values)
- [7. Validate external data at runtime](#7-validate-external-data-at-runtime)
- [8. Avoid unsafe type assertions](#8-avoid-unsafe-type-assertions)
- [9. Prefer narrowing over assertions](#9-prefer-narrowing-over-assertions)
- [10. Model valid states with unions](#10-model-valid-states-with-unions)
- [11. Use exhaustive checking](#11-use-exhaustive-checking)
- [12. Prefer literal unions for closed sets of values](#12-prefer-literal-unions-for-closed-sets-of-values)
- [13. Enums](#13-enums)
- [14. `type` vs `interface`](#14-type-vs-interface)
- [15. Prefer domain types over primitive ambiguity](#15-prefer-domain-types-over-primitive-ambiguity)
- [16. Branded / opaque IDs when identifiers must not be mixed](#16-branded-opaque-ids-when-identifiers-must-not-be-mixed)
- [17. Null and undefined](#17-null-and-undefined)
- [18. Optional properties](#18-optional-properties)
- [19. Readonly data](#19-readonly-data)
- [20. Function return types](#20-function-return-types)
- [21. Async return types](#21-async-return-types)
- [22. Prefer explicit result types for expected failures](#22-prefer-explicit-result-types-for-expected-failures)
- [23. Generics](#23-generics)
- [24. Name generic parameters when it improves clarity](#24-name-generic-parameters-when-it-improves-clarity)
- [25. Utility types](#25-utility-types)
- [26. Use `satisfies` when you want validation without widening away useful inference](#26-use-satisfies-when-you-want-validation-without-widening-away-useful-inference)
- [27. Avoid over-engineered type programming](#27-avoid-over-engineered-type-programming)
- [28. SOLID in TypeScript](#28-solid-in-typescript)
- [29. S — Single Responsibility Principle](#29-s-single-responsibility-principle)
- [30. O — Open/Closed Principle](#30-o-openclosed-principle)
- [31. L — Liskov Substitution Principle](#31-l-liskov-substitution-principle)
- [32. I — Interface Segregation Principle](#32-i-interface-segregation-principle)
- [33. D — Dependency Inversion Principle](#33-d-dependency-inversion-principle)
- [34. Prefer capability-based dependencies](#34-prefer-capability-based-dependencies)
- [35. React + TypeScript](#35-react-typescript)
- [36. Type component props explicitly](#36-type-component-props-explicitly)
- [37. Prefer domain props over primitive prop collections](#37-prefer-domain-props-over-primitive-prop-collections)
- [38. Callback props should describe the event](#38-callback-props-should-describe-the-event)
- [39. Model mutually exclusive props with discriminated unions](#39-model-mutually-exclusive-props-with-discriminated-unions)
- [40. Avoid boolean-prop explosions](#40-avoid-boolean-prop-explosions)
- [41. Reuse native element props where appropriate](#41-reuse-native-element-props-where-appropriate)
- [42. Prefer `ReactNode` for renderable children](#42-prefer-reactnode-for-renderable-children)
- [43. Event handler types](#43-event-handler-types)
- [44. State should have the narrowest valid type](#44-state-should-have-the-narrowest-valid-type)
- [45. Model complex component state as a state machine / discriminated union](#45-model-complex-component-state-as-a-state-machine-discriminated-union)
- [46. Refs](#46-refs)
- [47. Context](#47-context)
- [48. Reducers](#48-reducers)
- [49. Custom hooks](#49-custom-hooks)
- [50. Generic React components](#50-generic-react-components)
- [51. Component SOLID](#51-component-solid)
  - [Single Responsibility](#single-responsibility)
  - [Open/Closed](#openclosed)
  - [Liskov Substitution](#liskov-substitution)
  - [Interface Segregation](#interface-segregation)
  - [Dependency Inversion](#dependency-inversion)
- [52. Do not mirror backend models blindly through the UI](#52-do-not-mirror-backend-models-blindly-through-the-ui)
- [53. Avoid duplicating source-of-truth types manually](#53-avoid-duplicating-source-of-truth-types-manually)
- [54. Third-party libraries](#54-third-party-libraries)
- [55. Error handling in TypeScript](#55-error-handling-in-typescript)
- [56. Maps, records, and dictionaries](#56-maps-records-and-dictionaries)
- [57. Arrays and readonly arrays](#57-arrays-and-readonly-arrays)
- [58. Tuples](#58-tuples)
- [59. Function overloads](#59-function-overloads)
- [60. Declaration merging and module augmentation](#60-declaration-merging-and-module-augmentation)
- [61. Type-only imports](#61-type-only-imports)
- [62. Avoid namespaces in module-based applications](#62-avoid-namespaces-in-module-based-applications)
- [63. Classes in TypeScript](#63-classes-in-typescript)
- [64. Access modifiers](#64-access-modifiers)
- [65. Dependency contracts should be narrow](#65-dependency-contracts-should-be-narrow)
- [66. Tests and types](#66-tests-and-types)
- [67. Type-level tests](#67-type-level-tests)
- [68. Avoid suppressions](#68-avoid-suppressions)
- [69. Naming](#69-naming)
- [70. Common suffixes](#70-common-suffixes)
- [71. DTOs and domain models](#71-dtos-and-domain-models)
- [72. Pull-request checklist](#72-pull-request-checklist)
  - [Safety](#safety)
  - [Modelling](#modelling)
  - [Functions](#functions)
  - [SOLID](#solid)
  - [React](#react)
  - [Maintainability](#maintainability)
- [73. Rule of thumb](#73-rule-of-thumb)
- [74. Final principle](#74-final-principle)

---

## 1. Purpose

TypeScript adds a static type system to JavaScript. Its value is not simply that variables can be annotated.

Used well, TypeScript allows the codebase to express:

- domain concepts;
- function contracts;
- valid and invalid states;
- component APIs;
- ownership boundaries;
- expected failure cases;
- constraints between values.

This document extends both the **Core Software Development Standards** and the **JavaScript ES6+ Standards**.

It intentionally does not repeat general standards for function size, naming, control flow, comments, DRY, error handling, or formatting.

> **JavaScript standards govern runtime code. TypeScript standards govern how we model and enforce contracts around that runtime code.**

---

# 2. The central TypeScript principle

Do not ask:

> "How can I make this compile?"

Ask:

> "What does the type system know, and how can the model make an invalid use difficult or impossible?"

TypeScript should reduce uncertainty.

Avoid escaping the type system merely to silence an error.

---

# 3. Compiler configuration

TypeScript projects should use strict type checking unless a documented legacy constraint prevents it.

Recommended baseline:

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitOverride": true,
    "noFallthroughCasesInSwitch": true
  }
}
```

The exact configuration is project-specific, but weakening strictness requires a deliberate reason.

> Compiler errors are design feedback, not obstacles to be bypassed.

For migrations, stricter settings may be introduced incrementally, but new modules should aim for the target standard.

---

# 4. Prefer inference when the type is obvious

Avoid unnecessary annotations:

```ts
const userName: string = "Maria";
const retryCount: number = 3;
const isActive: boolean = true;
```

Prefer:

```ts
const userName = "Maria";
const retryCount = 3;
const isActive = true;
```

Add explicit types where they define a meaningful contract.

```ts
function calculateTotal(items: OrderItem[]): number {
  // ...
}
```

Useful places for explicit types include:

- public function parameters;
- public return contracts where useful;
- exported APIs;
- component props;
- data boundaries;
- object shapes representing domain concepts;
- complex values where inference would hide intent.

---

# 5. Do not use `any` as a default escape hatch

Avoid:

```ts
function processResponse(response: any) {
  return response.data.user.name;
}
```

`any` disables meaningful checking and spreads uncertainty through the codebase.

When a value is genuinely unknown, use `unknown`.

```ts
function parseResponse(response: unknown) {
  // narrow or validate before use
}
```

Use `any` only when:

- integration with an untyped library makes it unavoidable;
- migration work requires a temporary escape hatch;
- the limitation is localised and documented.

Keep `any` at boundaries and prevent it from leaking through the application.

---

# 6. Prefer `unknown` for untrusted values

`unknown` says:

> "A value exists, but its type has not yet been established."

```ts
function getErrorMessage(error: unknown): string {
  if (error instanceof Error) {
    return error.message;
  }

  return "An unexpected error occurred";
}
```

Unlike `any`, `unknown` requires validation or narrowing before use.

This is especially appropriate for:

- caught exceptions;
- parsed JSON before validation;
- third-party callback values;
- loosely typed external SDKs;
- dynamic storage.

---

# 7. Validate external data at runtime

TypeScript types disappear at runtime.

This does **not** validate data:

```ts
const response = await fetch("/api/users/1");

const user = (await response.json()) as User;
```

The assertion merely tells TypeScript to trust the developer.

For important external boundaries, validate the value using:

- project-approved schema validation;
- explicit type guards;
- parsers/mappers.

Example:

```ts
type User = {
  id: string;
  name: string;
};

function isUser(value: unknown): value is User {
  if (typeof value !== "object" || value === null) {
    return false;
  }

  const candidate = value as Record<string, unknown>;

  return (
    typeof candidate.id === "string" &&
    typeof candidate.name === "string"
  );
}
```

For complex API models, prefer a schema-validation solution over hand-written guards throughout the codebase.

> Static typing protects code written against a contract. Runtime validation establishes whether external data actually satisfies that contract.

---

# 8. Avoid unsafe type assertions

Be suspicious of:

```ts
value as User
value as SomeType
value as unknown as SomeType
```

A type assertion should be used only when the developer genuinely knows something the compiler cannot infer.

Avoid double assertions:

```ts
value as unknown as User;
```

except in tightly controlled compatibility code.

If assertions are repeatedly required, investigate whether:

- the type model is wrong;
- the boundary is not validated;
- an abstraction exposes an overly broad type;
- a third-party definition needs wrapping;
- narrowing should be used instead.

---

# 9. Prefer narrowing over assertions

```ts
function formatValue(value: string | number): string {
  if (typeof value === "number") {
    return value.toFixed(2);
  }

  return value.trim();
}
```

Prefer type guards and discriminants that allow TypeScript to infer the correct branch.

Useful narrowing tools include:

```text
typeof
instanceof
Array.isArray
"in" checks
literal comparisons
custom type predicates
discriminated unions
```

---

# 10. Model valid states with unions

Avoid types that permit contradictory states.

```ts
// Weak model
type RequestState<T> = {
  isLoading: boolean;
  data?: T;
  error?: string;
};
```

This allows impossible combinations such as:

```ts
{
  isLoading: true,
  data: someData,
  error: "Failed"
}
```

Prefer a discriminated union:

```ts
type RequestState<T> =
  | {
      status: "idle";
    }
  | {
      status: "loading";
    }
  | {
      status: "success";
      data: T;
    }
  | {
      status: "error";
      error: string;
    };
```

Now each state carries only the data that is valid for that state.

> Prefer types that make invalid combinations impossible to construct.

---

# 11. Use exhaustive checking

For important discriminated unions, make missing cases visible to the compiler.

```ts
type PaymentStatus =
  | "pending"
  | "authorised"
  | "captured"
  | "cancelled";

function getPaymentLabel(status: PaymentStatus): string {
  switch (status) {
    case "pending":
      return "Pending";

    case "authorised":
      return "Authorised";

    case "captured":
      return "Captured";

    case "cancelled":
      return "Cancelled";

    default: {
      const exhaustiveCheck: never = status;
      return exhaustiveCheck;
    }
  }
}
```

If a new status is added but not handled, the exhaustive branch fails to compile.

Use this where missing a case would be meaningful.

---

# 12. Prefer literal unions for closed sets of values

For application-domain values, string literal unions are often clearer than arbitrary strings.

```ts
type OrderStatus =
  | "draft"
  | "submitted"
  | "approved"
  | "cancelled";
```

This prevents:

```ts
const status: OrderStatus = "aproved";
```

Do not use `string` when the domain actually defines a small closed set.

---

# 13. Enums

Do not introduce an enum automatically whenever a fixed set exists.

A literal union is often sufficient:

```ts
type UserRole = "admin" | "editor" | "viewer";
```

An enum can be appropriate when:

- the project deliberately standardises on it;
- runtime enumeration is useful;
- interoperability requires it;
- the values represent a meaningful runtime object.

Choose one project convention and apply it consistently.

---

# 14. `type` vs `interface`

Both are valid.

Prefer consistency over ideology.

A practical convention:

- use `type` for unions, intersections, aliases, mapped/conditional types, and most application models;
- use `interface` when declaration merging or explicit object-oriented extension is a meaningful part of the design.

Example:

```ts
type UserId = string;

type UserRole = "admin" | "editor";

type User = {
  id: UserId;
  role: UserRole;
};
```

Do not convert between `type` and `interface` merely for style.

---

# 15. Prefer domain types over primitive ambiguity

These signatures compile but communicate little:

```ts
function transferMoney(
  source: string,
  destination: string,
  amount: number,
): void {
  // ...
}
```

Prefer meaningful domain types where confusion would be costly:

```ts
type AccountId = string;

type Money = {
  amount: number;
  currency: Currency;
};

function transferMoney(
  sourceAccountId: AccountId,
  destinationAccountId: AccountId,
  money: Money,
): void {
  // ...
}
```

Do not wrap every primitive mechanically. Introduce domain types where they reduce ambiguity or invalid use.

---

# 16. Branded / opaque IDs when identifiers must not be mixed

In domains where several IDs share the same primitive representation, branding can prevent accidental interchange.

```ts
type UserId = string & {
  readonly __brand: "UserId";
};

type OrderId = string & {
  readonly __brand: "OrderId";
};
```

Then:

```ts
function loadOrder(orderId: OrderId) {
  // ...
}
```

A `UserId` cannot be passed accidentally.

Use branded types selectively. They add value at important domain boundaries but can create unnecessary complexity in simple applications.

---

# 17. Null and undefined

Use strict null checking.

Do not use non-null assertions as a normal coding technique.

Avoid:

```ts
const userName = user!.profile!.name;
```

Prefer:

- validating required data;
- optional chaining for genuinely optional data;
- narrowing;
- modelling state correctly.

```ts
if (!user) {
  return;
}

const userName = user.profile.name;
```

A `!` should mean:

> "The surrounding contract guarantees this even though TypeScript cannot see it."

It should not mean:

> "I want the compiler error to disappear."

---

# 18. Optional properties

Use optional properties only when absence is a valid state.

```ts
type UserProfile = {
  displayName: string;
  avatarUrl?: string;
};
```

Do not make fields optional simply because data is populated in stages.

If an object has multiple valid lifecycle states, consider modelling those states explicitly.

---

# 19. Readonly data

Use `readonly` when mutation is not part of a contract.

```ts
type User = {
  readonly id: string;
  name: string;
};
```

For function parameters that should not mutate collections:

```ts
function calculateTotal(items: readonly OrderItem[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}
```

This documents and enforces intent.

Do not mark everything readonly mechanically if the domain genuinely requires mutation.

---

# 20. Function return types

Inference is often sufficient for private implementation details.

For exported or important domain operations, explicit return types can prevent accidental contract changes.

```ts
export function createUser(
  input: CreateUserInput,
): User {
  // ...
}
```

Without the explicit return type, a later implementation change may unintentionally alter the exported structure.

Use explicit return types where the function forms a stable API boundary.

---

# 21. Async return types

Important async APIs should make their promise contract clear.

```ts
async function getUser(userId: UserId): Promise<User> {
  // ...
}
```

Do not hide the possibility of absence.

```ts
async function findUser(
  userId: UserId,
): Promise<User | undefined> {
  // ...
}
```

The type should communicate the real outcome.

---

# 22. Prefer explicit result types for expected failures

Not every expected business failure should be an exception.

Instead of:

```ts
function validatePayment(payment: Payment): void {
  if (!payment.cardNumber) {
    throw new Error("Invalid payment");
  }
}
```

an operation with expected validation outcomes may use:

```ts
type ValidationResult =
  | {
      isValid: true;
    }
  | {
      isValid: false;
      errors: readonly string[];
    };
```

Choose exceptions for exceptional failure and explicit result types when failure is a normal branch callers are expected to handle.

---

# 23. Generics

Use generics to express relationships between types.

Good:

```ts
function getById<T extends { id: string }>(
  items: readonly T[],
  id: string,
): T | undefined {
  return items.find((item) => item.id === id);
}
```

The generic says:

> "The returned item is the same specific type as the input collection."

Avoid generics that merely replace a concrete type with a meaningless letter.

```ts
// Adds no value
function logValue<T>(value: T): void {
  console.log(value);
}
```

Use a generic when it preserves information or constrains relationships.

---

# 24. Name generic parameters when it improves clarity

`T` is fine for a small, obvious generic.

For more complex code, descriptive names may be clearer:

```ts
type ApiResponse<TData> = {
  data: TData;
  status: number;
};

type Mapper<TSource, TDestination> = (
  source: TSource,
) => TDestination;
```

Do not make developers decode several unrelated `T`, `U`, `V`, and `K` parameters when descriptive names communicate the role better.

---

# 25. Utility types

Use built-in utility types when they express the contract clearly.

Examples:

```ts
Pick<T, K>
Omit<T, K>
Partial<T>
Required<T>
Readonly<T>
Record<K, V>
ReturnType<T>
Parameters<T>
Awaited<T>
```

Example:

```ts
type UserSummary = Pick<
  User,
  "id" | "name" | "role"
>;
```

Do not create large chains of utility types that are harder to understand than a named domain type.

If the derived type represents an important business concept, give it a meaningful name and consider defining it explicitly.

---

# 26. Use `satisfies` when you want validation without widening away useful inference

```ts
type RouteName = "home" | "users" | "settings";

const routes = {
  home: "/",
  users: "/users",
  settings: "/settings",
} satisfies Record<RouteName, string>;
```

This checks the object against the required shape while preserving useful information about the value itself.

Use `satisfies` when the goal is:

> "Verify this value conforms to a contract without replacing its inferred type."

---

# 27. Avoid over-engineered type programming

TypeScript can express extremely sophisticated compile-time logic.

That does not mean application code should.

Be cautious when a type requires:

- deeply nested conditional types;
- several levels of mapped types;
- recursive type transformations;
- obscure inference tricks;
- type-level parsing.

Complex types are justified when they protect a widely used API or library abstraction.

For ordinary business code, prefer explicit domain types that developers can understand quickly.

> Type-level cleverness can create the same readability problems as runtime cleverness.

---

# 28. SOLID in TypeScript

SOLID is not a checklist requiring classes.

The principles should guide dependency and contract design regardless of whether the implementation uses:

- functions;
- objects;
- classes;
- React components;
- services;
- modules.

---

# 29. S — Single Responsibility Principle

A module or abstraction should have one primary reason to change.

Avoid:

```ts
class UserService {
  validateUser(user: User) {
    // ...
  }

  saveUser(user: User) {
    // ...
  }

  sendWelcomeEmail(user: User) {
    // ...
  }

  generateUserReport(user: User) {
    // ...
  }
}
```

This abstraction mixes validation, persistence, communication, and reporting.

Prefer focused contracts:

```ts
type UserRepository = {
  save(user: User): Promise<void>;
};

type EmailService = {
  sendWelcomeEmail(user: User): Promise<void>;
};

type UserValidator = {
  validate(user: User): ValidationResult;
};
```

A higher-level use case can orchestrate them:

```ts
type RegisterUserDependencies = {
  userRepository: UserRepository;
  emailService: EmailService;
  userValidator: UserValidator;
};

function createRegisterUser(
  dependencies: RegisterUserDependencies,
) {
  return async function registerUser(
    user: User,
  ): Promise<void> {
    const validation =
      dependencies.userValidator.validate(user);

    if (!validation.isValid) {
      throw new Error("User is invalid");
    }

    await dependencies.userRepository.save(user);

    await dependencies.emailService.sendWelcomeEmail(
      user,
    );
  };
}
```

The orchestration is a responsibility of its own; the implementation details remain focused.

---

# 30. O — Open/Closed Principle

Prefer designs where adding a new variant does not require editing unrelated behaviour everywhere.

Avoid repeated branching:

```ts
function calculateFee(
  paymentType: string,
  amount: number,
): number {
  if (paymentType === "card") {
    return amount * 0.02;
  }

  if (paymentType === "bank") {
    return 1;
  }

  if (paymentType === "wallet") {
    return amount * 0.01;
  }

  return 0;
}
```

A strategy contract can make extension explicit:

```ts
type FeeCalculator = {
  calculate(amount: number): number;
};

const cardFeeCalculator: FeeCalculator = {
  calculate: (amount) => amount * 0.02,
};

const bankFeeCalculator: FeeCalculator = {
  calculate: () => 1,
};
```

Do not introduce polymorphism for every `switch`.

Use it when behaviour varies independently and new variants are expected.

---

# 31. L — Liskov Substitution Principle

An implementation of a contract should preserve the expectations of that contract.

Bad contract:

```ts
interface Storage {
  save(value: string): Promise<void>;
}
```

An implementation must not silently:

- reject values another valid implementation accepts;
- change the meaning of `save`;
- require undocumented preconditions;
- produce incompatible side effects.

In TypeScript, structural compatibility does not guarantee behavioural compatibility.

Code review must verify both.

> If callers must ask "Which implementation is this?" before they can safely use the interface, the abstraction may be broken.

---

# 32. I — Interface Segregation Principle

Do not force consumers to depend on operations they do not need.

Avoid:

```ts
interface UserRepository {
  getUser(id: string): Promise<User>;
  saveUser(user: User): Promise<void>;
  deleteUser(id: string): Promise<void>;
  exportUsers(): Promise<string>;
  importUsers(file: File): Promise<void>;
}
```

A read-only consumer should not depend on all of that.

Prefer smaller capability contracts:

```ts
type UserReader = {
  getUser(id: UserId): Promise<User>;
};

type UserWriter = {
  saveUser(user: User): Promise<void>;
};

type UserRemover = {
  deleteUser(id: UserId): Promise<void>;
};
```

Compose capabilities where needed:

```ts
type UserRepository =
  UserReader &
  UserWriter &
  UserRemover;
```

This is particularly useful for testing because a consumer can receive only the dependency it actually needs.

---

# 33. D — Dependency Inversion Principle

High-level business logic should depend on contracts rather than infrastructure details.

Avoid:

```ts
async function registerUser(user: User) {
  const repository = new SqlUserRepository();
  const emailClient = new SendGridClient();

  await repository.save(user);
  await emailClient.sendWelcomeEmail(user.email);
}
```

The use case now owns infrastructure construction.

Prefer injecting capabilities:

```ts
type RegisterUserDependencies = {
  saveUser(user: User): Promise<void>;
  sendWelcomeEmail(user: User): Promise<void>;
};

function createRegisterUser(
  dependencies: RegisterUserDependencies,
) {
  return async function registerUser(
    user: User,
  ): Promise<void> {
    await dependencies.saveUser(user);
    await dependencies.sendWelcomeEmail(user);
  };
}
```

The business operation depends only on what it needs.

This improves:

- testability;
- replaceability;
- separation of infrastructure from business behaviour.

---

# 34. Prefer capability-based dependencies

A function should receive the smallest useful contract.

Avoid:

```ts
function createUserController(
  applicationServices: ApplicationServices,
) {
  // uses only applicationServices.users.getUser
}
```

Prefer:

```ts
type GetUser = (
  userId: UserId,
) => Promise<User | undefined>;

function createUserController(getUser: GetUser) {
  // ...
}
```

This combines Interface Segregation and Dependency Inversion.

---

# 35. React + TypeScript

React component APIs are contracts.

TypeScript should make those contracts easy to understand and difficult to misuse.

---

# 36. Type component props explicitly

```tsx
type UserCardProps = {
  user: User;
  onSelect: (userId: UserId) => void;
};

function UserCard({
  user,
  onSelect,
}: UserCardProps) {
  return (
    <button onClick={() => onSelect(user.id)}>
      {user.name}
    </button>
  );
}
```

Props should describe what the component requires from its caller.

---

# 37. Prefer domain props over primitive prop collections

Avoid:

```tsx
type UserCardProps = {
  id: string;
  name: string;
  email: string;
  role: string;
  department: string;
};
```

when the component conceptually operates on a `User`.

Prefer:

```tsx
type UserCardProps = {
  user: User;
};
```

However, if the component genuinely needs only a small projection, do not force it to depend on a huge model.

```tsx
type UserCardUser = Pick<
  User,
  "id" | "name" | "email"
>;

type UserCardProps = {
  user: UserCardUser;
};
```

Use the smallest meaningful domain contract.

---

# 38. Callback props should describe the event

```tsx
type UserListProps = {
  users: readonly User[];
  onUserSelect: (userId: UserId) => void;
};
```

Prefer event-oriented callbacks over leaking internal setters:

```tsx
// Avoid
type Props = {
  setSelectedUserId: (
    value: string | undefined,
  ) => void;
};

// Prefer
type Props = {
  onUserSelect: (userId: UserId) => void;
};
```

The component contract should communicate intent rather than parent implementation details.

---

# 39. Model mutually exclusive props with discriminated unions

Avoid APIs that permit impossible combinations:

```tsx
type AlertProps = {
  type: "success" | "error";
  successMessage?: string;
  errorMessage?: string;
};
```

This allows:

```tsx
<Alert
  type="success"
  errorMessage="Payment failed"
/>
```

Prefer:

```tsx
type AlertProps =
  | {
      type: "success";
      message: string;
    }
  | {
      type: "error";
      message: string;
      retry?: () => void;
    };
```

Then:

```tsx
function Alert(props: AlertProps) {
  if (props.type === "success") {
    return <SuccessAlert message={props.message} />;
  }

  return (
    <ErrorAlert
      message={props.message}
      onRetry={props.retry}
    />
  );
}
```

The component cannot receive contradictory success/error props.

---

# 40. Avoid boolean-prop explosions

Be cautious with component APIs such as:

```tsx
<Button
  primary
  secondary={false}
  destructive
  compact
  loading
/>
```

Multiple booleans can create invalid combinations.

Prefer explicit variants:

```tsx
type ButtonVariant =
  | "primary"
  | "secondary"
  | "destructive";

type ButtonProps = {
  variant: ButtonVariant;
  isLoading?: boolean;
};
```

This reduces the state space.

---

# 41. Reuse native element props where appropriate

For a wrapper around a native button:

```tsx
type ButtonProps =
  React.ComponentPropsWithoutRef<"button"> & {
    variant?: "primary" | "secondary";
  };

function Button({
  variant = "primary",
  ...buttonProps
}: ButtonProps) {
  return (
    <button
      data-variant={variant}
      {...buttonProps}
    />
  );
}
```

This preserves native props such as:

- `disabled`;
- `type`;
- `aria-*`;
- event handlers.

Do not manually recreate large portions of a native element contract.

---

# 42. Prefer `ReactNode` for renderable children

```tsx
type PanelProps = {
  title: string;
  children: React.ReactNode;
};
```

Use more specific child types only when the component truly requires them.

Do not type every child as `JSX.Element` if strings, numbers, fragments, or `null` are valid.

---

# 43. Event handler types

Use React's event types when the event object is part of the component's implementation.

```tsx
function SearchInput() {
  const handleChange = (
    event: React.ChangeEvent<HTMLInputElement>,
  ) => {
    // ...
  };

  return <input onChange={handleChange} />;
}
```

For component APIs, prefer domain-level values where appropriate.

Instead of requiring the parent to understand a DOM event:

```tsx
type SearchInputProps = {
  onChange: (
    event: React.ChangeEvent<HTMLInputElement>,
  ) => void;
};
```

consider:

```tsx
type SearchInputProps = {
  onValueChange: (value: string) => void;
};
```

The component absorbs DOM mechanics and exposes the domain-relevant value.

---

# 44. State should have the narrowest valid type

Avoid:

```tsx
const [status, setStatus] =
  useState<string>("idle");
```

Prefer:

```tsx
type Status =
  | "idle"
  | "loading"
  | "success"
  | "error";

const [status, setStatus] =
  useState<Status>("idle");
```

Now invalid values cannot be assigned.

---

# 45. Model complex component state as a state machine / discriminated union

Avoid parallel state that can contradict itself:

```tsx
const [isLoading, setIsLoading] =
  useState(false);

const [data, setData] =
  useState<User[] | undefined>();

const [error, setError] =
  useState<string | undefined>();
```

Prefer:

```tsx
type UsersState =
  | {
      status: "idle";
    }
  | {
      status: "loading";
    }
  | {
      status: "success";
      users: User[];
    }
  | {
      status: "error";
      message: string;
    };

const [state, setState] =
  useState<UsersState>({
    status: "idle",
  });
```

This prevents loading + success + error from all being represented simultaneously.

---

# 46. Refs

Type refs according to the value they actually hold.

```tsx
const inputRef =
  useRef<HTMLInputElement>(null);
```

When exposing an imperative ref API, expose the smallest capability required.

```tsx
export type SearchInputHandle = {
  focus(): void;
  clear(): void;
};
```

Do not expose an entire internal component or DOM implementation when a small imperative contract is sufficient.

---

# 47. Context

Do not hide a missing provider with unsafe defaults.

Avoid:

```tsx
const UserContext =
  createContext<UserContextValue>(
    {} as UserContextValue,
  );
```

Prefer:

```tsx
const UserContext =
  createContext<UserContextValue | undefined>(
    undefined,
  );

function useUserContext(): UserContextValue {
  const context = useContext(UserContext);

  if (!context) {
    throw new Error(
      "useUserContext must be used inside UserProvider",
    );
  }

  return context;
}
```

The runtime invariant is checked and callers receive a non-null type.

---

# 48. Reducers

Reducers are an excellent fit for discriminated unions.

```tsx
type State = {
  count: number;
};

type Action =
  | {
      type: "increment";
    }
  | {
      type: "add";
      amount: number;
    }
  | {
      type: "reset";
    };

function reducer(
  state: State,
  action: Action,
): State {
  switch (action.type) {
    case "increment":
      return {
        count: state.count + 1,
      };

    case "add":
      return {
        count: state.count + action.amount,
      };

    case "reset":
      return {
        count: 0,
      };

    default: {
      const exhaustiveCheck: never = action;
      return exhaustiveCheck;
    }
  }
}
```

Each action carries exactly the data required for that action.

---

# 49. Custom hooks

A custom hook should expose a deliberate contract rather than leaking unrelated implementation details.

```tsx
type UseUsersResult =
  | {
      status: "loading";
    }
  | {
      status: "error";
      error: Error;
      retry(): void;
    }
  | {
      status: "success";
      users: readonly User[];
      refresh(): void;
    };
```

The return type can prevent consumers from using data in states where it does not exist.

Do not automatically return large bags of optional fields.

---

# 50. Generic React components

Use generics when the component preserves a relationship between caller-supplied types.

```tsx
type SelectProps<TItem> = {
  items: readonly TItem[];
  getKey: (item: TItem) => string;
  getLabel: (item: TItem) => string;
  onSelect: (item: TItem) => void;
};

function Select<TItem>({
  items,
  getKey,
  getLabel,
  onSelect,
}: SelectProps<TItem>) {
  return (
    <ul>
      {items.map((item) => (
        <li key={getKey(item)}>
          <button
            onClick={() => onSelect(item)}
          >
            {getLabel(item)}
          </button>
        </li>
      ))}
    </ul>
  );
}
```

The selected value remains the same concrete type as the items.

Avoid generic components when a normal domain-specific component would be simpler.

---

# 51. Component SOLID

SOLID can be applied to React without turning components into classes.

## Single Responsibility

A component should have one primary UI responsibility.

Avoid a component that simultaneously:

- fetches unrelated data;
- owns several business workflows;
- transforms many API models;
- renders multiple unrelated views.

Extract hooks, domain functions, and child components when they represent distinct responsibilities.

---

## Open/Closed

Prefer extension through explicit props, composition, and variants rather than editing a shared component for every one-off use case.

```tsx
type CardProps = {
  title: string;
  actions?: React.ReactNode;
  children: React.ReactNode;
};
```

Do not make a component infinitely configurable. Extension should remain within its coherent responsibility.

---

## Liskov Substitution

A reusable component variant must preserve the contract expected by its caller.

A custom button that claims to behave like a button should not:

- ignore `disabled`;
- break keyboard interaction;
- remove accessibility semantics;
- silently change `onClick` behaviour.

Type compatibility alone does not guarantee substitutability.

---

## Interface Segregation

Do not create giant prop interfaces used by every component.

Avoid:

```tsx
type ApplicationProps = {
  currentUser: User;
  permissions: Permission[];
  selectedOrder: Order;
  featureFlags: FeatureFlags;
  theme: Theme;
  onSave(): void;
  onDelete(): void;
  onRefresh(): void;
  // ...
};
```

Pass each component only what it needs.

This improves reuse, testing, and comprehension.

---

## Dependency Inversion

UI/business components should depend on domain capabilities rather than constructing infrastructure.

Avoid:

```tsx
function UserEditor() {
  const api = new UserApiClient();
  // ...
}
```

Prefer injecting the behaviour through a hook, service boundary, context, or function contract:

```tsx
type UserEditorProps = {
  saveUser(user: User): Promise<void>;
};

function UserEditor({
  saveUser,
}: UserEditorProps) {
  // ...
}
```

The exact injection mechanism is architectural; the principle is that high-level UI behaviour should not own low-level infrastructure construction.

---

# 52. Do not mirror backend models blindly through the UI

An API response type and a UI/domain model have different responsibilities.

Avoid spreading backend DTOs through every component when:

- naming differs;
- nullable backend fields are awkward;
- several endpoints represent the same domain concept differently;
- the UI needs derived values;
- backend changes would create unnecessary UI churn.

Prefer mapping at a boundary:

```ts
type UserDto = {
  UserId: string;
  DisplayName: string | null;
};

type User = {
  id: UserId;
  displayName: string;
};

function toUser(dto: UserDto): User {
  return {
    id: dto.UserId as UserId,
    displayName:
      dto.DisplayName ?? "Unknown user",
  };
}
```

Boundary mapping creates a stable internal contract.

---

# 53. Avoid duplicating source-of-truth types manually

When an authoritative type can safely be derived, derive it.

Examples:

```ts
type UserFormValues =
  Pick<User, "name" | "email">;
```

or use generated API types where the project has an approved code-generation pipeline.

However, do not tightly couple unrelated layers merely to avoid typing five fields twice.

DRY applies to **knowledge**, not textual similarity.

A UI model and API DTO may intentionally be different concepts.

---

# 54. Third-party libraries

Do not broadly cast around poor third-party types.

Avoid:

```ts
const result =
  libraryCall() as unknown as User[];
```

Prefer:

- an adapter;
- a small wrapper;
- a local augmentation where appropriate;
- runtime validation at the boundary.

Keep uncertainty contained.

---

# 55. Error handling in TypeScript

Caught errors should normally begin as `unknown`.

```ts
try {
  await saveOrder(order);
} catch (error: unknown) {
  handleSaveError(error);
}
```

Model known domain errors explicitly when callers need to react differently.

```ts
type SaveOrderError =
  | {
      type: "validation";
      messages: string[];
    }
  | {
      type: "conflict";
      currentVersion: number;
    }
  | {
      type: "network";
      retryable: boolean;
    };
```

Do not use a giant error union for failures callers never distinguish.

---

# 56. Maps, records, and dictionaries

Prefer a type that matches the semantics.

Use `Record` for a known or key-constrained lookup object:

```ts
type UsersById =
  Record<UserId, User>;
```

Use `Map` when runtime map behaviour is valuable:

```ts
const usersById =
  new Map<UserId, User>();
```

Do not use `{ [key: string]: any }`.

If values are unknown:

```ts
Record<string, unknown>
```

is safer.

---

# 57. Arrays and readonly arrays

Use:

```ts
User[]
```

for mutable arrays.

Use:

```ts
readonly User[]
```

or:

```ts
ReadonlyArray<User>
```

when callers should not mutate the collection.

For props, selectors, and domain queries, readonly collections are often a useful default.

---

# 58. Tuples

Use tuples when position has stable semantic meaning.

```ts
type Coordinate = readonly [
  latitude: number,
  longitude: number,
];
```

Do not use tuples as anonymous mini-objects when named properties would be clearer.

Avoid:

```ts
type UserData = [
  string,
  string,
  boolean,
  number,
];
```

Prefer a named object type.

---

# 59. Function overloads

Use overloads when callers genuinely have distinct supported call shapes and the relationship cannot be expressed more clearly with unions/generics.

Avoid overloads that simply hide a confused API.

Often this:

```ts
function getUser(
  idOrEmail: string,
): User | undefined {
  // ...
}
```

should instead become two intention-revealing operations:

```ts
getUserById(userId);
getUserByEmail(emailAddress);
```

The Core naming and single-responsibility standards still apply.

---

# 60. Declaration merging and module augmentation

Use only when deliberately integrating with libraries/frameworks designed for augmentation.

Do not use declaration merging as a hidden way to mutate application contracts globally.

Global type changes can create the compile-time equivalent of runtime global state.

---

# 61. Type-only imports

Use type-only imports when the toolchain benefits from explicit separation.

```ts
import type { User } from "./user";
import { getUser } from "./user-service";
```

This communicates which dependencies exist only at compile time and can prevent accidental runtime imports in some configurations.

The project formatter/linter should enforce the chosen convention.

---

# 62. Avoid namespaces in module-based applications

For modern module-based TypeScript, prefer ES modules.

Do not introduce TypeScript `namespace` as a substitute for normal module boundaries unless required by a legacy/global integration.

---

# 63. Classes in TypeScript

TypeScript makes classes easy to model, but classes are not automatically better architecture.

Prefer a class when you need:

- object identity;
- encapsulated mutable state;
- lifecycle;
- polymorphic implementation;
- a framework-required class contract.

Prefer functions/modules for stateless transformations and orchestration when simpler.

---

# 64. Access modifiers

Use `private` / `protected` only where a class design genuinely requires them.

Prefer private state over public mutable internals.

Do not expose a field publicly merely to simplify tests.

Test observable behaviour, or extract the behaviour into a focused dependency.

---

# 65. Dependency contracts should be narrow

Avoid injecting a huge service just because it already exists.

```ts
type OrderLoaderDependencies = {
  getOrder(
    orderId: OrderId,
  ): Promise<Order | undefined>;
};
```

is preferable to giving the use case an entire API client with dozens of unrelated methods.

This improves Interface Segregation and test design.

---

# 66. Tests and types

Type checking does not replace tests.

Types can prove structural constraints such as:

- this function receives a `UserId`;
- this state cannot contain both success and error;
- this component cannot receive mutually exclusive props together.

Tests should prove runtime behaviour such as:

- totals are calculated correctly;
- permissions are enforced;
- errors are mapped correctly;
- components behave correctly;
- APIs are integrated correctly.

Use the type system for contracts and tests for behaviour.

---

# 67. Type-level tests

For reusable libraries or sophisticated public type APIs, compile-time tests may be useful.

For ordinary application code, do not create elaborate type-test suites for obvious annotations.

Use them when type behaviour itself is part of the product/API contract.

---

# 68. Avoid suppressions

Be cautious with:

```ts
// @ts-ignore
// @ts-nocheck
```

Prefer `@ts-expect-error` only when an error is intentionally expected and tested.

Any suppression should be:

- local;
- justified;
- ideally linked to a migration or upstream issue.

Do not disable checking for an entire file merely to avoid resolving normal type problems.

---

# 69. Naming

The Core naming standard applies.

TypeScript-specific conventions:

```text
Types / interfaces / classes / enums → PascalCase
Variables / functions / properties  → camelCase
Generic parameters                  → T or descriptive PascalCase names
Boolean values                      → is / has / can / should / was...
```

Avoid prefixes such as:

```text
IUser
TUser
UserInterface
```

unless required by an established project convention.

Prefer names that describe the concept:

```ts
type User = {};
type UserRepository = {};
type CreateOrderInput = {};
type PaymentResult = {};
```

---

# 70. Common suffixes

Useful type suffixes include:

```text
Input
Output
Result
State
Props
Options
Config
Context
Dto
Response
Request
Error
Handler
Repository
Service
```

Use them only when they clarify the concept.

Do not add a suffix mechanically when the domain noun alone is clearer.

---

# 71. DTOs and domain models

Make transport types explicit when they differ from domain types.

```ts
type CreateUserRequestDto = {
  Name: string;
  EmailAddress: string;
};

type CreateUserInput = {
  name: string;
  emailAddress: string;
};
```

A mapper makes the boundary visible.

```ts
function toCreateUserRequest(
  input: CreateUserInput,
): CreateUserRequestDto {
  return {
    Name: input.name,
    EmailAddress: input.emailAddress,
  };
}
```

This prevents backend naming and nullability from becoming the internal application standard.

---

# 72. Pull-request checklist

## Safety

- [ ] `strict` compiler expectations are preserved.
- [ ] No unnecessary `any`.
- [ ] External/untrusted data is validated where required.
- [ ] Type assertions are justified and local.
- [ ] Non-null assertions are rare and contract-backed.
- [ ] Type-checking suppressions are justified.

## Modelling

- [ ] Types represent domain concepts rather than merely implementation shapes.
- [ ] Closed value sets use narrow types.
- [ ] Impossible states are not represented through bags of optional properties/booleans.
- [ ] Discriminated unions are used where state variants have different valid data.
- [ ] Important unions are exhaustively handled.
- [ ] Optional properties represent genuinely optional data.
- [ ] Mutability/readonly intent is clear.

## Functions

- [ ] Generics preserve a useful relationship rather than add complexity.
- [ ] Important API boundaries have clear parameter/return contracts.
- [ ] Expected absence/failure is visible in the return type.
- [ ] Dependencies use the smallest useful capability contract.

## SOLID

- [ ] Responsibilities are focused.
- [ ] Extensibility has not produced repeated branching everywhere.
- [ ] Implementations honour behavioural contracts.
- [ ] Consumers are not forced to depend on oversized interfaces.
- [ ] Business logic does not construct infrastructure dependencies unnecessarily.

## React

- [ ] Component props define a clear contract.
- [ ] DOM mechanics are not leaked through component APIs without need.
- [ ] Mutually exclusive props are modelled as mutually exclusive types.
- [ ] Boolean prop combinations have not created invalid states.
- [ ] Native element wrappers reuse native prop types where appropriate.
- [ ] Contexts do not hide missing providers behind unsafe assertions.
- [ ] Component/request state avoids contradictory parallel booleans/optionals.
- [ ] Refs expose only the required capability.
- [ ] Generic components preserve a real caller type relationship.

## Maintainability

- [ ] Types are easier to understand than the uncertainty they replace.
- [ ] Type-level programming is not excessively clever.
- [ ] API DTOs are not blindly propagated through the application.
- [ ] Derived types have not coupled unrelated layers merely to avoid duplication.

---

# 73. Rule of thumb

Prefer TypeScript that turns assumptions into contracts:

```text
unknown over any
narrowing over assertions
strict null checks
discriminated unions
exhaustive checking
literal unions
domain types
readonly contracts
narrow dependencies
runtime validation at external boundaries
typed React props
typed state variants
small capability interfaces
```

Avoid using:

```text
any
double assertions
non-null assertions
bags of optional fields
stringly typed state
giant interfaces
giant prop objects
unsafe context defaults
boolean prop explosions
clever conditional/mapped type puzzles
type suppressions
```

as shortcuts around unclear design.

---

# 74. Final principle

TypeScript is most valuable when it helps the codebase express the domain accurately.

> **Do not use TypeScript merely to annotate JavaScript. Use it to encode contracts, constrain invalid states, expose dependencies, and make important assumptions checkable by the compiler.**

The type system should make correct code easier to write and incorrect code harder to represent.
