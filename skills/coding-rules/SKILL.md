---
name: coding-rules
description: Opinionated mandatory coding rules for TypeScript and React development. Use whenever writing, modifying, reviewing, or refactoring TypeScript or React code, including components, hooks, tests, and shared utilities. These rules override default coding instincts.
---

# Coding Rules

These rules are mandatory and override default coding instincts. The pre-flight checklist is the compact form; the sections below provide rationale and examples for rules that need judgment.

---

## Pre-flight checklist

Each bullet is the terse form of a rule. The fuller rationale and examples follow in the sections below.

<!-- BEGIN PREFLIGHT -->
**Code structure**
- **No one-use indirection**: do not create a local alias, wrapper function, extracted type, helper, component, hook, or module-level cache unless it has 2+ call sites, hides real complexity, or names a domain concept used elsewhere. Inline single-use `ref.current`, object properties, props, and derived constants.
- **Inline single-use `Props` and param types**: extract a type alias only if reused in 2+ places. Top-level and exported components are not exempt.
- **No nested ternaries**: one ternary is fine; 2+ levels become a named pure function above the component.
- **Positive conditions first** in `if/else` and 2-branch ternaries. Early-return guards are exempt.
- **Named-param objects** for any regular function, not a component or hook, with 2+ params. Name the object `params` and reach through it with `params.x`.
- **Early returns over nested conditions**: validate preconditions at the top; keep the happy path at the lowest indent.
- **No `switch`**: use `if` with early returns.
- **No IIFEs in components**: extract `(() => {...})()` as a named pure function above.
- **Curly braces on every block**: wrap all `if`, `else`, `for`, and `while` bodies in braces.

**React**
- **No `useCallback` or `useMemo`** unless there is a measured performance problem.
- **No destructuring hook returns, props, or function params**: use the whole object (`result.data`, `props.nav`, `params.queryTokens`). Tuple APIs such as `useState`, `useReducer`, and `Promise.all` are exempt.
- **Keep event handlers inside components**: define click, change, select, submit, hover, and similar React event handlers inside the component that owns the state and props they touch.
- **Prefer pure functions over components over hooks** when extracting shared logic.
- **No business logic in `useEffect`**: effects sync with external systems. Move logic into the handler that sets the value.
- **Hoist feature or experiment branching above existing components**: branch in a parent wrapper instead of weaving flags into component internals.

**TypeScript**
- **No `as` casts**: refactor so types flow naturally.
- **No non-null assertions**: use `=== undefined`, `=== null`, or optional chaining.
- **Use discriminated unions over runtime checks**: model variants with explicit discriminants instead of optional params, nullable fields, or inferred shape checks when the caller knows the variant.
- **No `eslint-disable`, `@ts-ignore`, or `@ts-expect-error`**: fix the root cause.
- **No default exports**: named exports only.

**Co-location**
- **Helpers travel with their only caller**: when extracting a sub-component, move helpers the parent no longer calls with it.
- **Use the narrowest variable scope**: declare values in the block that first reads them.
- **Extract cohesive UI clusters**: form fields, list rows, and conditional sub-views with their own helpers belong in their own component. Cohesion is the trigger, not size.
- **Derive at the point of use**: pass entities or IDs down and compute display values where consumed. Do not pre-compute in the parent unless the derived value is the reusable component's contract or is measured-expensive.

**Data safety**
- **No silent defaults** for money or any value whose absence is a programming bug. Throw or guard instead.

**Testing**
- **Fix tests instead of production code** unless there is a genuine production bug.
- **Keep tests in sync with code**: new behavior gets tests, changed behavior updates tests, moved code moves tests.

**Tooling**
- **Use `date-fns`, not `moment.js`**.
- **Never stage, commit, or push** unless explicitly asked.

**General**
- **No comments** unless the why is non-obvious: hidden constraint, subtle invariant, or external bug workaround.
- **Refactor copied code before using it**: treat copied code as a first draft.
<!-- END PREFLIGHT -->

---

## Code Structure

**No one-use indirection**: do not create a local alias, wrapper function, extracted type, helper, component, hook, or module-level cache unless it has 2+ call sites, hides real complexity, or names a domain concept used elsewhere. Inline directly at the call site. Prefer repetition over a spurious abstraction.

```tsx
// Don't
const handleChange = (value: string) => props.setName(value);
return <Input onChange={handleChange} />;

// Do
return <Input onChange={props.setName} />;
```

