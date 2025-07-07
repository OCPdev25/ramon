---
description: Ultracite Rules
globs: "**/*.{ts,tsx,js,jsx}"
alwaysApply: true
---

# TypeScript Codebase Guidelines

This document contains comprehensive TypeScript coding standards that should be followed when generating or modifying code in this project. These rules ensure type safety, performance, maintainability, and consistency across the codebase.

## Core Principles

- **Type safety first**: Always prefer stricter types over loose ones
- **Immutability by default**: Use readonly properties unless mutation is required
- **Explicit over implicit**: Declare return types and avoid optional properties
- **Performance matters**: Follow patterns that optimize TypeScript compiler performance
- **Consistency**: Follow established naming conventions and patterns

## Naming Conventions

- Use kebab-case for file names (e.g., `my-component.ts`)
- Use camelCase for variables and function names (e.g., `myVariable`, `myFunction()`)
- Use UpperCamelCase (PascalCase) for classes, types, and interfaces (e.g., `MyClass`, `MyInterface`)
- Use ALL_CAPS for constants and enum values (e.g., `MAX_COUNT`, `Color.RED`)
- Inside generic types, functions or classes, prefix type parameters with `T` (e.g., `TKey`, `TValue`)

```ts
type RecordOfArrays<TItem> = Record<string, TItem[]>;
```

## Type System Rules

### 1. Return Types

When declaring functions on the top-level of a module,
declare their return types. This will help future AI
assistants understand the function's purpose.

```ts
const myFunc = (): string => {
  return "hello";
};
```

One exception to this is components which return JSX.
No need to declare the return type of a component,
as it is always JSX.

```tsx
const MyComponent = () => {
  return <div>Hello</div>;
};
```

### 2. Readonly Properties

Use `readonly` properties for object types by default. This will prevent accidental mutation at runtime.

Omit `readonly` only when the property is genuinely mutable.

```ts
// BAD
type User = {
  id: string;
};

const user: User = {
  id: "1",
};

user.id = "2";
```

```ts
// GOOD
type User = {
  readonly id: string;
};

const user: User = {
  id: "1",
};

user.id = "2"; // Error
```

### 3. Optional Properties

Use optional properties extremely sparingly. Only use them when the property is truly optional, and consider whether bugs may be caused by a failure to pass the property.

In the example below we always want to pass user ID to `AuthOptions`. This is because if we forget to pass it somewhere in the code base, it will cause our function to be not authenticated.

```ts
// BAD
type AuthOptions = {
  userId?: string;
};

const func = (options: AuthOptions) => {
  const userId = options.userId;
};
```

```ts
// GOOD
type AuthOptions = {
  userId: string | undefined;
};

const func = (options: AuthOptions) => {
  const userId = options.userId;
};
```

### 4. Discriminated Unions

Proactively use discriminated unions to model data that can be in one of a few different shapes.

For example, when sending events between environments:

```ts
type UserCreatedEvent = {
  type: "user.created";
  data: { id: string; email: string };
};

type UserDeletedEvent = {
  type: "user.deleted";
  data: { id: string };
};

type Event = UserCreatedEvent | UserDeletedEvent;
```

Use switch statements to handle the results of discriminated unions:

```ts
const handleEvent = (event: Event) => {
  switch (event.type) {
    case "user.created":
      console.log(event.data.email);
      break;
    case "user.deleted":
      console.log(event.data.id);
      break;
  }
};
```

Use discriminated unions to prevent the 'bag of optionals' problem.

For example, when describing a fetching state:

```ts
// BAD - allows impossible states
type FetchingState<TData> = {
  status: "idle" | "loading" | "success" | "error";
  data?: TData;
  error?: Error;
};

// GOOD - prevents impossible states
type FetchingState<TData> =
  | { status: "idle" }
  | { status: "loading" }
  | { status: "success"; data: TData }
  | { status: "error"; error: Error };
```

### 5. Interface Inheritance

ALWAYS prefer interfaces when modelling inheritance.

The `&` operator has terrible performance in TypeScript. Only use it where `interface extends` is not possible.

```ts
// BAD

type A = {
  a: string;
};

type B = {
  b: string;
};

type C = A & B;
```

