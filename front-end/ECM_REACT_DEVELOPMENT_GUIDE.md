# ECM React Development Guide

**Parents:**

1. [ECM Core Software Development Standards](../core/STANDARDS.md)
2. [ECM JavaScript Coding Standards — ES6+](./ECM_JAVASCRIPT_ES6_CODING_STANDARDS.md)
3. [ECM TypeScript Coding Standards](./ECM_TYPESCRIPT_CODING_STANDARDS.md)

**Applies to:** New React code and React code that is materially modified

**Primary goal:** Build UIs that are predictable, composable, and easy to change without hiding business rules inside rendering logic.

---

## Table of Contents

- [React Development Guide](#react-guide)
  - [Purpose](#purpose)
  - [Standard Hierarchy](#standard-hierarchy)
  - [Component Design](#component-design)
    - [Use Function Components](#use-function-components)
    - [Keep Components Focused](#keep-components-focused)
    - [Prefer Composition Over Configuration](#prefer-composition-over-configuration)
    - [Separate UI From Data Access](#separate-ui-from-data-access)
    - [Component Design Checklist](#component-design-checklist)
  - [Naming and File Organisation](#naming-and-file-organisation)
    - [Component and File Naming](#component-and-file-naming)
    - [Project Structure](#project-structure)
    - [One Component Per File](#one-component-per-file)
  - [Props, Events, and Callbacks](#props-events-and-callbacks)
    - [Use `onX` for Callback Props and `handleX` Internally](#use-onx-for-callback-props-and-handlex-internally)
    - [Keep Props Intentional](#keep-props-intentional)
    - [Avoid Prop Drilling](#avoid-prop-drilling)
  - [Component Structure and Rendering](#component-structure-and-rendering)
    - [Structure Components Top to Bottom](#structure-components-top-to-bottom)
    - [Keep Conditional Rendering Simple](#keep-conditional-rendering-simple)
    - [Render Lists With Stable Keys](#render-lists-with-stable-keys)
    - [Model Loading and Empty States Explicitly](#model-loading-and-empty-states-explicitly)
  - [State Management](#state-management)
    - [Choose the Smallest Appropriate State Location](#choose-the-smallest-appropriate-state-location)
    - [Prefer Discriminated State Over Parallel Flags](#prefer-discriminated-state-over-parallel-flags)
    - [Use `useReducer` for Complex Transitions](#use-usereducer-for-complex-transitions)
    - [Use Context for Shared Cross-Cutting State](#use-context-for-shared-cross-cutting-state)
    - [State Management Checklist](#state-management-checklist)
  - [Hooks](#hooks)
    - [Extract Reusable Logic Into Custom Hooks](#extract-reusable-logic-into-custom-hooks)
    - [Custom Hooks Should Return a Deliberate Contract](#custom-hooks-should-return-a-deliberate-contract)
    - [Use Effects for Synchronisation, Not for Event Logic](#use-effects-for-synchronisation-not-for-event-logic)
    - [Keep Effect Dependencies Honest](#keep-effect-dependencies-honest)
    - [Clean Up Effects When Needed](#clean-up-effects-when-needed)
    - [Hooks Checklist](#hooks-checklist)
  - [Data Fetching and Forms](#data-fetching-and-forms)
    - [Fetch Close to the Feature That Needs the Data](#fetch-close-to-the-feature-that-needs-the-data)
    - [Map API Data at the Boundary](#map-api-data-at-the-boundary)
    - [Prefer Controlled Inputs for Forms](#prefer-controlled-inputs-for-forms)
    - [Handle Submission as an Explicit Workflow](#handle-submission-as-an-explicit-workflow)
  - [Errors, Suspense, and Resilience](#errors-suspense-and-resilience)
    - [Use Error Boundaries for Render Failures](#use-error-boundaries-for-render-failures)
    - [Do Not Use Error Boundaries for Expected Failures](#do-not-use-error-boundaries-for-expected-failures)
    - [Keep Failure UI Close to the Feature](#keep-failure-ui-close-to-the-feature)
  - [Performance](#performance)
    - [Optimise Only When There Is Evidence](#optimise-only-when-there-is-evidence)
    - [Avoid Premature Memoisation](#avoid-premature-memoisation)
    - [Split Code by Route or Feature When Useful](#split-code-by-route-or-feature-when-useful)
  - [Accessibility](#accessibility)
    - [Use Semantic HTML First](#use-semantic-html-first)
    - [Label Interactive Controls](#label-interactive-controls)
    - [Do Not Rely on Divs for Buttons](#do-not-rely-on-divs-for-buttons)
  - [Testing](#testing)
    - [Test Behaviour, Not Implementation Details](#test-behaviour-not-implementation-details)
    - [Prefer User-Oriented Queries](#prefer-user-oriented-queries)
  - [Pull-Request Checklist](#pull-request-checklist)
  - [Final Principle](#final-principle)

---

<a id="react-guide"></a>

## React Development Guide

<a id="purpose"></a>

### Purpose

React is a UI library for building interfaces from composable components. Used well, it allows teams to express user workflows clearly, isolate rendering from business rules, and evolve features without turning every screen into a tightly coupled monolith.

This guide extends the **Core Software Development Standards**, the **JavaScript ES6+ Standards**, and the **TypeScript Coding Standards**.

It intentionally does not repeat general guidance for naming, function size, control flow, comments, formatting, DRY, or error handling. Those belong in the parent documents.

It also does not repeat TypeScript modelling rules for component props, state, context, reducers, or custom hook return types. For those, see [React + TypeScript](./ECM_TYPESCRIPT_CODING_STANDARDS.md#35-react-typescript) in the TypeScript standards.

> **Core standards govern readable code. TypeScript standards govern component contracts. This guide governs how React components, hooks, and UI workflows should be structured.**

---

<a id="standard-hierarchy"></a>

### Standard Hierarchy

When standards overlap, apply them in this order:

1. **Core standards** — naming, functions, control flow, readability
2. **JavaScript standards** — modern runtime patterns
3. **TypeScript standards** — props, state, and hook contracts
4. **This guide** — React-specific structure and UI workflow

If a React pattern makes the UI easier to write but harder to understand, prefer the clearer option even when it requires slightly more code.

---

<a id="component-design"></a>

### Component Design

React components should communicate a single UI responsibility and compose cleanly with the components around them.

<a id="use-function-components"></a>

#### Use Function Components

Use function components for all new React code.

❌ Avoid introducing class components in new code:

```tsx
class UserPanel extends React.Component {
  render() {
    return <div>{this.props.user.name}</div>;
  }
}
```

✔ Prefer function components:

```tsx
type UserPanelProps = {
  user: User;
};

function UserPanel({ user }: UserPanelProps) {
  return <div>{user.name}</div>;
}
```

Class components may remain in legacy code until they are materially changed, but new features should not expand their use.

---

<a id="keep-components-focused"></a>

#### Keep Components Focused

A component should have one primary UI responsibility.

❌ Avoid components that simultaneously:

- fetch unrelated data;
- own several business workflows;
- transform API models;
- render unrelated sections of a page.

✔ Prefer extracting focused child components and hooks:

```tsx
function OrderPage() {
  const orderState = useOrder(orderId);

  if (orderState.status === "loading") {
    return <LoadingState />;
  }

  if (orderState.status === "error") {
    return <ErrorState message={orderState.message} onRetry={orderState.retry} />;
  }

  return (
    <OrderLayout>
      <OrderSummary order={orderState.order} />
      <OrderItems items={orderState.order.items} />
      <OrderActions order={orderState.order} />
    </OrderLayout>
  );
}
```

Each child component should answer one clear question about the UI.

See also [Component SOLID](./ECM_TYPESCRIPT_CODING_STANDARDS.md#51-component-solid) in the TypeScript standards.

---

<a id="prefer-composition-over-configuration"></a>

#### Prefer Composition Over Configuration

Prefer composing children and slots over adding many optional props that switch behaviour internally.

❌ Be cautious with:

```tsx
<Panel
  showHeader
  showFooter
  showActions
  headerVariant="compact"
  footerAlign="right"
/>
```

✔ Prefer composition:

```tsx
<Panel>
  <PanelHeader>{title}</PanelHeader>
  <PanelBody>{children}</PanelBody>
  <PanelFooter>{actions}</PanelFooter>
</Panel>
```

Composition keeps each component's contract smaller and makes unexpected combinations harder to introduce.

---

<a id="separate-ui-from-data-access"></a>

#### Separate UI From Data Access

Components should render UI. Data fetching, mapping, caching, and retry logic belong in hooks or boundary modules.

❌ Avoid:

```tsx
function UserList() {
  const [users, setUsers] = useState<User[]>([]);

  useEffect(() => {
    fetch("/api/users")
      .then((response) => response.json())
      .then((data) => setUsers(data.items));
  }, []);

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

✔ Prefer:

```tsx
function UserList() {
  const usersState = useUsers();

  if (usersState.status === "loading") {
    return <LoadingState />;
  }

  if (usersState.status === "error") {
    return <ErrorState message={usersState.error.message} onRetry={usersState.retry} />;
  }

  return <UserListView users={usersState.users} />;
}
```

The page component describes the workflow. The hook owns data access. The view component owns rendering.

---

<a id="component-design-checklist"></a>

#### Component Design Checklist

Before considering a component complete, ask:

- Does the component have one clear UI responsibility?

- Is data access separated from rendering where practical?

- Could composition replace a growing prop list?

- Are loading, empty, error, and success states handled explicitly?

- Does the component depend on domain types rather than raw API shapes? See [Do not mirror backend models blindly through the UI](./ECM_TYPESCRIPT_CODING_STANDARDS.md#52-do-not-mirror-backend-models-blindly-through-the-ui).

---

<a id="naming-and-file-organisation"></a>

### Naming and File Organisation

React naming should follow the core naming conventions, with React-specific rules for components, files, and events.

<a id="component-and-file-naming"></a>

#### Component and File Naming

| Item | Convention | Example |
| --- | --- | --- |
| Component | `PascalCase` | `UserCard`, `OrderSummary` |
| Props type | `ComponentNameProps` | `UserCardProps` |
| Custom hook | `use` + `PascalCase` | `useUsers`, `useOrderForm` |
| Context | domain + `Context` | `UserContext`, `ThemeContext` |
| File name | `PascalCase` for components, `camelCase` for hooks/utilities | `UserCard.tsx`, `useUsers.ts` |
| Folder name | `kebab-case` for feature folders | `user-management`, `order-checkout` |

Export one primary component per component file unless the file contains tightly coupled helpers that are not reused elsewhere.

---

<a id="project-structure"></a>

#### Project Structure

Organise React code by feature first, shared UI second, and infrastructure third.

```text
src/
  app/                 # App shell, routing, providers
  features/
    orders/
      components/
      hooks/
      api/
      types/
      OrderPage.tsx
    users/
      components/
      hooks/
      UserPage.tsx
  shared/
    components/        # Reusable UI with no feature ownership
    hooks/
    utils/
  styles/
```

Prefer colocating feature-specific components, hooks, and API helpers inside the feature folder that owns the workflow.

Shared components belong in `shared/components` only when at least two features genuinely reuse them.

---

<a id="one-component-per-file"></a>

#### One Component Per File

✔ Prefer:

```text
UserCard.tsx
UserCard.test.tsx
```

❌ Avoid large files containing many unrelated components simply because they belong to the same screen.

Small private helper components may live in the same file when they are not reused and their only purpose is to keep the primary component readable.

---

<a id="props-events-and-callbacks"></a>

### Props, Events, and Callbacks

Component APIs should describe what the caller provides and what the component reports back, without leaking parent implementation details.

<a id="use-onx-for-callback-props-and-handlex-internally"></a>

#### Use `onX` for Callback Props and `handleX` Internally

Follow the event naming convention from the core standards:

- `onX` → callback exposed by a component
- `handleX` → internal handler inside the component

```tsx
type UserFormProps = {
  onSubmit: (values: UserFormValues) => void;
};

function UserForm({ onSubmit }: UserFormProps) {
  const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();

    onSubmit(getFormValues(event.currentTarget));
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* fields */}
    </form>
  );
}
```

The parent owns the business consequence. The child owns the DOM event mechanics.

---

<a id="keep-props-intentional"></a>

#### Keep Props Intentional

Props should describe the component contract, not mirror every piece of parent state.

❌ Avoid leaking parent setters:

```tsx
type UserListProps = {
  selectedUserId: string | undefined;
  setSelectedUserId: (value: string | undefined) => void;
};
```

✔ Prefer event-oriented callbacks:

```tsx
type UserListProps = {
  selectedUserId?: UserId;
  onUserSelect: (userId: UserId) => void;
};
```

For prop typing rules, variant modelling, and boolean-prop avoidance, see the [React + TypeScript](./ECM_TYPESCRIPT_CODING_STANDARDS.md#35-react-typescript) sections in the TypeScript standards.

---

<a id="avoid-prop-drilling"></a>

#### Avoid Prop Drilling

Pass props explicitly when the dependency chain is short and the data flow remains easy to follow.

Consider Context when many unrelated components at different depths need the same stable, cross-cutting value such as:

- authenticated user;
- theme;
- locale;
- feature flags already validated at the app boundary.

❌ Avoid using Context for data that only one branch needs.

✔ Prefer fetching or deriving data close to the feature that consumes it.

Context providers should be placed at the narrowest level that still satisfies the dependency tree.

See [Context](./ECM_TYPESCRIPT_CODING_STANDARDS.md#47-context) in the TypeScript standards for provider and hook patterns.

---

<a id="component-structure-and-rendering"></a>

### Component Structure and Rendering

A component should read like a short story: preconditions, state, derived values, handlers, then JSX.

<a id="structure-components-top-to-bottom"></a>

#### Structure Components Top to Bottom

✔ Prefer this order inside a component:

1. props destructuring
2. hooks
3. derived values
4. event handlers
5. early returns for loading, empty, or error states
6. main render

```tsx
function OrderSummaryPanel({ orderId }: OrderSummaryPanelProps) {
  const orderState = useOrder(orderId);

  const formattedTotal = useMemo(() => {
    if (orderState.status !== "success") {
      return undefined;
    }

    return formatCurrency(orderState.order.total);
  }, [orderState]);

  const handleRetry = () => {
    orderState.retry();
  };

  if (orderState.status === "loading") {
    return <LoadingState />;
  }

  if (orderState.status === "error") {
    return <ErrorState message={orderState.message} onRetry={handleRetry} />;
  }

  return (
    <section>
      <h2>Order Summary</h2>
      <p>Total: {formattedTotal}</p>
    </section>
  );
}
```

See [Lead With Intent](../core/STANDARDS.md#lead-with-intent) in the core standards.

---

<a id="keep-conditional-rendering-simple"></a>

#### Keep Conditional Rendering Simple

Use guard-style early returns for major UI states.

❌ Avoid deeply nested ternaries in JSX:

```tsx
return (
  <div>
    {isLoading ? (
      <Spinner />
    ) : hasError ? (
      <ErrorMessage />
    ) : items.length === 0 ? (
      <EmptyState />
    ) : (
      <ItemList items={items} />
    )}
  </div>
);
```

✔ Prefer:

```tsx
if (isLoading) {
  return <Spinner />;
}

if (hasError) {
  return <ErrorMessage />;
}

if (items.length === 0) {
  return <EmptyState />;
}

return <ItemList items={items} />;
```

For small inline choices such as label text, a ternary or logical expression is acceptable.

See [Use Guard Clauses](../core/STANDARDS.md#use-guard-clauses) in the core standards.

---

<a id="render-lists-with-stable-keys"></a>

#### Render Lists With Stable Keys

List keys must identify the entity, not the row position.

❌ Avoid:

```tsx
{users.map((user, index) => (
  <UserRow key={index} user={user} />
))}
```

✔ Prefer:

```tsx
{users.map((user) => (
  <UserRow key={user.id} user={user} />
))}
```

Do not use random values generated during render as keys.

---

<a id="model-loading-and-empty-states-explicitly"></a>

#### Model Loading and Empty States Explicitly

Every data-driven view should decide what the user sees when:

- data is loading;
- data loaded successfully but is empty;
- data failed to load;
- data is available and can be rendered.

❌ Avoid silently rendering nothing:

```tsx
function UserList({ users }: UserListProps) {
  return (
    <ul>
      {users?.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

✔ Prefer explicit states:

```tsx
function UserList({ usersState }: UserListProps) {
  if (usersState.status === "loading") {
    return <LoadingState label="Loading users" />;
  }

  if (usersState.status === "error") {
    return <ErrorState message={usersState.message} onRetry={usersState.retry} />;
  }

  if (usersState.users.length === 0) {
    return <EmptyState title="No users found" />;
  }

  return (
    <ul>
      {usersState.users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

For typed state modelling, see [Model complex component state as a state machine](./ECM_TYPESCRIPT_CODING_STANDARDS.md#45-model-complex-component-state-as-a-state-machine-discriminated-union).

---

<a id="state-management"></a>

### State Management

State should live at the lowest level that still satisfies the UI requirement.

<a id="choose-the-smallest-appropriate-state-location"></a>

#### Choose the Smallest Appropriate State Location

Ask these questions in order:

1. Can this be derived from existing props or state?
2. Can this stay local to one component?
3. Does it need to be shared between a few siblings?
4. Does it need to be shared across a feature or the app?

❌ Avoid lifting state to the root "just in case".

✔ Prefer local state until a real sharing requirement appears.

---

<a id="prefer-discriminated-state-over-parallel-flags"></a>

#### Prefer Discriminated State Over Parallel Flags

❌ Avoid parallel booleans and optional fields that can contradict each other:

```tsx
const [isLoading, setIsLoading] = useState(false);
const [users, setUsers] = useState<User[] | undefined>();
const [errorMessage, setErrorMessage] = useState<string | undefined>();
```

✔ Prefer one state object with a discriminant:

```tsx
type UsersState =
  | { status: "idle" }
  | { status: "loading" }
  | { status: "success"; users: User[] }
  | { status: "error"; message: string };

const [usersState, setUsersState] = useState<UsersState>({ status: "idle" });
```

This prevents impossible combinations such as loading and success at the same time.

---

<a id="use-usereducer-for-complex-transitions"></a>

#### Use `useReducer` for Complex Transitions

Use `useReducer` when state changes depend on the previous state, multiple fields must change together, or the transition rules are easier to express as actions.

```tsx
type FormState = {
  values: UserFormValues;
  errors: Partial<Record<keyof UserFormValues, string>>;
  isSubmitting: boolean;
};

type FormAction =
  | { type: "field-changed"; field: keyof UserFormValues; value: string }
  | { type: "submit-started" }
  | { type: "submit-succeeded" }
  | { type: "submit-failed"; errors: FormState["errors"] };

function formReducer(state: FormState, action: FormAction): FormState {
  switch (action.type) {
    case "field-changed":
      return {
        ...state,
        values: {
          ...state.values,
          [action.field]: action.value,
        },
      };

    case "submit-started":
      return {
        ...state,
        isSubmitting: true,
      };

    case "submit-succeeded":
      return {
        ...state,
        isSubmitting: false,
        errors: {},
      };

    case "submit-failed":
      return {
        ...state,
        isSubmitting: false,
        errors: action.errors,
      };

    default: {
      const exhaustiveCheck: never = action;
      return exhaustiveCheck;
    }
  }
}
```

If a `switch` grows beyond eight cases, consider replacing it with a map. See [Choose the Appropriate Conditional Structure](../core/STANDARDS.md#choose-the-appropriate-conditional-structure) in the core standards.

See [Reducers](./ECM_TYPESCRIPT_CODING_STANDARDS.md#48-reducers) in the TypeScript standards.

---

<a id="use-context-for-shared-cross-cutting-state"></a>

#### Use Context for Shared Cross-Cutting State

Use Context for values that many components need and that change infrequently relative to render frequency.

Examples:

- authenticated session;
- theme;
- locale;
- permissions already resolved at the app boundary.

Do not use Context as a general-purpose store for all feature state.

Each context should expose a focused hook such as `useAuth()` rather than raw context objects scattered through the tree.

---

<a id="state-management-checklist"></a>

#### State Management Checklist

- Is this state derived rather than stored?

- Is state colocated at the lowest useful level?

- Can invalid combinations be made unrepresentable?

- Should this be a reducer instead of several `useState` calls?

- Is Context truly needed, or is prop passing still clearer?

---

<a id="hooks"></a>

### Hooks

Hooks should encapsulate reusable behaviour and keep components focused on rendering decisions.

<a id="extract-reusable-logic-into-custom-hooks"></a>

#### Extract Reusable Logic Into Custom Hooks

Extract a custom hook when logic combines state, effects, and domain rules in a way that would otherwise clutter a component.

```tsx
function useOrder(orderId: OrderId) {
  const [state, setState] = useState<OrderState>({ status: "idle" });

  useEffect(() => {
    let isCancelled = false;

    async function loadOrder() {
      setState({ status: "loading" });

      try {
        const order = await fetchOrder(orderId);

        if (!isCancelled) {
          setState({ status: "success", order });
        }
      } catch (error) {
        if (!isCancelled) {
          setState({
            status: "error",
            message: getErrorMessage(error),
          });
        }
      }
    }

    loadOrder();

    return () => {
      isCancelled = true;
    };
  }, [orderId]);

  const retry = () => {
    setState({ status: "idle" });
  };

  return { ...state, retry };
}
```

Custom hooks may call other hooks, but they should not become hidden components with JSX inside them unless the project has an explicit pattern for render-props or hook-returned elements.

---

<a id="custom-hooks-should-return-a-deliberate-contract"></a>

#### Custom Hooks Should Return a Deliberate Contract

❌ Avoid returning unrelated optional fields:

```tsx
return {
  users,
  isLoading,
  error,
  refresh,
};
```

✔ Prefer a discriminated result:

```tsx
return usersState;
```

Where `usersState` is:

```tsx
type UseUsersResult =
  | { status: "loading" }
  | { status: "error"; error: Error; retry(): void }
  | { status: "success"; users: readonly User[]; refresh(): void };
```

See [Custom hooks](./ECM_TYPESCRIPT_CODING_STANDARDS.md#49-custom-hooks) in the TypeScript standards.

---

<a id="use-effects-for-synchronisation-not-for-event-logic"></a>

#### Use Effects for Synchronisation, Not for Event Logic

`useEffect` is for synchronising React state with external systems such as:

- network subscriptions;
- browser APIs;
- non-React widgets;
- document title or focus management when tied to rendered state.

❌ Avoid effects that simply react to user actions already handled in event handlers:

```tsx
function SearchForm() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState<Result[]>([]);

  useEffect(() => {
    if (query.length === 0) {
      setResults([]);
      return;
    }

    search(query).then(setResults);
  }, [query]);

  const handleSubmit = (event: React.FormEvent) => {
    event.preventDefault();
    setQuery(getQueryFromForm(event.currentTarget));
  };

  return <form onSubmit={handleSubmit}>{/* ... */}</form>;
}
```

✔ Prefer performing the action in the handler when the trigger is explicit user input:

```tsx
const handleSubmit = async (event: React.FormEvent) => {
  event.preventDefault();

  const nextQuery = getQueryFromForm(event.currentTarget);
  setQuery(nextQuery);
  setResults(await search(nextQuery));
};
```

Use an effect when the component must stay synchronised with something outside the event flow, not when a button click already defines the moment to act.

---

<a id="keep-effect-dependencies-honest"></a>

#### Keep Effect Dependencies Honest

Effects should include every value from the component scope that the effect reads and that can change between renders.

❌ Avoid suppressing dependency warnings without a documented reason.

✔ Prefer one of these responses:

- add the missing dependency;
- move stable logic outside the component;
- refactor so the effect has a smaller, clearer responsibility.

Do not copy stale values into refs simply to silence lint warnings unless the behaviour is intentionally tied to first render only and that choice is obvious.

---

<a id="clean-up-effects-when-needed"></a>

#### Clean Up Effects When Needed

If an effect starts a subscription, timer, listener, or async request, clean it up when the component unmounts or when the dependencies change.

```tsx
useEffect(() => {
  const controller = new AbortController();

  fetchUsers({ signal: controller.signal })
    .then(setUsers)
    .catch(handleFetchError);

  return () => {
    controller.abort();
  };
}, []);
```

This prevents stale responses from updating unmounted components.

---

<a id="hooks-checklist"></a>

#### Hooks Checklist

- Does this hook have one clear responsibility?

- Does the return value make invalid consumer usage harder?

- Is this effect truly synchronisation, or should the logic live in an event handler?

- Are dependencies complete and intentional?

- Does the effect clean up external work when needed?

---

<a id="data-fetching-and-forms"></a>

### Data Fetching and Forms

<a id="fetch-close-to-the-feature-that-needs-the-data"></a>

#### Fetch Close to the Feature That Needs the Data

Fetch in the feature hook or page that owns the workflow unless multiple routes genuinely share exactly the same cached resource.

Avoid global fetch-on-app-load patterns that pull data for screens the user may never visit.

---

<a id="map-api-data-at-the-boundary"></a>

#### Map API Data at the Boundary

Convert API DTOs into UI-friendly domain shapes before they spread through components.

```tsx
function toUserViewModel(dto: UserDto): User {
  return {
    id: dto.user_id,
    name: dto.full_name,
    email: dto.email_address,
  };
}
```

Components should not need to understand transport-layer field names.

See [Do not mirror backend models blindly through the UI](./ECM_TYPESCRIPT_CODING_STANDARDS.md#52-do-not-mirror-backend-models-blindly-through-the-ui).

---

<a id="prefer-controlled-inputs-for-forms"></a>

#### Prefer Controlled Inputs for Forms

Prefer controlled inputs when the form participates in validation, conditional fields, or submission workflows.

```tsx
function UserForm({ initialValues, onSubmit }: UserFormProps) {
  const [values, setValues] = useState(initialValues);

  const handleFieldChange = (
    field: keyof UserFormValues,
  ) => (event: React.ChangeEvent<HTMLInputElement>) => {
    setValues((currentValues) => ({
      ...currentValues,
      [field]: event.target.value,
    }));
  };

  const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    onSubmit(values);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={values.name} onChange={handleFieldChange("name")} />
    </form>
  );
}
```

Uncontrolled inputs may be acceptable for simple, one-off filters when values are read only on submit.

---

<a id="handle-submission-as-an-explicit-workflow"></a>

#### Handle Submission as an Explicit Workflow

Form submission should follow a clear sequence:

1. prevent default browser submission when needed;
2. validate input;
3. enter submitting state;
4. call the domain action;
5. handle success or failure explicitly.

❌ Avoid mutating several unrelated state variables from scattered callbacks.

✔ Prefer one reducer or one submission handler that expresses the workflow.

---

<a id="errors-suspense-and-resilience"></a>

### Errors, Suspense, and Resilience

<a id="use-error-boundaries-for-render-failures"></a>

#### Use Error Boundaries for Render Failures

Use error boundaries for unexpected rendering failures in a feature area.

```tsx
<ErrorBoundary fallback={<FeatureErrorPanel />}>
  <OrderPage />
</ErrorBoundary>
```

Place boundaries where a failure can be contained without crashing the entire application shell.

---

<a id="do-not-use-error-boundaries-for-expected-failures"></a>

#### Do Not Use Error Boundaries for Expected Failures

Expected failures such as:

- failed HTTP requests;
- validation errors;
- permission denials;
- empty search results

should be modelled as normal UI states, not thrown errors.

See [Provide Meaningful Errors](../core/STANDARDS.md#provide-meaningful-errors) in the core standards.

---

<a id="keep-failure-ui-close-to-the-feature"></a>

#### Keep Failure UI Close to the Feature

Prefer feature-level error panels, inline field errors, or retry actions over generic global alerts that remove context from the user.

The user should understand which part of the workflow failed and what they can do next.

---

<a id="performance"></a>

### Performance

React performance work should be driven by measured problems, not by default wrapping every component in memoisation.

<a id="optimise-only-when-there-is-evidence"></a>

#### Optimise Only When There Is Evidence

Optimise when profiling or realistic usage shows:

- expensive renders repeated unnecessarily;
- large lists causing interaction lag;
- avoidable network or computation work during typing or scrolling.

Do not optimise before the component structure is clear.

---

<a id="avoid-premature-memoisation"></a>

#### Avoid Premature Memoisation

❌ Avoid blanket use of:

- `React.memo`
- `useMemo`
- `useCallback`

without evidence that referential stability solves a real render problem.

✔ Prefer fixing the root cause first:

- smaller components;
- less state lifted unnecessarily;
- derived values computed only where needed;
- stable keys and list rendering.

Use memoisation selectively for hot paths such as large lists or expensive child components receiving stable props.

---

<a id="split-code-by-route-or-feature-when-useful"></a>

#### Split Code by Route or Feature When Useful

Use route-level or feature-level code splitting when a screen or module is large enough to affect initial load time.

```tsx
const AdminReportsPage = lazy(() => import("./features/admin-reports/AdminReportsPage"));
```

Pair lazy loading with an explicit loading fallback.

---

<a id="accessibility"></a>

### Accessibility

Accessible UI is part of correct UI, not an optional polish step.

<a id="use-semantic-html-first"></a>

#### Use Semantic HTML First

Prefer native elements before building custom interactive widgets.

```tsx
<button type="button" onClick={onSave}>
  Save
</button>
```

instead of:

```tsx
<div onClick={onSave}>Save</div>
```

Semantic HTML gives keyboard support, roles, and browser behaviour for free.

---

<a id="label-interactive-controls"></a>

#### Label Interactive Controls

Form controls must have an accessible name through a `<label>`, `aria-label`, or `aria-labelledby`.

```tsx
<label htmlFor="email">Email</label>
<input id="email" name="email" type="email" value={email} onChange={handleEmailChange} />
```

Do not rely on placeholder text alone as a label.

---

<a id="do-not-rely-on-divs-for-buttons"></a>

#### Do Not Rely on Divs for Buttons

If an element behaves like a button, use a `button`. If it navigates, use a link.

Custom widgets that cannot use native elements must reproduce keyboard interaction and ARIA semantics deliberately.

When wrapping native elements, preserve native props and accessibility attributes. See [Reuse native element props where appropriate](./ECM_TYPESCRIPT_CODING_STANDARDS.md#41-reuse-native-element-props-where-appropriate).

---

<a id="testing"></a>

### Testing

React tests should protect user-visible behaviour and regressions, not lock the codebase to implementation details.

<a id="test-behaviour-not-implementation-details"></a>

#### Test Behaviour, Not Implementation Details

❌ Avoid tests that break when harmless refactors occur:

- asserting exact hook call order;
- testing private helper functions that are not part of the contract;
- querying by class names that belong to styling internals.

✔ Prefer tests that verify what the user can see and do:

- labels and button text;
- validation messages;
- disabled and loading states;
- navigation after success.

---

<a id="prefer-user-oriented-queries"></a>

#### Prefer User-Oriented Queries

Prefer queries in this order:

1. `getByRole`
2. `getByLabelText`
3. `getByText`
4. `getByTestId` only when no semantic query is practical

This keeps tests aligned with accessibility and real user interaction.

---

<a id="pull-request-checklist"></a>

### Pull-Request Checklist

Before opening a React pull request, ask:

**Structure**

- Does each component have one clear UI responsibility?

- Is data access separated from rendering where practical?

- Are feature files colocated and named consistently?

**State and hooks**

- Is state colocated at the lowest useful level?

- Are loading, empty, error, and success states explicit?

- Are effects used for synchronisation rather than event logic?

- Do custom hooks return deliberate contracts?

**Contracts**

- Are callback props named with `onX` and internal handlers with `handleX`?

- Are props and state typed according to the TypeScript standards?

- Are API DTOs mapped before they reach UI components?

**UI quality**

- Are list keys stable?

- Are semantic HTML and labels used correctly?

- Are error boundaries reserved for unexpected render failures?

**Performance and tests**

- Was memoisation added only where justified?

- Do tests verify user-visible behaviour rather than implementation details?

---

<a id="final-principle"></a>

### Final Principle

> **A good React component makes the user workflow obvious, keeps invalid UI states hard to represent, and leaves the reader able to understand the screen from top to bottom without hunting through hidden side effects.**

When in doubt, prefer clearer structure over cleverness, and prefer composition over configuration.