```tsx
// Don't
const isOwner = user.role === "owner";
if (isOwner) {
  return <OwnerTools />;
}

// Do
if (user.role === "owner") {
  return <OwnerTools />;
}
```

```tsx
// Don't
const input = ref.current;
if (input) {
  input.focus();
}

// Do
if (ref.current) {
  ref.current.focus();
}
```

```tsx
// Don't
const label = props.item.name.trim();
return <Text>{label}</Text>;

// Do
return <Text>{props.item.name.trim()}</Text>;
```

Extraction is allowed when it pays for itself: 2+ call sites, real complexity hidden behind a good name, or a domain concept that is used elsewhere. Do not extract a component, hook, helper, type, or module-level cache only because a value or block exists.

**Inline prop and param types**: extract a type alias only if the same shape appears in 2+ places. Top-level and exported components are not exempt.

```tsx
// Don't
type CheckoutProps = { cartId: string };
export const Checkout = (props: CheckoutProps) => {
  return <CheckoutScreen cartId={props.cartId} />;
};

// Do
export const Checkout = (props: { cartId: string }) => {
  return <CheckoutScreen cartId={props.cartId} />;
};
```

**Extract nested ternaries**: a single inline ternary is fine; two or more levels become a named pure function above the component.

**Positive conditions first**: when there is an `if/else` or 2-branch ternary, put the positive case first. Early-return guard clauses are exempt.

```tsx
// Don't
return props.value === null ? <Empty /> : <Display value={props.value} />;

// Do
return props.value !== null ? <Display value={props.value} /> : <Empty />;
```

**Named-param objects**: any regular function with 2+ params must use one `params` object. Components and hooks are exempt. Keep the object whole and read fields through `params.x`.

```ts
// Don't
function score(queryTokens: readonly string[], haystackText: string) {}

// Don't
function score({ queryTokens, haystackText }: { queryTokens: readonly string[]; haystackText: string }) {}

// Do
function score(params: { queryTokens: readonly string[]; haystackText: string }) {
  return params.queryTokens.every((token) => params.haystackText.includes(token));
}
```

**Early returns over nested conditions**: validate preconditions at the top and return immediately. Keep the happy path at the lowest indentation level.

```ts
// Don't
if (user) {
  if (user.active) {
    if (hasPermission) {
      return doWork();
    }
  }
}

// Do
if (!user) {
  return;
}
if (!user.active) {
  return;
}
if (!hasPermission) {
  return;
}
return doWork();
```

**No `switch` statements**: use `if` with early returns. Each branch should be self-contained and obvious.

**No IIFEs in components**: extract `(() => { ... })()` as a named pure function above the component.

**Curly braces on every block**: wrap all `if`, `else`, `for`, and `while` bodies in braces, even single-liners.

## React

**No `useCallback` or `useMemo` by default**: only add them for a specific, identifiable performance problem. Do not add them preemptively.

**No destructuring hook returns, props, or function params**: keep the whole object and reach through it. Namespaced access makes the value source clear at the read site.

```tsx
// Don't
const { data, loading } = useCustomer();
const CustomerCard = ({ customer }: { customer: Customer }) => {};

// Do
const result = useCustomer();
const CustomerCard = (props: { customer: Customer }) => {};
```

Tuple APIs whose contract is positional are exempt:

```tsx
const [value, setValue] = useState("");
const [users, posts] = await Promise.all([fetchUsers(), fetchPosts()]);
```

**Keep React event handlers inside components**: define handlers for click, change, select, submit, hover, keyboard, and similar UI events in the component that owns the state and props they read or update. Do not extract a handler into a pure function only to pass component state, setters, or props back into it. Extract only the domain computation inside the handler when it is reusable or meaningfully complex.

```tsx
// Don't
function handleNameChange(params: {
  value: string;
  setName: (value: string) => void;
}) {
  params.setName(params.value.trimStart());
}

function NameField(): React.ReactNode {
  const [name, setName] = useState("");

  return (
    <Input
      value={name}
      onChange={(event) =>
        handleNameChange({ value: event.target.value, setName })
      }
    />
  );
}

// Do
function NameField(): React.ReactNode {
  const [name, setName] = useState("");

  function handleChange(event: React.ChangeEvent<HTMLInputElement>) {
    setName(event.target.value.trimStart());
  }

  return <Input value={name} onChange={handleChange} />;
}
```