```ts
// GOOD

interface A {
  a: string;
}

interface B {
  b: string;
}

interface C extends A, B {
  // Additional properties can be added here
}

Use `interface` with `extends` for inheritance. For other type definitions, prefer `type`.
```

### 6. Enums vs Const Objects

Do not introduce new enums into the codebase. Retain existing enums.

If you require enum-like behaviour, use an `as const` object:

```ts
const backendToFrontendEnum = {
  xs: "EXTRA_SMALL",
  sm: "SMALL",
  md: "MEDIUM",
} as const;

type LowerCaseEnum = keyof typeof backendToFrontendEnum; // "xs" | "sm" | "md"

type UpperCaseEnum = (typeof backendToFrontendEnum)[LowerCaseEnum]; // "EXTRA_SMALL" | "SMALL" | "MEDIUM"
```

Remember that numeric enums behave differently to string enums. Numeric enums produce a reverse mapping:

```ts
enum Direction {
  Up,
  Down,
  Left,
  Right,
}

const direction = Direction.Up; // 0
const directionName = Direction[0]; // "Up"
```

This means that the enum `Direction` above will have eight keys instead of four.

```ts
enum Direction {
  Up,
  Down,
  Left,
  Right,
}

Object.keys(Direction).length; // 8
```

## Consistency Preferences

### 1. Array Types

Use `Array<T>` over `T[]` for consistency across the codebase.

```ts
// GOOD
const users: Array<User> = [];
const nested: Array<Array<string>> = [];

// BAD
const users: User[] = [];
const nested: string[][] = [];
```

### 2. Object Types

Use `Record<string, T>` over `{ [key: string]: T }` for explicit intent and better composability.

```ts
// GOOD
type UserMap = Record<string, User>;
type NestedMap = Record<string, Array<User>>;

// BAD
type UserMap = { [key: string]: User };
type NestedMap = { [key: string]: Array<User> };
```

### 3. Function Declaration Style

Follow functional-light programming principles:

- Use arrow functions for pure functions and functional composition
- Use function declarations for top-level module functions
- Use classes when they provide clear composition advantages

```ts
// GOOD - Arrow functions for pure functions
const calculateTotal = (items: Array<Item>): number =>
  items.reduce((sum, item) => sum + item.price, 0);

// GOOD - Function declarations for top-level module functions
function createUserService(): UserService {
  return new UserService();
}

// GOOD - Classes when composition benefits are clear
class UserService {
  private users: Array<User> = [];

  addUser(user: User): void {
    this.users.push(user);
  }
}
```

## Import/Export Patterns

### 1. Import Organization

Separate type imports from value imports to prevent debugging issues and ensure clarity.

```ts
// GOOD - Clear separation
import type { User, Project } from "./schemas";
import { userSchema, projectSchema } from "./schemas";
```

```ts
// BAD - Mixed imports can cause confusion
import { userSchema, type User, projectSchema, type Project } from "./schemas";
```

**Why separate imports matter:**

- **Bundle analysis**: Easy to see what's in your runtime bundle vs compile-time
- **Refactoring safety**: Moving between type/value is just moving lines
- **Zero ambiguity**: Never have to think "is this a type or value?"
- **Tool compatibility**: Works reliably with all bundlers and analysis tools

Always use `import type` for type-only imports to ensure they're erased during transpilation.

### 2. Default Exports

Unless explicitly required by the framework, do not use default exports.

```ts
// BAD
export default function myFunction() {
  return <div>Hello</div>;
}
```

```ts
// GOOD
export function myFunction() {
  return <div>Hello</div>;
}
```

Default exports create confusion from the importing file.

```ts
// BAD
import myFunction from "./myFunction";
```

```ts
// GOOD
import { myFunction } from "./myFunction";
```

There are certain situations where a framework may require a default export. For instance, Next.js requires a default export for pages.

```tsx
// This is fine, if required by the framework
export default function MyPage() {
  return <div>Hello</div>;
}
```

## Error Handling

Think carefully before implementing code that throws errors.

If a thrown error produces a desirable outcome in the system, go for it. For instance, throwing a custom error inside a backend framework's request handler.

However, for code that you would need a manual try catch for, consider using a result type instead:

