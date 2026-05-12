# TypeScript + React Advanced Types — Deep Notes with Examples

This document covers the topics listed in `Mix/text.file`:

- Advanced Types
- Generics
- Utility types
- Mapped types
- Conditional types
- Discriminated unions
- Type Safety Design
- Reusable generic components
- Typed APIs
- Typed hooks
- API response modelling
- Strict Mode
- Null safety
- Narrowing
- Exhaustive checks
- Practical interview prompts

> **Assumptions**: Examples use **TypeScript** + **React** and a small **API layer** (native `fetch` and optional `axios`). Snippets focus on type design rather than UI styling.

---

## Table of Contents

1. [Advanced Types](#advanced-types)
2. [Generics](#generics)
3. [Utility Types](#utility-types)
4. [Mapped Types](#mapped-types)
5. [Conditional Types](#conditional-types)
6. [Discriminated Unions](#discriminated-unions)
7. [Type Safety Design](#type-safety-design)
8. [Reusable Generic Components](#reusable-generic-components)
9. [Typed APIs](#typed-apis)
10. [Typed Hooks](#typed-hooks)
11. [API Response Modelling](#api-response-modelling)
12. [Strict Mode](#strict-mode)
13. [Null Safety](#null-safety)
14. [Narrowing](#narrowing)
15. [Exhaustive Checks](#exhaustive-checks)
16. [Practical Questions (Interview-Style)](#practical-questions-interview-style)

---

## Advanced Types

Advanced types are the building blocks that let you model real-world constraints in code: unions/intersections, literal types, `keyof`, indexed access types, template literal types, type predicates, etc.

### Why it matters

- Helps you represent **domain invariants** (e.g., state machines, valid transitions, API schemas).
- Reduces runtime checks by shifting correctness into compile time.
- Makes refactoring safer: you change a type and TypeScript points to all impacted code.

### Example 1: `keyof` + indexed access for safe property reads

```ts
type User = {
  id: string;
  name: string;
  email?: string;
};

function getProp<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const u: User = { id: '1', name: 'A' };
const name = getProp(u, 'name'); // string
const email = getProp(u, 'email'); // string | undefined
```

### Example 2: template literal types for event names

```ts
type Entity = 'user' | 'order';
type Action = 'created' | 'updated' | 'deleted';

type DomainEventName = `${Entity}.${Action}`;

const ev1: DomainEventName = 'user.created';
// const ev2: DomainEventName = "user.paid"; // error
```

### Example 3: branding (nominal typing) to prevent ID mixups

```ts
type Brand<T, B extends string> = T & { readonly __brand: B };

type UserId = Brand<string, 'UserId'>;
type OrderId = Brand<string, 'OrderId'>;

const asUserId = (s: string) => s as UserId;
const asOrderId = (s: string) => s as OrderId;

function loadUser(id: UserId) {}

loadUser(asUserId('u_1'));
// loadUser(asOrderId("o_1")); // error
```

---

## Generics

Generics parameterize types and functions so they can work with multiple shapes while preserving type information.

### What to optimize for

- **Inference first**: design APIs so callers rarely need to write `<T>`.
- **Constrain when needed**: `T extends ...` to express requirements.
- **Keep generics local**: avoid pushing `<T>` through every layer unless it’s truly reusable.

### Example 1: generic API client function

```ts
export type ApiError = {
  message: string;
  status?: number;
  details?: unknown;
};

async function fetchJson<T>(
  input: RequestInfo,
  init?: RequestInit,
): Promise<T> {
  const res = await fetch(input, init);
  if (!res.ok) {
    const text = await res.text().catch(() => '');
    throw {
      message: `Request failed`,
      status: res.status,
      details: text,
    } satisfies ApiError;
  }
  return (await res.json()) as T;
}

type User = { id: string; name: string };
const user = await fetchJson<User>('/api/users/1');
```

### Example 2: generic React component props

```tsx
import React from 'react';

type SelectProps<T> = {
  value: T;
  options: readonly T[];
  getLabel: (t: T) => string;
  onChange: (t: T) => void;
};

export function Select<T>(props: SelectProps<T>) {
  const { value, options, getLabel, onChange } = props;
  return (
    <select
      value={String(options.indexOf(value))}
      onChange={(e) => onChange(options[Number(e.target.value)])}
    >
      {options.map((opt, idx) => (
        <option key={idx} value={idx}>
          {getLabel(opt)}
        </option>
      ))}
    </select>
  );
}
```

### Example 3: constrained generics for “has id”

```ts
type HasId = { id: string };

function indexById<T extends HasId>(items: readonly T[]): Record<string, T> {
  return Object.fromEntries(items.map((x) => [x.id, x])) as Record<string, T>;
}
```

---

## Utility Types

Utility types transform existing types without rewriting them.

### Common ones

- `Partial<T>`: make fields optional
- `Required<T>`: make fields required
- `Readonly<T>`: immutable view
- `Pick<T, K>` / `Omit<T, K>`: include/exclude keys
- `Record<K, V>`: map keys to values
- `ReturnType<F>` / `Parameters<F>`: derive function shapes

### Example 1: update DTO vs read model

```ts
type User = {
  id: string;
  name: string;
  email?: string;
  createdAt: string;
};

type UpdateUserDto = Partial<Pick<User, 'name' | 'email'>>;
```

### Example 2: typed form state using `Record`

```ts
type FieldName = 'name' | 'email' | 'age';
type FieldErrors = Record<FieldName, string | null>;

const errors: FieldErrors = { name: null, email: 'invalid', age: null };
```

### Example 3: API function type derivation

```ts
async function getUser(id: string) {
  return { id, name: 'A' } as const;
}

type GetUserResult = Awaited<ReturnType<typeof getUser>>;
// { readonly id: string; readonly name: "A" }
```

---

## Mapped Types

Mapped types iterate over keys and build a new object type.

### Example 1: make all properties nullable

```ts
type Nullable<T> = { [K in keyof T]: T[K] | null };

type User = { id: string; email?: string };
type UserNullable = Nullable<User>;
// { id: string | null; email?: (string | undefined) | null }
```

### Example 2: deep readonly (practical-ish version)

```ts
type Primitive = string | number | boolean | bigint | symbol | null | undefined;

type DeepReadonly<T> = T extends Primitive
  ? T
  : T extends (...args: any[]) => any
    ? T
    : T extends readonly (infer U)[]
      ? readonly DeepReadonly<U>[]
      : { readonly [K in keyof T]: DeepReadonly<T[K]> };

type Config = { api: { baseUrl: string }; flags: string[] };
type Frozen = DeepReadonly<Config>;
```

### Example 3: turn a model into “form fields”

```ts
type FormField<T> = {
  value: T;
  touched: boolean;
  error?: string;
};

type FormState<TModel extends object> = {
  [K in keyof TModel]-?: FormField<TModel[K]>;
};

type Profile = { name: string; age: number };
type ProfileForm = FormState<Profile>;
```

---

## Conditional Types

Conditional types let you express “if this type matches that pattern, produce X else Y”. They’re essential for building reusable type utilities.

### Example 1: extract item type from array

```ts
type ElementType<T> = T extends readonly (infer U)[] ? U : T;

type A = ElementType<string[]>; // string
type B = ElementType<number>; // number
```

### Example 2: API success payload extractor

```ts
type ApiSuccess<T> = { ok: true; data: T };
type ApiFailure = { ok: false; error: { message: string; code?: string } };
type ApiResult<T> = ApiSuccess<T> | ApiFailure;

type DataOf<R> = R extends { ok: true; data: infer D } ? D : never;

type User = { id: string };
type UserData = DataOf<ApiResult<User>>; // User
```

### Example 3: make function async return type explicit

```ts
type Asyncify<F> = F extends (...args: infer A) => infer R
  ? (...args: A) => Promise<Awaited<R>>
  : never;

type Fn = (x: number) => string;
type AsyncFn = Asyncify<Fn>; // (x: number) => Promise<string>
```

---

## Discriminated Unions

Discriminated unions are unions of object types sharing a common literal field (the “discriminant”). They allow safe narrowing and enforce exhaustive handling.

### Example 1: API request state machine for UI

```ts
type RequestState<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: string };

function render<T>(s: RequestState<T>) {
  switch (s.status) {
    case 'idle':
      return 'Idle';
    case 'loading':
      return 'Loading';
    case 'success':
      return s.data;
    case 'error':
      return s.error;
  }
}
```

### Example 2: reducer with discriminated actions

```ts
type Action =
  | { type: 'add'; value: number }
  | { type: 'remove'; id: string }
  | { type: 'reset' };

type State = { items: { id: string; value: number }[] };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case 'add':
      return {
        items: [
          ...state.items,
          { id: crypto.randomUUID(), value: action.value },
        ],
      };
    case 'remove':
      return { items: state.items.filter((x) => x.id !== action.id) };
    case 'reset':
      return { items: [] };
  }
}
```

### Example 3: discriminated union + React props (variant component)

```tsx
type BannerProps =
  | { variant: 'info'; message: string }
  | { variant: 'error'; message: string; retry: () => void };

export function Banner(props: BannerProps) {
  if (props.variant === 'error') {
    return (
      <div>
        <strong>Error:</strong> {props.message}
        <button onClick={props.retry}>Retry</button>
      </div>
    );
  }

  return <div>{props.message}</div>;
}
```

---

## Type Safety Design

Type safety design is about using the type system to make invalid states unrepresentable and make correct usage “the easiest path”.

### Principles

1. **Model domain constraints** (don’t just mirror JSON).
2. **Prefer narrow types at boundaries** (validate/parse), wider types internally only when needed.
3. **Push `unknown` to the edge**: parse it into a safe shape.
4. **Create small reusable type utilities** rather than clever one-off types.

### Example 1: parse `unknown` API response (boundary)

```ts
type User = { id: string; name: string };

function isUser(x: unknown): x is User {
  return (
    typeof x === 'object' &&
    x !== null &&
    'id' in x &&
    typeof (x as any).id === 'string' &&
    'name' in x &&
    typeof (x as any).name === 'string'
  );
}

async function getUserSafe(id: string): Promise<User> {
  const data: unknown = await fetch(`/api/users/${id}`).then((r) => r.json());
  if (!isUser(data)) throw new Error('Invalid user payload');
  return data;
}
```

### Example 2: make invalid UI states impossible

```ts
type AuthState =
  | { status: 'anonymous' }
  | { status: 'authenticated'; userId: string; token: string };

function callApi(state: AuthState) {
  if (state.status === 'anonymous') {
    throw new Error('Must be logged in');
  }
  // token is guaranteed here
  return fetch('/api/me', {
    headers: { Authorization: `Bearer ${state.token}` },
  });
}
```

### Example 3: avoid “stringly typed” parameters

```ts
type SortDir = 'asc' | 'desc';
type SortKey = 'name' | 'createdAt';

type Query = { sortBy: SortKey; sortDir: SortDir };

function buildQuery(q: Query) {
  return `?sortBy=${q.sortBy}&sortDir=${q.sortDir}`;
}
```

---

## Reusable Generic Components

When typing reusable components, the goal is:

- Call-site inference (minimal annotations)
- Correct event types
- Correct relationship between props (e.g., columns depend on row type)

### Example 1: typed `Table<T>` with `columns` tied to row shape

```tsx
import React from 'react';

export type ColumnDef<T> = {
  key: string;
  header: React.ReactNode;
  cell: (row: T) => React.ReactNode;
};

export type TableProps<T> = {
  rows: readonly T[];
  columns: readonly ColumnDef<T>[];
  getRowKey: (row: T) => string;
};

export function Table<T>(props: TableProps<T>) {
  const { rows, columns, getRowKey } = props;
  return (
    <table>
      <thead>
        <tr>
          {columns.map((c) => (
            <th key={c.key}>{c.header}</th>
          ))}
        </tr>
      </thead>
      <tbody>
        {rows.map((r) => (
          <tr key={getRowKey(r)}>
            {columns.map((c) => (
              <td key={c.key}>{c.cell(r)}</td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  );
}

// usage
type User = { id: string; name: string; email?: string };

const userColumns: ColumnDef<User>[] = [
  { key: 'name', header: 'Name', cell: (u) => u.name },
  { key: 'email', header: 'Email', cell: (u) => u.email ?? '-' },
];
```

### Example 2: generic `AsyncBoundary` render-prop

```tsx
type RequestState<T> =
  | { status: 'loading' }
  | { status: 'error'; error: string }
  | { status: 'success'; data: T };

export function AsyncBoundary<T>(props: {
  state: RequestState<T>;
  children: (data: T) => React.ReactNode;
}) {
  const { state } = props;
  if (state.status === 'loading') return <div>Loading...</div>;
  if (state.status === 'error') return <div>Error: {state.error}</div>;
  return <>{props.children(state.data)}</>;
}
```

### Example 3: polymorphic component (button vs link)

```tsx
type AsChildProps<E extends React.ElementType> = {
  as?: E;
} & Omit<React.ComponentProps<E>, 'as'>;

export function Box<E extends React.ElementType = 'div'>(
  props: AsChildProps<E>,
) {
  const { as, ...rest } = props;
  const Comp = (as ?? 'div') as React.ElementType;
  return <Comp {...rest} />;
}

// <Box as="a" href="/home">Home</Box>
// <Box as="button" onClick={() => {}}>Click</Box>
```

---

## Typed APIs

Typed APIs means your API calls return strongly typed data and errors, and request parameters are typed.

### Approach

- **At the boundary**: treat JSON as `unknown` and validate (runtime) if you need true safety.
- **Inside the app**: use typed wrappers so the rest of the code never sees `any`.

### Example 1: typed fetch wrapper + typed endpoints

```ts
type ApiProblem = { message: string; status: number };
type ApiResult<T> = { ok: true; data: T } | { ok: false; error: ApiProblem };

async function apiGet<T>(
  url: string,
  init?: RequestInit,
): Promise<ApiResult<T>> {
  try {
    const res = await fetch(url, init);
    if (!res.ok) {
      return {
        ok: false,
        error: { message: res.statusText, status: res.status },
      };
    }
    const json = (await res.json()) as T;
    return { ok: true, data: json };
  } catch (e) {
    return {
      ok: false,
      error: {
        message: e instanceof Error ? e.message : 'Unknown error',
        status: 0,
      },
    };
  }
}

type User = { id: string; name: string };
const r = await apiGet<User>('/api/users/1');
```

### Example 2 (axios): generic helper

```ts
// If you use axios:
// import axios, { AxiosError } from "axios";

type AxiosApiResult<T> =
  | { ok: true; data: T }
  | { ok: false; error: { message: string; status?: number } };

async function axiosGet<T>(
  /* axiosInstance */ any,
  url: string,
): Promise<AxiosApiResult<T>> {
  try {
    const res = await /* axiosInstance */ Promise.resolve({
      data: undefined as unknown as T,
    });
    return { ok: true, data: res.data };
  } catch (err) {
    const e = err as any; // typically AxiosError
    return {
      ok: false,
      error: {
        message: e?.message ?? 'Request failed',
        status: e?.response?.status,
      },
    };
  }
}
```

### Example 3: typed query params

```ts
type UsersQuery = {
  page: number;
  pageSize: number;
  search?: string;
};

function toQueryString(q: UsersQuery) {
  const params = new URLSearchParams();
  params.set('page', String(q.page));
  params.set('pageSize', String(q.pageSize));
  if (q.search) params.set('search', q.search);
  return params.toString();
}
```

---

## Typed Hooks

Typed hooks hide complexity: components call a hook and get a typed result (data + status + errors).

### Example 1: `useAsync<T>` returning discriminated union state

```tsx
import * as React from 'react';

type AsyncState<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: string };

export function useAsync<T>(
  fn: () => Promise<T>,
  deps: React.DependencyList,
): AsyncState<T> {
  const [state, setState] = React.useState<AsyncState<T>>({ status: 'idle' });

  React.useEffect(() => {
    let cancelled = false;
    setState({ status: 'loading' });
    fn()
      .then((data) => {
        if (!cancelled) setState({ status: 'success', data });
      })
      .catch((e) => {
        const msg = e instanceof Error ? e.message : 'Unknown error';
        if (!cancelled) setState({ status: 'error', error: msg });
      });
    return () => {
      cancelled = true;
    };
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, deps);

  return state;
}
```

### Example 2: `useUser(userId)` built on a typed API

```tsx
type User = { id: string; name: string };

async function getUser(userId: string): Promise<User> {
  const res = await fetch(`/api/users/${userId}`);
  if (!res.ok) throw new Error('Failed');
  return (await res.json()) as User;
}

export function useUser(userId: string) {
  return useAsync(() => getUser(userId), [userId]);
}
```

### Example 3: typed event handlers inside hooks

```tsx
export function useInput(initial = '') {
  const [value, setValue] = React.useState(initial);
  const onChange: React.ChangeEventHandler<HTMLInputElement> = (e) =>
    setValue(e.target.value);
  return { value, onChange, setValue };
}
```

---

## API Response Modelling

Your UI and business logic should not have to guess what an API returned. Model responses as:

- Discriminated unions (`ok: true/false`)
- Or “problem details” objects
- Or data + metadata wrappers for pagination

### Example 1: success/failure envelope

```ts
type Problem = { title: string; detail?: string; status: number };
type Envelope<T> = { ok: true; data: T } | { ok: false; error: Problem };

type Paginated<T> = {
  items: T[];
  page: number;
  pageSize: number;
  total: number;
};

type User = { id: string; name: string };
type UsersResponse = Envelope<Paginated<User>>;
```

### Example 2: modelling partial/optional fields explicitly

```ts
// Example: backend may omit email for privacy
type UserPublic = { id: string; name: string; email?: never };
type UserPrivate = { id: string; name: string; email: string };

type UserDto = UserPublic | UserPrivate;

function displayEmail(u: UserDto) {
  return 'email' in u ? u.email : 'hidden';
}
```

### Example 3: map DTO -> domain model

```ts
type UserDto = { id: string; name: string; createdAt: string };
type User = { id: string; name: string; createdAt: Date };

function toUser(dto: UserDto): User {
  return { ...dto, createdAt: new Date(dto.createdAt) };
}
```

---

## Strict Mode

In TypeScript discussions, “strict mode” usually means enabling the `strict` family of compiler options. It’s one of the highest ROI moves for large codebases.

### Key flags (commonly relevant)

- `"strict": true`
- `"noImplicitAny": true`
- `"strictNullChecks": true`
- `"noUncheckedIndexedAccess": true`
- `"exactOptionalPropertyTypes": true`

### Example 1: `noUncheckedIndexedAccess` impact

```ts
const map: Record<string, number> = { a: 1 };
const x = map['missing']; // number | undefined with noUncheckedIndexedAccess
```

### Example 2: `exactOptionalPropertyTypes` pitfall

```ts
type U = { email?: string };

const u1: U = {}; // ok
const u2: U = { email: 'x' }; // ok
// const u3: U = { email: undefined }; // may be error with exactOptionalPropertyTypes
```

### Example 3: recommended approach for large apps

- Turn on `strict` early.
- If migrating, enable flags gradually and fix by module.
- Add lint rules to prevent `any` and unsafe assertions.

---

## Null Safety

Null safety is mostly `strictNullChecks` + patterns that keep `null | undefined` explicit and localized.

### Example 1: prefer `undefined` for “missing” (JS idiom)

```ts
type User = { email?: string };

function sendEmail(u: User) {
  if (!u.email) return; // handles undefined and empty string
  // u.email is string here
}
```

### Example 2: safe optional chaining + nullish coalescing

```ts
type ApiUser = { profile?: { displayName?: string } };

function label(u: ApiUser) {
  return u.profile?.displayName ?? 'Anonymous';
}
```

### Example 3: React props with nullable values

```tsx
type Props = { userId: string | null };

function Profile({ userId }: Props) {
  if (userId === null) return <div>Please select a user</div>;
  return <div>Showing {userId}</div>;
}
```

---

## Narrowing

Narrowing is how TypeScript refines a wider type into a more specific one using control flow.

### Narrowing techniques

- `typeof`, `instanceof`
- equality checks (`=== null`)
- `in` operator
- discriminated unions (`status`, `type` fields)
- user-defined type predicates (`x is Foo`)

### Example 1: `in` narrowing

```ts
type A = { kind: 'a'; a: number };
type B = { kind: 'b'; b: string };
type AB = A | B;

function f(x: AB) {
  if ('a' in x) {
    return x.a;
  }
  return x.b;
}
```

### Example 2: predicate narrowing for API data

```ts
type Problem = { title: string; status: number };
type Ok<T> = { ok: true; data: T };
type Err = { ok: false; error: Problem };
type Result<T> = Ok<T> | Err;

function isOk<T>(r: Result<T>): r is Ok<T> {
  return r.ok;
}

function handle<T>(r: Result<T>) {
  if (isOk(r)) return r.data;
  return r.error.title;
}
```

### Example 3: narrowing React event targets

```tsx
function onClick(e: React.MouseEvent) {
  const el = e.target;
  if (!(el instanceof HTMLButtonElement)) return;
  el.disabled = true;
}
```

---

## Exhaustive Checks

Exhaustive checks ensure every union variant is handled. This prevents future changes from silently breaking logic.

### Example 1: `never` helper

```ts
function assertNever(x: never): never {
  throw new Error(`Unexpected value: ${String(x)}`);
}

type Status =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: string };

function render(s: Status) {
  switch (s.status) {
    case 'idle':
      return 'Idle';
    case 'loading':
      return 'Loading';
    case 'success':
      return s.data;
    default:
      return assertNever(s);
  }
}
```

### Example 2: reducer exhaustive check

```ts
type Action =
  | { type: 'inc' }
  | { type: 'dec' }
  | { type: 'set'; value: number };

function reducer(state: number, action: Action) {
  switch (action.type) {
    case 'inc':
      return state + 1;
    case 'dec':
      return state - 1;
    case 'set':
      return action.value;
    default:
      return assertNever(action);
  }
}
```

### Example 3: enforce exhaustiveness with `satisfies`

```ts
type Route = 'home' | 'users' | 'settings';

const routeTitles = {
  home: 'Home',
  users: 'Users',
  settings: 'Settings',
} satisfies Record<Route, string>;

// If you add a new Route, TS forces you to update this object.
```

---

## Practical Questions (Interview-Style)

These prompts show up often because they force you to combine multiple topics (generics, unions, narrowing, strict null safety, reusable components, API modelling).

### 1) “Design a reusable typed table component”

**What interviewers want**:

- Can you tie `columns` to the row type `T`?
- Can you preserve inference so call sites are clean?
- Do you support custom rendering and stable keys?

#### Example A: minimal but strongly typed

```tsx
type Column<T> = {
  id: string;
  header: string;
  accessor: (row: T) => React.ReactNode;
};

function DataTable<T>(props: {
  rows: T[];
  columns: Column<T>[];
  rowKey: (row: T) => string;
}) {
  const { rows, columns, rowKey } = props;
  return (
    <table>
      <thead>
        <tr>
          {columns.map((c) => (
            <th key={c.id}>{c.header}</th>
          ))}
        </tr>
      </thead>
      <tbody>
        {rows.map((r) => (
          <tr key={rowKey(r)}>
            {columns.map((c) => (
              <td key={c.id}>{c.accessor(r)}</td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  );
}

type User = { id: string; name: string; age: number };
const cols: Column<User>[] = [
  { id: 'name', header: 'Name', accessor: (u) => u.name },
  { id: 'age', header: 'Age', accessor: (u) => u.age },
];
```

#### Example B: column keys using `keyof T` (when you want simple cells)

```ts
type KeyColumn<T> = {
  key: keyof T;
  header: string;
};

function valueCell<T extends object>(row: T, col: KeyColumn<T>) {
  return String(row[col.key]);
}
```

**Tradeoff**: `keyof T` is great for “simple tables” but you often still want `cell(row)` for computed formatting.

---

### 2) “How would you type dynamic forms?”

**What interviewers want**:

- Do you model form values as a type parameter `TValues`?
- Do field names become `keyof TValues`?
- Do you manage validation errors in a mapped type?

#### Example A: typed form state + generic update

```ts
type Errors<T> = Partial<Record<keyof T, string>>;

function setField<T extends object, K extends keyof T>(
  values: T,
  key: K,
  value: T[K],
): T {
  return { ...values, [key]: value };
}

type Profile = { name: string; age: number; email?: string };
let values: Profile = { name: '', age: 0 };
values = setField(values, 'age', 42);
// values = setField(values, "age", "nope"); // error
```

#### Example B: schema-driven fields with discriminated unions

```ts
type TextField = {
  kind: 'text';
  name: string;
  label: string;
  required?: boolean;
};
type NumberField = {
  kind: 'number';
  name: string;
  label: string;
  min?: number;
  max?: number;
};
type SelectField = {
  kind: 'select';
  name: string;
  label: string;
  options: string[];
};
type Field = TextField | NumberField | SelectField;

function renderField(field: Field) {
  switch (field.kind) {
    case 'text':
      return field.label;
    case 'number':
      return field.min;
    case 'select':
      return field.options.join(',');
  }
}
```

**Note**: Truly “dynamic forms” require runtime validation (Zod/Yup) if fields come from the backend. Types alone can’t prove runtime data.

---

### 3) “How do you avoid `any` in large apps?”

**Core strategies**:

1. Turn on **strict** TS options (`strict`, `noUncheckedIndexedAccess`, etc.).
2. Treat external inputs as **`unknown`**, not `any`, then parse/narrow.
3. Use **typed API wrappers** (`ApiResult<T>`) so UI never sees untyped data.
4. Prefer **generic helpers** over repeated casting.
5. Create shared types for “cross-cutting” concerns (pagination, errors, request state).
6. Use lint rules: `@typescript-eslint/no-explicit-any`, `no-unsafe-assignment`, etc.

#### Example A: `unknown` at boundary + parsing

```ts
async function getJsonUnknown(url: string): Promise<unknown> {
  const res = await fetch(url);
  return res.json();
}

type User = { id: string; name: string };

function parseUser(x: unknown): User {
  if (typeof x !== 'object' || x === null) throw new Error('bad');
  const o = x as any;
  if (typeof o.id !== 'string' || typeof o.name !== 'string')
    throw new Error('bad');
  return { id: o.id, name: o.name };
}

const raw = await getJsonUnknown('/api/users/1');
const user = parseUser(raw);
```

#### Example B: `satisfies` to avoid unsafe widening

```ts
type Routes = 'home' | 'users';

const routes = {
  home: '/',
  users: '/users',
} satisfies Record<Routes, string>;
```

#### Example C: type-safe wrapper for `Object.keys`

```ts
function typedKeys<T extends object>(obj: T): (keyof T)[] {
  return Object.keys(obj) as (keyof T)[];
}
```

---

## Closing Notes

- Use advanced types to model **constraints**, not to show off cleverness.
- Keep runtime validation in mind for anything coming from IO (APIs, localStorage, query params).
- Prefer discriminated unions for UI state and reducers; pair them with exhaustive checks.