**Prefer pure functions over components over hooks** when extracting shared logic. Reach for a hook only when React lifecycle is genuinely required.

**No business logic in `useEffect`**: effects sync with external systems. If an effect runs only because a value changed that you set elsewhere, move the logic into the handler that sets it.

**Hoist feature and experiment branching to the top of the tree**: mount the new variant from a parent wrapper. Do not weave conditionals into the existing component internals.

```tsx
// Don't
return <PaymentForm variant={isExperiment ? "v2" : "legacy"} />;

// Do
return isExperiment ? <PaymentFormV2 /> : <PaymentForm />;
```

## TypeScript

**No `as` type casting**: refactor so types flow naturally.

**No non-null assertions**: use `=== undefined`, `=== null`, or optional chaining.

**Use discriminated unions over runtime checks**: when a value has variants and the caller knows which variant it is creating, encode the variant in the type with an explicit discriminant. Do not collapse variants into optional params, nullable fields, or inferred shape checks that force the callee to rediscover intent at runtime.

```ts
// Don't
function placeBand(params: {
  pageIndex: number;
  columnIndex?: number;
  type: "header" | "footer";
}) {
  if (params.columnIndex === undefined) {
    return `${params.type} for page ${params.pageIndex}`;
  }

  return `${params.type} for column ${params.columnIndex}`;
}

// Do
type PlaceBandParams = {
  pageIndex: number;
  type: "header" | "footer";
} & (
  | { scope: "page" }
  | { scope: "column"; columnIndex: number }
);

function placeBand(params: PlaceBandParams) {
  if (params.scope === "page") {
    return `${params.type} for page ${params.pageIndex}`;
  }

  return `${params.type} for column ${params.columnIndex}`;
}
```

Put shared fields outside the union and keep only variant-specific fields inside each branch: `{ shared: Shared } & ({ kind: "a"; a: A } | { kind: "b"; b: B })`.

**No `eslint-disable`, `@ts-ignore`, or `@ts-expect-error`**: fix the underlying issue.

**No default exports**: use named exports only.

## Co-location

Code that changes together should live together. Distance between related pieces forces the reader to scroll, search, and hold context that should be local.

**Helpers travel with their only caller**: when a pure function, constant, or type alias is used by exactly one component or cluster, it belongs in that component's scope or file. When extracting a sub-component, move every helper the parent no longer calls with it.

**Declare variables in the narrowest scope that uses them**: push declarations down to the block where they are first read. Top-of-function declarations are reserved for values that genuinely live across the whole body.

**Extract cohesive UI clusters into their own component**: form fields, list rows, and conditional sub-views with their own helpers and optional state belong in a child component. Cohesion is the trigger, not size, and state is not required.

**Derive at the point of use**: when a child needs values derived from an entity, pass the entity or ID and derive at the consumer. Do not pre-compute display values in the parent and thread them through props. Duplicating cheap derivation calls across consumers is better than centralizing them upstream.

Exceptions: if a derivation is measured-expensive, or the derived value is the contract of a reusable component such as `<Badge label={...} />`, passing the derived value is correct.

## Data Safety

**Never silently default money amounts**: do not use `?? 0` or numeric fallbacks for monetary values. A missing amount is a bug.

```ts
// Don't
return new Decimal(ctx.amount ?? 0).toFixed(2);

// Do
if (ctx.amount === undefined) {
  throw new Error("amount missing");
}
return new Decimal(ctx.amount).toFixed(2);
```

Apply the same rule to any value whose absence indicates a programming error.

## Testing

**Fix tests instead of production code unless there is a real bug**: when a test fails after an intentional code change, update the test to match the intended behavior. Touch production code only when the failure reveals a genuine production bug.

**Keep tests in sync with code changes**: new functionality gets new tests, modified behavior updates existing tests, and moved functionality moves the tests with it.

## Tooling

**Use `date-fns`, not `moment.js`**.

```ts
// Don't
import moment from "moment";
moment(date).format("YYYY-MM-DD");

// Do
import { format } from "date-fns";
format(date, "yyyy-MM-dd");
```

**Never stage, commit, or push** unless explicitly asked. The `/done` workflow counts as explicit permission.

## General

**Refactor copied code before using it**: treat copied code as a first draft and apply these rules before leaving it in the codebase.

**No comments by default**: add a comment only when the why is non-obvious, such as a hidden constraint, a subtle invariant, or a workaround for a specific external bug. Do not narrate what the code does or reference the current task.