```ts
type Result<T, E extends Error> =
  | { ok: true; value: T }
  | { ok: false; error: E };
```

For example, when parsing JSON:

```ts
const parseJson = (input: string): Result<unknown, Error> => {
  try {
    return { ok: true, value: JSON.parse(input) };
  } catch (error) {
    return { ok: false, error: error as Error };
  }
};
```

This way you can handle the error in the caller:

```ts
const result = parseJson('{"name": "John"}');

if (result.ok) {
  console.log(result.value);
} else {
  console.error(result.error);
}
```

## Validation Patterns

### 1. Schema-First Development

Always define Zod schemas first, then derive types from them. This ensures runtime validation and type safety are always in sync.

```ts
import { z } from "zod";

// Define schemas first - these provide runtime validation
const UserSchema = z.object({
  id: z.string().uuid(),
  email: z.string().email(),
  name: z.string().min(1),
  age: z.number().int().positive(),
});

const AddressSchema = z.object({
  street: z.string().min(1),
  city: z.string().min(1),
  postcode: z.string().regex(/^[A-Z]{1,2}\d[A-Z\d]? ?\d[A-Z]{2}$/i),
});

// Derive types from schemas
type User = z.infer<typeof UserSchema>;
type Address = z.infer<typeof AddressSchema>;

// Schema composition for complex domains
const UserWithAddressSchema = UserSchema.extend({
  address: AddressSchema,
});

type UserWithAddress = z.infer<typeof UserWithAddressSchema>;
```

### 2. Validation Boundaries

Use Zod at external boundaries where data crosses trust boundaries. Use plain types for internal domain logic.

```ts
// GOOD - Use Zod at external boundaries
const parseApiResponse = (data: unknown): Result<User, ValidationError> => {
  const result = UserSchema.safeParse(data);
  if (!result.success) {
    return { ok: false, error: result.error };
  }
  return { ok: true, value: result.data };
};

// GOOD - Plain types for internal logic
const calculateUserScore = (user: User): number => {
  return user.age * 10; // Internal computation, no validation needed
};
```

**Use Zod for:**

- API request/response validation
- User input validation
- File parsing and configuration
- Database I/O boundaries
- Inter-service communication

**Use plain types for:**

- Internal domain logic
- Component props
- Pure computation functions
- Performance-critical paths

### 3. Type Flow Following

Let TypeScript's type narrowing guide your error handling flow. When validation succeeds, TypeScript automatically narrows the type.

```ts
const processUserData = (input: unknown): Result<ProcessedUser, Error> => {
  // At this point: input is unknown
  const parseResult = UserSchema.safeParse(input);
  if (!parseResult.success) {
    // TypeScript knows this is an error case
    return { ok: false, error: parseResult.error };
  }

  // TypeScript knows parseResult.data is User here
  const user = parseResult.data;

  // Follow the narrowed type through the function
  if (user.age < 18) {
    return { ok: false, error: new Error("User must be 18 or older") };
  }

  return { ok: true, value: { ...user, processed: true } };
};
```

## Type-Safe API Patterns

### 1. tRPC-Style Procedures

Design APIs using a tRPC-inspired pattern that works with any framework. Define schema-validated procedures that provide full type safety from client to server.

```ts
import { z } from "zod";

// Input/Output schemas
const CreateUserInputSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1),
  age: z.number().int().positive(),
});

const CreateUserOutputSchema = z.object({
  ok: z.boolean(),
  data: z
    .object({
      id: z.string().uuid(),
      email: z.string(),
      name: z.string(),
    })
    .optional(),
  error: z.string().optional(),
});

// Server-side procedure
export const createUser = async (
  input: unknown
): Promise<z.infer<typeof CreateUserOutputSchema>> => {
  const inputResult = CreateUserInputSchema.safeParse(input);
  if (!inputResult.success) {
    return { ok: false, error: "Invalid input" };
  }

  const userData = inputResult.data;

  // Business logic with validated data
  const result = await saveUserToDatabase(userData);
  if (!result.ok) {
    return { ok: false, error: result.error };
  }

  return { ok: true, data: result.value };
};

// Shared types for client
export type CreateUserInput = z.infer<typeof CreateUserInputSchema>;
export type CreateUserOutput = z.infer<typeof CreateUserOutputSchema>;
```

