# JavaScript-Specific Agent Configuration

Include this alongside other agent docs when working on JavaScript/TypeScript projects.

## Running Tests
```
npx vitest run path/to/test.ts
npx vitest run path/to/test.ts --testNamePattern="description of test"
```

## Linting
```
npx eslint path/to/changed_file.ts
npx prettier --check path/to/changed_file.ts
```
Follow the project's ESLint and Prettier configurations.

## Type Checking
```
npx tsc --noEmit
```
Ensure no type errors are introduced by the change.

## Testing Rules
- Prefer `vi.mock` only for external modules and HTTP boundaries.
- Use `msw` (Mock Service Worker) or similar for API mocking when available.
- Avoid snapshot tests for dynamic content; prefer explicit assertions.
- Test user-facing behavior, not implementation details.
- For React/Vue components, prefer testing library queries (`getByRole`, `getByText`) over test IDs or CSS selectors.

## Plan Mode Triggers
In addition to the general triggers, use plan mode when:
- State management changes are needed (stores, context, signals)
- Build configuration or bundler changes are needed
- Shared component API changes that affect multiple consumers
- Route structure changes

## Code Review Extras
In addition to the general review checklist, check for:
- Bundle size impact of new dependencies
- Unnecessary re-renders in component-based frameworks
- Missing error boundaries or loading states
- Unhandled promise rejections and missing error handling in async code
- Memory leaks from event listeners, subscriptions, or timers not cleaned up
- Accessibility (semantic HTML, ARIA attributes, keyboard navigation)
- Missing `key` props in lists (React/Vue)