### 2. Framework Integration

This pattern works with any TypeScript framework:

```ts
// Next.js API route
export default async function handler(
  req: NextApiRequest,
  res: NextApiResponse
) {
  const result = await createUser(req.body);
  res.json(result);
}

// SvelteKit endpoint
export const POST = async ({ request }) => {
  const body = await request.json();
  const result = await createUser(body);
  return json(result);
};

// Remix action
export const action = async ({ request }) => {
  const formData = await request.formData();
  const result = await createUser(Object.fromEntries(formData));
  return json(result);
};
```

### 3. Client-Side Usage

Import types and use them for full type safety:

```ts
import type { CreateUserInput, CreateUserOutput } from "./api/users";

// Client knows exact input/output types
const createUser = async (
  userData: CreateUserInput
): Promise<CreateUserOutput> => {
  const response = await fetch("/api/users", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(userData),
  });

  return response.json();
};

// Usage with full type safety
const handleSubmit = async (formData: CreateUserInput) => {
  const result = await createUser(formData);

  if (result.ok) {
    // TypeScript knows result.data exists and has correct type
    console.log("Created user:", result.data);
  } else {
    // TypeScript knows result.error exists
    console.error("Error:", result.error);
  }
};
```

### 4. Schema Composition for Complex APIs

Build complex APIs by composing schemas:

```ts
// Base schemas
const PaginationSchema = z.object({
  page: z.number().int().positive().default(1),
  limit: z.number().int().min(1).max(100).default(10),
});

const SortSchema = z.object({
  field: z.enum(["name", "email", "createdAt"]),
  direction: z.enum(["asc", "desc"]).default("asc"),
});

// Composed input schema
const GetUsersInputSchema = z.object({
  filters: z
    .object({
      name: z.string().optional(),
      email: z.string().optional(),
    })
    .optional(),
  pagination: PaginationSchema,
  sort: SortSchema,
});

// Composed output schema
const GetUsersOutputSchema = z.object({
  ok: z.boolean(),
  data: z
    .object({
      users: z.array(UserSchema),
      totalCount: z.number(),
      hasMore: z.boolean(),
    })
    .optional(),
  error: z.string().optional(),
});
```

## Development Practices

### 1. Installing Libraries

When installing libraries, do not rely on your own training data.

Your training data has a cut-off date. You're probably not aware of all of the latest developments in the JavaScript and TypeScript world.

This means that instead of picking a version manually (via updating the `package.json` file), you should use a script to install the latest version of a library.

```bash
# pnpm
pnpm add -D @typescript-eslint/eslint-plugin

# yarn
yarn add -D @typescript-eslint/eslint-plugin

# npm
npm install --save-dev @typescript-eslint/eslint-plugin
```

This will ensure you're always using the latest version.

### 2. Generic Functions and Type Assertions

When building generic functions, you may need to use any inside the function body.

This is because TypeScript often cannot match your runtime logic to the logic done inside your types.

One example:

```ts
const youSayGoodbyeISayHello = <TInput extends "hello" | "goodbye">(
  input: TInput
): TInput extends "hello" ? "goodbye" : "hello" => {
  if (input === "goodbye") {
    return "hello"; // Error!
  } else {
    return "goodbye"; // Error!
  }
};
```

On the type level (and the runtime), this function returns `goodbye` when the input is `hello`.

There is no way to make this work concisely in TypeScript.

So using `any` is the most concise solution:

```ts
const youSayGoodbyeISayHello = <TInput extends "hello" | "goodbye">(
  input: TInput
): TInput extends "hello" ? "goodbye" : "hello" => {
  if (input === "goodbye") {
    return "hello" as any;
  } else {
    return "goodbye" as any;
  }
};
```

Outside of generic functions, use `any` extremely sparingly.

## TypeScript Configuration Notes

### noUncheckedIndexedAccess

If the user has this rule enabled in their `tsconfig.json`, indexing into objects and arrays will behave differently from how you expect.

```ts
const obj: Record<string, string> = {};

// With noUncheckedIndexedAccess, value will
// be `string | undefined`
// Without it, value will be `string`
const value = obj.key;
```

```ts
const arr: string[] = [];

// With noUncheckedIndexedAccess, value will
// be `string | undefined`
// Without it, value will be `string`
const value = arr[0];
```

## Quick Reference Checklist

When generating or reviewing TypeScript code, ensure:

- [ ] **File names** use kebab-case (e.g., `user-service.ts`)
- [ ] **Variables and functions** use camelCase (e.g., `getUserById`)
- [ ] **Types, interfaces, and classes** use PascalCase (e.g., `UserProfile`)
- [ ] **Constants and enum values** use ALL_CAPS (e.g., `MAX_RETRIES`)
- [ ] **Generic type parameters** are prefixed with T (e.g., `TData`, `TError`)
- [ ] **Return types** are declared for all module-level functions (except JSX components)
- [ ] **Properties** are marked as `readonly` by default
- [ ] **Optional properties** are avoided - use explicit `| undefined` instead
- [ ] **Discriminated unions** are used for multi-shape data instead of optional properties
- [ ] **Interface extends** is used instead of type intersections with `&`
- [ ] **Type vs interface** - use `type` by default, `interface` for inheritance
- [ ] **Array types** use `Array<T>` syntax for consistency
- [ ] **Object types** use `Record<string, T>` for key-value structures
- [ ] **Function style** follows functional-light principles (arrow functions for pure functions, function declarations for top-level)
- [ ] **Import separation** - types and values imported separately
- [ ] **Schema-first development** - define Zod schemas before types
- [ ] **Validation boundaries** - use Zod at external boundaries, plain types internally
- [ ] **Type flow following** - let TypeScript's type narrowing guide error handling
- [ ] **API procedures** - schema-validated inputs/outputs for all endpoints
- [ ] **Type sharing** - derive shared types from schemas for client-server communication
- [ ] **Const objects** with `as const` are used instead of new enums
- [ ] **Named exports** are used instead of default exports (unless framework requires)
- [ ] **Result types** are used instead of throwing in application code
- [ ] **Package manager commands** are used to install libraries, not manual edits
- [ ] **Type assertions** (`as any`) are only used inside generic function bodies when necessary

## Why These Rules Matter for LLMs

When an LLM (like Claude) works with your TypeScript codebase:

1. **Consistent Patterns**: Following these rules ensures generated code matches your existing codebase style
2. **Type Safety**: Explicit types and immutability patterns help prevent runtime errors
3. **Performance**: Using interface extends instead of type intersections improves IDE and compiler performance
4. **Maintainability**: Clear naming conventions and explicit types make code self-documenting
5. **Error Prevention**: Result types and discriminated unions prevent common bugs
6. **Runtime Validation**: Schema-first development with Zod ensures type safety at runtime boundaries
7. **Full-Stack Type Safety**: tRPC-style patterns provide end-to-end type safety without library lock-in
8. **Future-Proofing**: Using package managers ensures up-to-date dependencies

By following these guidelines, LLMs will generate TypeScript code that is:

- Type-safe and less prone to runtime errors
- Performant and optimized for the TypeScript compiler
- Consistent with your team's coding standards
- Self-documenting and easy to understand
- Maintainable and extensible
- Validated at runtime boundaries for external data
- Full-stack type-safe from client to server

- Don't use `accessKey` attribute on any HTML element.
- Don't set `aria-hidden="true"` on focusable elements.
- Don't add ARIA roles, states, and properties to elements that don't support them.
- Don't use distracting elements like `<marquee>` or `<blink>`.
- Only use the `scope` prop on `<th>` elements.
- Don't assign non-interactive ARIA roles to interactive HTML elements.
- Make sure label elements have text content and are associated with an input.
- Don't assign interactive ARIA roles to non-interactive HTML elements.
- Don't assign `tabIndex` to non-interactive HTML elements.
- Don't use positive integers for `tabIndex` property.
- Don't include "image", "picture", or "photo" in img `alt` prop.
- Don't use explicit role property that's the same as the implicit/default role.
- Make static elements with click handlers use a valid role attribute.
- Always include a `title` element for SVG elements.
- Give all elements requiring alt text meaningful information for screen readers.
- Make sure anchors have content that's accessible to screen readers.
- Assign `tabIndex` to non-interactive HTML elements with `aria-activedescendant`.
- Include all required ARIA attributes for elements with ARIA roles.
- Make sure ARIA properties are valid for the element's supported roles.
- Always include a `type` attribute for button elements.
- Make elements with interactive roles and handlers focusable.
- Give heading elements content that's accessible to screen readers (not hidden with `aria-hidden`).
- Always include a `lang` attribute on the html element.
- Always include a `title` attribute for iframe elements.
- Accompany `onClick` with at least one of: `onKeyUp`, `onKeyDown`, or `onKeyPress`.
- Accompany `onMouseOver`/`onMouseOut` with `onFocus`/`onBlur`.
- Include caption tracks for audio and video elements.
- Use semantic elements instead of role attributes in JSX.
- Make sure all anchors are valid and navigable.
- Ensure all ARIA properties (`aria-*`) are valid.
- Use valid, non-abstract ARIA roles for elements with ARIA roles.
- Use valid ARIA state and property values.
- Use valid values for the `autocomplete` attribute on input elements.
- Use correct ISO language/country codes for the `lang` attribute.
- Don't use consecutive spaces in regular expression literals.
- Don't use the `arguments` object.
- Don't use primitive type aliases or misleading types.
- Don't use the comma operator.
- Don't use empty type parameters in type aliases and interfaces.
- Don't write functions that exceed a given Cognitive Complexity score.
- Don't nest describe() blocks too deeply in test files.
- Don't use unnecessary boolean casts.
- Don't use unnecessary callbacks with flatMap.
- Use for...of statements instead of Array.forEach.
- Don't create classes that only have static members (like a static namespace).
- Don't use this and super in static contexts.
- Don't use unnecessary catch clauses.
- Don't use unnecessary constructors.
- Don't use unnecessary continue statements.
- Don't export empty modules that don't change anything.
- Don't use unnecessary escape sequences in regular expression literals.
- Don't use unnecessary fragments.
- Don't use unnecessary labels.
- Don't use unnecessary nested block statements.
- Don't rename imports, exports, and destructured assignments to the same name.
- Don't use unnecessary string or template literal concatenation.
- Don't use String.raw in template literals when there are no escape sequences.
- Don't use useless case statements in switch statements.
- Don't use ternary operators when simpler alternatives exist.
- Don't use useless `this` aliasing.
- Don't use any or unknown as type constraints.
- Don't initialize variables to undefined.
- Don't use void operators (they're not familiar).
- Use arrow functions instead of function expressions.
- Use Date.now() to get milliseconds since the Unix Epoch.
- Use .flatMap() instead of map().flat() when possible.
- Use literal property access instead of computed property access.
- Don't use parseInt() or Number.parseInt() when binary, octal, or hexadecimal literals work.
- Use concise optional chaining instead of chained logical expressions.
- Use regular expression literals instead of the RegExp constructor when possible.
- Don't use number literal object member names that aren't base 10 or use underscore separators.
- Remove redundant terms from logical expressions.
- Use while loops instead of for loops when you don't need initializer and update expressions.
- Don't pass children as props.
- Don't reassign const variables.
- Don't use constant expressions in conditions.
- Don't use `Math.min` and `Math.max` to clamp values when the result is constant.
- Don't return a value from a constructor.
- Don't use empty character classes in regular expression literals.
- Don't use empty destructuring patterns.
- Don't call global object properties as functions.
- Don't declare functions and vars that are accessible outside their block.
- Make sure builtins are correctly instantiated.
- Don't use super() incorrectly inside classes. Also check that super() is called in classes that extend other constructors.
- Don't use variables and function parameters before they're declared.
- Don't use 8 and 9 escape sequences in string literals.
- Don't use literal numbers that lose precision.
- Don't use the return value of React.render.
- Don't assign a value to itself.
- Don't return a value from a setter.
- Don't compare expressions that modify string case with non-compliant values.
- Don't use lexical declarations in switch clauses.
- Don't use variables that haven't been declared in the document.
- Don't write unreachable code.
- Make sure super() is called exactly once on every code path in a class constructor before this is accessed if the class has a superclass.
- Don't use control flow statements in finally blocks.
- Don't use optional chaining where undefined values aren't allowed.
- Don't have unused function parameters.
- Don't have unused imports.
- Don't have unused labels.
- Don't have unused private class members.
- Don't have unused variables.
- Make sure void (self-closing) elements don't have children.
- Don't return a value from a function that has a 'void' return type.
- Make sure all dependencies are correctly specified in React hooks.
- Make sure all React hooks are called from the top level of component functions.
- Use isNaN() when checking for NaN.
- Don't forget key props in iterators and collection literals.
- Make sure "for" loop update clauses move the counter in the right direction.
- Make sure typeof expressions are compared to valid values.
- Make sure generator functions contain yield.
- Don't use await inside loops.
- Don't use bitwise operators.
- Don't use expressions where the operation doesn't change the value.
- Don't destructure props inside JSX components in Solid projects.
- Make sure Promise-like statements are handled appropriately.
- Don't use **dirname and **filename in the global scope.
- Prevent import cycles.
- Don't define React components inside other components.
- Don't use event handlers on non-interactive elements.
- Don't assign to React component props.
- Don't use configured elements.
- Don't hardcode sensitive data like API keys and tokens.
- Don't let variable declarations shadow variables from outer scopes.
- Don't use the TypeScript directive @ts-ignore.
- Prevent duplicate polyfills from Polyfill.io.
- Don't use useless backreferences in regular expressions that always match empty strings.
- Don't use unnecessary escapes in string literals.
- Don't use useless undefined.
- Make sure getters and setters for the same property are next to each other in class and object definitions.
- Make sure object literals are declared consistently (defaults to explicit definitions).
- Use static Response methods instead of new Response() constructor when possible.
- Make sure switch-case statements are exhaustive.
- Make sure the `preconnect` attribute is used when using Google Fonts.
- Use `Array#{indexOf,lastIndexOf}()` instead of `Array#{findIndex,findLastIndex}()` when looking for the index of an item.
- Make sure iterable callbacks return consistent values.
- Use `with { type: "json" }` for JSON module imports.
- Use numeric separators in numeric literals.
- Use object spread instead of `Object.assign()` when constructing new objects.
- Always use the radix argument when using `parseInt()`.
- Make sure JSDoc comment lines start with a single asterisk, except for the first one.
- Include a description parameter for `Symbol()`.
- Don't use spread (`...`) syntax on accumulators.
- Don't use the `delete` operator.
- Don't access namespace imports dynamically.
- Don't use `<img>` elements in Next.js projects.
- Don't use namespace imports.
- Declare regex literals at the top level.
- Don't use `target="_blank"` without `rel="noopener"`.
- Don't use dangerous JSX props.
- Don't use both `children` and `dangerouslySetInnerHTML` props on the same element.
- Don't use global `eval()`.
- Don't use callbacks in asynchronous tests and hooks.
- Don't use TypeScript enums.
- Don't export imported variables.
- Don't use `<head>` elements in Next.js projects.
- Don't add type annotations to variables, parameters, and class properties that are initialized with literal expressions.
- Don't use TypeScript namespaces.
- Don't use negation in `if` statements that have `else` clauses.
- Don't use nested ternary expressions.
- Don't use non-null assertions with the `!` postfix operator.
- Don't reassign function parameters.
- Don't use parameter properties in class constructors.
- This rule lets you specify global variable names you don't want to use in your application.
- Don't use specified modules when loaded by import or require.
- Don't use user-defined types.
- Don't use constants whose value is the upper-case version of their name.
- Use `String.slice()` instead of `String.substr()` and `String.substring()`.
- Don't use template literals if you don't need interpolation or special-character handling.
- Don't use `else` blocks when the `if` block breaks early.
- Don't use yoda expressions.
- Don't use Array constructors.
- Use `as const` instead of literal types and type annotations.
- Use `at()` instead of integer index access.
- Follow curly brace conventions.
- Use `else if` instead of nested `if` statements in `else` clauses.
- Use single `if` statements instead of nested `if` clauses.
- Use either `T[]` or `Array<T>` consistently.
- Use `new` for all builtins except `String`, `Number`, and `Boolean`.
- Use consistent accessibility modifiers on class properties and methods.
- Use `const` declarations for variables that are only assigned once.
- Put default function parameters and optional function parameters last.
- Include a `default` clause in switch statements.
- Initialize each enum member value explicitly.
- Use the `**` operator instead of `Math.pow`.
- Use `export type` for types.
- Use `for-of` loops when you need the index to extract an item from the iterated array.
- Use `<>...</>` instead of `<Fragment>...</Fragment>`.
- Use `import type` for types.
- Make sure all enum members are literal values.
- Use `node:assert/strict` over `node:assert`.
- Use the `node:` protocol for Node.js builtin modules.
- Use Number properties instead of global ones.
- Don't add extra closing tags for components without children.
- Use assignment operator shorthand where possible.
- Use function types instead of object types with call signatures.
- Use template literals over string concatenation.
- Use `new` when throwing an error.
- Don't throw non-Error values.
- Use `String.trimStart()` and `String.trimEnd()` over `String.trimLeft()` and `String.trimRight()`.
- Use standard constants instead of approximated literals.
- Don't use Array index in keys.
- Don't assign values in expressions.
- Don't use async functions as Promise executors.
- Don't reassign exceptions in catch clauses.
- Don't reassign class members.
- Don't insert comments as text nodes.
- Don't compare against -0.
- Don't use labeled statements that aren't loops.
- Don't use void type outside of generic or return types.
- Don't use console.
- Don't use TypeScript const enum.
- Don't use control characters and escape sequences that match control characters in regular expression literals.
- Don't use debugger.
- Don't assign directly to document.cookie.
- Don't import next/document outside of pages/\_document.jsx in Next.js projects.
- Use `===` and `!==`.
- Don't use duplicate case labels.
- Don't use duplicate class members.
- Don't use duplicate conditions in if-else-if chains.
- Don't assign JSX properties multiple times.
- Don't use two keys with the same name inside objects.
- Don't use duplicate function parameter names.
- Don't have duplicate hooks in describe blocks.
- Don't use empty block statements and static blocks.
- Don't declare empty interfaces.
- Don't let variables evolve into any type through reassignments.
- Don't use the any type.
- Don't use export or module.exports in test files.
- Don't misuse the non-null assertion operator (!) in TypeScript files.
- Don't let switch clauses fall through.
- Don't use focused tests.
- Don't reassign function declarations.
- Don't allow assignments to native objects and read-only global variables.
- Use Number.isFinite instead of global isFinite.
- Use Number.isNaN instead of global isNaN.
- Don't use the next/head module in pages/\_document.js on Next.js projects.
- Don't use implicit any type on variable declarations.
- Don't assign to imported bindings.
- Don't use irregular whitespace characters.
- Don't use labels that share a name with a variable.
- Don't use characters made with multiple code points in character class syntax.
- Make sure to use new and constructor properly.
- Make sure the assertion function, like expect, is placed inside an it() function call.
- Don't use shorthand assign when the variable appears on both sides.
- Don't use octal escape sequences in string literals.
- Don't use Object.prototype builtins directly.
- Don't redeclare variables, functions, classes, and types in the same scope.
- Don't have redundant "use strict".
- Don't compare things where both sides are exactly the same.
- Don't let identifiers shadow restricted names.
- Don't use disabled tests.
- Don't use sparse arrays (arrays with holes).
- Watch out for possible "wrong" semicolons inside JSX elements.
- Don't use template literal placeholder syntax in regular strings.
- Don't use the then property.
- Don't merge interfaces and classes unsafely.
- Don't use unsafe negation.
- Don't use var.
- Don't use with statements in non-strict contexts.
- Don't use overload signatures that aren't next to each other.
- Make sure async functions actually use await.
- Make sure default clauses in switch statements come last.
- Make sure to pass a message value when creating a built-in error.
- Make sure get methods always return a value.
- Use a recommended display strategy with Google Fonts.
- Make sure for-in loops include an if statement.
- Use Array.isArray() instead of instanceof Array.
- Use the namespace keyword instead of the module keyword to declare TypeScript namespaces.
- Make sure to use the digits argument with Number#toFixed().
- Make sure to use the "use strict" directive in script files.
